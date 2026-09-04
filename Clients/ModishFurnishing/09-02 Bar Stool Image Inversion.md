---
client: Modish Furnishing
date: 2026-09-02
type: dev-change
theme: theme_export__modishfurnishingcoukpdpredoconversiondude__02SEP20260606pm
---

# Bar stool image inversion (collection card <-> PDP gallery)

## Ask

Swap which bar stool image leads where:

- **Collection page (`/collections/bar-stools`)** should show the white-background
  packshot - the image that currently sits at position 1 of the PDP media gallery.
- **PDP** should lead with the **lifestyle** image, not the white-background one.

## What was there before

The PDP gallery already had a bar-stool customisation: it *pinned* the media whose
alt text is `collection thumbnail` (the white-background packshot) to position 1,
while the collection cards used the merchant's own media order and therefore showed
the lifestyle shot. The two were exactly the wrong way round.

## What changed

Two files, display order only - `product.media` is never modified, so nothing has to
be re-sorted in the Shopify admin.

### `snippets/card-gallery.liquid` (collection / search / recommendation cards)

For bar stools, the `collection thumbnail` packshot is moved to the front of the card
gallery, keeping the rest of the order intact. Hover-for-second-image now reveals the
lifestyle shot. The pin is skipped when the packshot belongs to a variant other than
the selected one, because the theme's existing variant rules would hide it and leave
a blank card.

### `blocks/_product-media-gallery.liquid` (PDP gallery)

The old "pin packshot first" logic is inverted: when the packshot lands at position 1
it is demoted to position 2, so the lifestyle shot leads and the packshot is still the
second thing a visitor sees. Products whose packshot already sits further down are
left alone.

Both files now use the same bar-stool test (`snippets/pdp-redo-is-barstool.liquid`)
instead of the PDP's old inline collection-handle check, so the card and the PDP can
never disagree about which products get the treatment.

## Depends on

The alt text convention: the white-background packshot must have alt text
`collection thumbnail` (case/whitespace insensitive). A bar stool without it keeps the
merchant's order everywhere - which is the safe fallback, not a broken card.

## Verification

- Shopify `theme-check` on the whole theme: no new offences in either edited file.
- Reorder logic run against a Liquid engine with 12 cases (packshot first / last /
  absent, 2-image products, non-bar-stools, variant-attached packshots): all pass.
- Not yet verified in a live preview - needs the theme uploaded and eyeballed on
  `/collections/bar-stools` plus a couple of PDPs.

## Files

- `theme-changes/snippets/card-gallery.liquid`
- `theme-changes/blocks/_product-media-gallery.liquid`
- `theme-changes/bar-stool-image-inversion.diff`
