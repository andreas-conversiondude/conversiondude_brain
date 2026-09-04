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
