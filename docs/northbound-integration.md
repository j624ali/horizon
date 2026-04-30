# Northbound integration

How the Northbound app integrates with this theme. Read this when touching pricing surfaces, cart, ATC buttons, community selector, or shipping display.

Northbound is **not** a per-section `@app` block. It's a global app-embed plus a DOM-attribute contract that the theme has to honor.

## App embed (one block, body-level)

The Northbound app extension exposes a single block: `community_selector.liquid` with `"target": "body"`. The merchant enables it once in **Theme editor → App embeds**. Its job is minimal:

- Emits one bootstrap node: `<div data-northbound-selector-root>`
- Loads six deferred storefront JS files (runtime, UI, DOM, pricing, controller, entry)

That's it. No per-section blocks, no inline pricing UI. The runtime then takes over and operates entirely against attribute hooks the **theme** renders.

## App proxy endpoints

- **Bootstrap** (`GET /apps/northbound/bootstrap`): community list, selected community, shop config. Called once on page load.
- **Prices** (`POST /apps/northbound/prices`): community-adjusted prices for a set of variant IDs. Called when community changes or new variant nodes appear.
- **Carrier service** (`POST /api/carrier-service`): Shopify calls this at checkout for real shipping rates. Not directly theme-related.

## DOM contract — what the theme must emit

The runtime queries these selectors after bootstrap. **If the theme doesn't render them, Northbound has nothing to mutate.**

### Selector UI mount point

Renders the community selector once available — usually in the header.

```html
<div data-northbound-location-slot></div>
```

The runtime will populate every match, so multiple slots are fine (e.g., a header slot and a fallback in-page slot).

### Price surface

Every place a price appears — product cards, PDP, recommended rows, search results.

```html
<span data-northbound-price-surface
      data-northbound-variant-id="{{ variant.id }}"
      data-northbound-base-price-cents="{{ price }}"
      data-northbound-base-compare-cents="{{ compare_at_price | default: 0 }}">
  <span data-northbound-price-display>{{ price | money }}</span>
  <span data-northbound-compare-display>{{ compare_at_price | money }}</span>
</span>
```

### Add to cart button

```html
<button data-northbound-add-to-cart data-northbound-variant-id="{{ variant.id }}">…</button>
```

### Cart contract

Cart drawer + cart page need the most hooks:

- `data-northbound-cart` — root
- `data-northbound-cart-line` + `data-northbound-variant-id` + `data-northbound-base-line-cents` — each line
- `data-northbound-cart-quantity` — qty input
- `data-northbound-cart-line-total` — line total display
- `data-northbound-cart-products-total` + `data-northbound-base-subtotal-cents` — subtotal
- `data-northbound-cart-shipping-total`, `data-northbound-cart-shipping-note`, `data-northbound-cart-grand-total` — shipping/grand total
- `data-northbound-cart-checkout` — checkout button (gated by min-order)
- `data-northbound-cart-alert` + `data-northbound-cart-alert-message` — min-order alert
- `data-northbound-min-order-display` — anywhere min-order amount needs to render (e.g. announcement bar)

The legacy theme renders these in `snippets/product-item.liquid`, `snippets/product-form.liquid`, `templates/product.liquid`, and `templates/cart.liquid` — useful as a reference for the contract, **not** as a code-style template (legacy uses jQuery + Bootstrap 4).

## Integration approach for this rebuild

- **Don't put `@app` blocks per surface.** Northbound doesn't expose any. The theme accepts `@app` only as future-proofing for *other* apps that might want per-section placement.
- **Wrap Horizon's `price.liquid`** in a thin snippet (e.g. `nnc-aware-price.liquid`) that emits the `data-northbound-price-surface` envelope around it, so we get Northbound's mutation without re-implementing money formatting.
- **Add `data-northbound-add-to-cart`** + `data-northbound-variant-id` to ATC buttons everywhere they appear.
- **Mount the selector UI** by rendering `<div data-northbound-location-slot>` once in the header (and optionally elsewhere — the runtime will populate every match).
- **Cart needs the most hooks** — when we get to cart work, we'll have to wire the full cart contract above.

## App modification is in scope

The Northbound app source lives at `references/northbound/` and **can be modified** to support new theme features. The integration boundary still holds (Northbound logic stays in the app, not in theme code), but extending the app — new app proxy endpoints, new storefront JS hooks, new DOM-attribute contracts, schema additions — is fair game when the theme needs them.

When making app changes:

- Coordinate the theme change and the app change so they ship together.
- Document the new contract (attribute name, endpoint shape) in `docs/horizon-notes.md` so future theme work knows what's available.
- Don't fork integration logic into the theme just to avoid touching the app.

## Active theme-side feature requests

Tracked in `docs/roadmap.md` (Product Page & Product Card, Cart) — single source of truth so this doc doesn't drift. Original April 2026 client feedback is captured in `references/northbound/docs/waleed-feedback-2026-04-27.md`; checkout disclaimers and the Canadian North employee gate are likely checkout-extension work, not theme.

## Reference

- `references/northbound/CLAUDE.md` — full Northbound architecture, domain model, and app-side decision principles.
- `references/arcticfresh-legacy-theme-export/` — the legacy theme currently in production with Northbound integrated. Reference for the contract; **not** a code-style template.
