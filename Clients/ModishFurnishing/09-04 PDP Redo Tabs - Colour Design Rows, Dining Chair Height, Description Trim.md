---
client: Modish Furnishing
date: 2026-09-04
type: dev-change
scope: product.pdp-redo template ONLY
---

# PDP Redo tabs: Colour/Design rows, dining chair height, trimmed Description

Scope confirmed before touching anything: `pdp-redo-specs`, `pdp-redo-tabs`,
`pdp-redo-spec-value` and `pdp-redo-seat-fit` are referenced only by
`templates/product.pdp-redo.json`. The default `templates/product.json` is not
touched by this change.

## Task 1 - Colour and Design rows

New `snippets/pdp-redo-description-label.liquid` reads a "Label: value" line out
of the description and returns the text after the label up to the end of the
line. Case-insensitive, accepts Colour and Color, bold tags and whitespace
stripped. `pdp-redo-spec-value.liquid` gained `colour` and `design` fields
(metafield `custom.colour` / `custom.color` / `custom.design` still wins if set),
and the rows render between Depth and Fabric.

Key detail: line boundaries (`<br>`, `</p>`, `</li>`, `</h*>`, real newlines) are
replaced with a sentinel BEFORE `strip_html`. strip_html alone removes those tags
without leaving anything behind, so `Colour: Cream</p><p>Design: Curved` collapses
into one line and Colour swallows the Design line. The label is located on a
lower-cased copy and the value sliced out of the original, so casing survives.

Row order: Seat height, Total height, Width, Depth, Colour, Design, Fabric,
Frame, Assembly. Same `<th>`/`<td>` markup as the existing rows, so the layout
and divider lines are unchanged; long Design values wrap.

## Task 2 - dining chairs show Total height instead of Width

New `snippets/pdp-redo-is-dining-chair.liquid`. Condition (ANY of, AND not a bar
stool):

1. `product.type` handleizes to `dining-chair(s)`, `diningchair(s)`,
   `kitchen-chair(s)`
2. product is in the `dining-chairs` collection
3. product has tag `dining-chairs`, `dining-chair` or `dining chair`
4. product title contains "dining chair"

Bar stools are excluded outright via `pdp-redo-is-barstool.liquid`, so a product
tagged for both groups keeps the bar stool rows.

The block clears the width VALUE rather than skipping the `<tr>`, so the
"hide the whole table when every value is blank" guard stays honest.

## Task 3 - Description tab shows intro copy only

New `snippets/pdp-redo-description-intro.liquid` cuts the description at the
first "Dimensions:" (case-insensitive, wherever it sits: bare text, `<strong>`,
`<b>`, `<h1>`-`<h6>`). No "Dimensions:" heading = description rendered as is.
The cut lands inside the heading's markup, so the snippet peels off the dangling
opening tag, drops the empty wrapper it sat in, and closes an unbalanced
paragraph.

The Description tab's custom-liquid setting in `templates/product.pdp-redo.json`
is now just:

    {% render 'pdp-redo-description-intro', product: product %}

All server side. The product description in Shopify admin is untouched - Task 1
parses the full text and other blocks still use it.

## Files

- `snippets/pdp-redo-description-label.liquid` (new)
- `snippets/pdp-redo-description-intro.liquid` (new)
- `snippets/pdp-redo-is-dining-chair.liquid` (new)
- `snippets/pdp-redo-spec-value.liquid` (modified)
- `blocks/pdp-redo-specs.liquid` (modified)
- `templates/product.pdp-redo.json` (modified - one custom-liquid setting)

## Verification

- Shopify `theme-check`: no syntax offences. One `OrphanedSnippet` warning on
  pdp-redo-description-intro is a false positive - theme-check does not scan
  render calls inside a template's custom-liquid setting.
- Label parsing run against a Liquid engine, 6 description shapes (strong+br,
  h3 + lowercase labels, `<b>` + American spelling, no Dimensions heading, no
  Colour/Design labels, everything on one line): all correct.
- Description cut run against the same 6 shapes: intro only, markup balanced.
- Dining chair detection, 7 cases (type, collection, tag, title, bar stool by
  collection, product tagged BOTH ways, sofa): all correct.
- Full spec table rendered for a bar stool, a dining chair, a dining chair with
  no Colour/Design labels, and a product with the Colour metafield set: rows,
  order and the Width/Total height swap all as specified.
- Not yet checked in a live preview - needs the theme uploaded.

## Follow-up fixes (same day)

1. **Colour rendered as "[ColorDrop]".** `custom.color` on this store is a
   **Color**-type metafield, so `.value` is a ColorDrop object and printing it
   gives the literal "[ColorDrop]". The Colour row no longer reads any
   metafield - it is always the parsed "Colour:" line, like Design.
2. **One line per row.** Parsed text values (colour, design, fabric, frame,
   assembly) are cut at the first comma or full stop, whichever comes first,
   punctuation dropped, then capped at 60 characters with an ellipsis as a
   backstop. Numeric rows are excluded so a decimal like 55.5 is not cut at its
   own point. The truncation sits outside the fallback branch: colour and design
   resolve earlier, so nesting it there meant it never ran for them.
