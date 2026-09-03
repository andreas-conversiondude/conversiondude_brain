---
client: Modish Furnishing
date: 2026-09-03
type: dev-change
theme: theme_export__modishfurnishingcoukpdpredoconversiondude__02SEP20260606pm
---

# Customer photos section: default product template + video support

## Ask

1. Add the "PDP Redo customer photos" section to the **default** product template too,
   underneath the "You may also like" section.
2. Make the section render **videos** - selecting videos on the metafield showed nothing.

## Why videos did not show

The section gated every entry on `photo.width` and rendered it with `image_tag`. A
`file_reference` list hands back a different Liquid object per file type, and videos
have no `width` - so every video was counted as zero and skipped. Only images could
ever appear.

## What changed

### New: `snippets/pdp-redo-customer-media-kind.liquid`

Classifies one metafield entry by what the object actually responds to, and echoes:

- `image` - has width/height (render with `image_tag`)
- `video` - has `sources`, i.e. picked from Content > Files (render with `video_tag`)
- `file_video` - a generic file whose URL ends `.mp4 / .mov / .webm / .m4v`
- `""` - anything else (a stray PDF) - skipped, as before

### `sections/pdp-redo-customer-photos.liquid`

- Counting pass and render pass both use the classifier, so a product with **only**
  videos now shows the section instead of hiding it.
- Videos render in the same 1:1 crop as the photos, so the strip still reads as one line.
- Playback: `preload="none"`, muted, looped, no controls; an IntersectionObserver starts
  a video when its tile scrolls into view and pauses it on the way out - nothing
  downloads on page load. Same approach as the influencer videos section.
- `prefers-reduced-motion`, no IntersectionObserver, or a refused autoplay all fall back
  to native controls rather than leaving a dead tile.
- Small play glyph overlaid (`snippets/pdp-redo-customer-photos-play.liquid`), fading out
  while the video plays.

### `templates/product.json`

Added a `pdp-redo-customer-photos` section instance at the end of the section order, so it
sits below the Inflate recommendation/carousel widgets at the bottom of the default PDP.

## Open question

There is no section literally named "You may also like" in this template - the PDP-redo
comment notes the photos section replaced "Shop The Full Collection". The bottom of the
default template is: Shop The Full Collection carousel -> Inflate product app block ->
Inflate reviews widget -> Inflate product carousel. The customer photos section was put
**after all of them**, which reads as "underneath" whichever of the Inflate widgets is the
"You may also like". If it needs to sit directly under a specific one, it is a drag in the
theme editor.

## Verification

- Shopify `theme-check` on the whole theme: no new offences in any changed file
  (the four remaining `JSONMissingBlock` notices on `product.json` are pre-existing app
  blocks that are not part of a theme export).
- Classifier and counting logic run against a Liquid engine with 10 cases (image, Shopify
  video, generic mp4/mov, PDF, empty object, and mixed / video-only / image-only /
  PDF-only lists): all pass.
- `templates/product.json` verified byte-for-byte identical to the export apart from the
  added section.
- Not yet checked in a live preview - needs the theme uploaded, a product with a video on
  `custom.customer_photos`, and a look at a default-template PDP.

## Files

- `theme-changes/sections/pdp-redo-customer-photos.liquid`
- `theme-changes/snippets/pdp-redo-customer-media-kind.liquid`
- `theme-changes/snippets/pdp-redo-customer-photos-play.liquid`
- `theme-changes/templates/product.json`
- `theme-changes/customer-photos-videos.diff`
