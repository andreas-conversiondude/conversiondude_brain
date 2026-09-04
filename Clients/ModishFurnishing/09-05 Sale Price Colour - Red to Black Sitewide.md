# Sale price colour: red to black, sitewide

Date: 2026-09-04
Client: Modish Furnishing

## Ask

The discounted ("now") price rendered red everywhere. Make it the same black as
a regular price, keep the struck-through original grey, leave every other red
alone, and fix it at the source rather than per template.

## Approach

Fixed at the source. There is no theme setting for the sale price colour -
`config/settings_schema.json` only has `badge_sale_color_scheme`, which is the
sale badge, not the price - so the source is the CSS rule the price markup
already carries.

Every price on the site renders through `snippets/price.liquid`, which puts
`price price--on-sale` on the discounted figure. One rule in `assets/base.css`
coloured that class red, so one rule covers the sale page, all collection
pages, the PDP, search results, product cards, upsells and the cart drawer.

Two components draw their own price markup and had the red hard-coded a second
time; both are changed the same way:

- `snippets/sticky-add-to-cart.liquid` - the mobile sticky bar on the PDP
- `snippets/cart-products.liquid` - the per-unit "each" price on cart lines
  with quantity above one

## What changed

`color: inherit` in all three places, not a black hex. `.price` carries no
colour of its own - it inherits the section's foreground - so inheriting is
what makes the sale price and the regular price identical, including in any
section that runs a different colour scheme. A hex would match on white and
break on a dark background.

Untouched: `.compare-at-price` (grey, line-through, 0.4 opacity), the sale
badge, the sale code chip, and every error state.

## Files

- `assets/base.css` - `.price--on-sale`
- `snippets/sticky-add-to-cart.liquid` - `.sticky-add-to-cart__price--sale`
- `snippets/cart-products.liquid` - `.cart-items__unit-price--sale .cart-items__unit-price-current`

Theme-check clean.