3. **Label column wrapping.** The table is `table-layout: fixed` with the label
   column at 38% (32% from 750px) and `white-space: nowrap`, so "Seat height"
   and "Total height" stay on one line. Values are `nowrap` +
   `text-overflow: ellipsis` and stay right-aligned; the full text is kept in
   each cell's title attribute.

Verified against a Liquid engine: ColorDrop metafield ignored, "Curved
backrest. Semi-circular base, added stability" -> "Curved backrest", "Versatile
design ... interiors, from contemporary to transitional" -> "Versatile design
that complements a variety of interiors", decimals intact, numeric rows
untouched.

## Badge row: dining chairs show total height (same day)

`blocks/pdp-redo-description-review.liquid` builds the three-badge row
(material | measurement | colour). A dining-chair branch now runs after every
existing size rule and before the manual handle override:

- value comes from `pdp-redo-spec-value` field `total_height` - the same source
  as the Details & Dimensions "Total height" row, so the two can never drift
- formatted as "86cm High", matching "54cm Wide"
- gated by `pdp-redo-is-dining-chair.liquid`, which excludes anything passing
  the bar-stool test, so bar stools and every other type keep their badge
- no total height resolves -> badge_size keeps whatever the existing rules
  produced (the width badge), never an empty badge

The default template's `product-description-review.liquid` is untouched.

Verified against a Liquid engine, 7 cases: dining chair by type / collection /
tag / title all render "86cm High"; a dining chair with no dimension block keeps
"54cm Wide"; bar stool and sofa keep "54cm Wide".

## Colour row still blank on the live PDP (same day)

After removing the ColorDrop metafield read, the Colour row disappeared instead
of showing a value: the description parse was not matching the real markup. The
"[ColorDrop]" text had been masking that all along, since it came from the
metafield, not the parse.

`snippets/pdp-redo-description-label.liquid` now normalises before searching:

- `&nbsp;` entity and literal U+00A0 both become a normal space
- a space in front of the colon is dropped, so "Colour :" and "Colour&nbsp;:"
  collapse to "Colour:"
- a label whose value sits on the NEXT line (own paragraph or after a `<br>`)
  falls through to the first non-empty line instead of returning nothing

`pdp-redo-spec-value.liquid` also accepts "Colours:" and "Colors:" for the
Colour row. The Design row is untouched - it shares the same parser, so it
benefits from the normalisation, but no design-specific logic or alias changed.

Verified against 12 markup shapes: plain label, entity nbsp, literal nbsp, plain
space before colon, colon outside the bold tag, value on the next line, label in
its own paragraph, American spelling, plural label, list-item markup, nbsp
inside the value, and no label at all (correctly blank).

## Stray line under the Description copy (same day)

The Description tab ended with a short vertical black line below the last line
of text. That is markup with no text in it - the empty element the cut left
behind, or one that was already sitting at the end of the description - still
carrying the theme's border or padding.

`snippets/pdp-redo-description-intro.liquid` no longer works from a list of tag
names. It now sweeps the end of the intro and drops anything that renders
nothing:

- a tag that was opened and never filled (`<p>`, `<span style=...>`, `<br />`)
- an element that closes with no text in it - the closing tag is matched back to
  the last opener of the same name and the element between them is checked with
  `strip_html`, so an empty paragraph, blockquote or whole table shell goes,
  however deeply nested

Each pass removes one element, so nested shells unwind on their own. Media is
left alone: an intro may end on an image. The sweep now runs on every
description, not only ones that were cut at "Dimensions:", so a stray empty
element that was always in the description is removed too.

Verified against 20 shapes, including an empty paragraph, an nbsp-only
paragraph, a blockquote shell, a table shell, trailing `<hr>` and `<br>`,
stacked empty spans, and the same cases with no "Dimensions:" heading at all.
Theme-check clean.

## The stray line was the "|" separator (same day)

The first sweep did not remove it because it is not empty markup - it is a
literal pipe character. Every description in this shop carries a "|" between the
intro copy and the dimensions block, usually as `<span>|</span>` or `<p>|</p>`.
The old accordion blocks still in the template split the description on exactly
that marker, which is what it is there for. Rendered on its own it draws a short
vertical line.

`pdp-redo-description-intro.liquid` now counts "|" as blank alongside the hard
spaces, so the pipe goes and the empty paragraph or span left around it goes
with it on the next pass.

Verified against 7 more shapes: pipe in a span, in its own paragraph, in a
`<strong>`, in an `<h3>`, padded with `&nbsp;`, bare between blocks, and with no
"Dimensions:" heading at all.

## Design row removed (same day)

`blocks/pdp-redo-specs.liquid` no longer renders the Design row. The capture,
the strip, the row_count value and the `<tr>` are all gone. Colour stays and
still comes from the description parse. `pdp-redo-spec-value.liquid` keeps its
`design` field - nothing calls it now, but the parser is shared and a future row
can use it.
