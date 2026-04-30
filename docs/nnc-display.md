# NNC pricing display

How Nutrition North Canada-subsidized pricing renders on the storefront. Source of truth for the visual + interaction pattern; product cards, search results, and the product detail page all follow this.

## The principle: eligibility, not sale

NNC is a **structural federal subsidy**, not a promotion. The price the customer sees is the price they pay. Treat it the way Costco treats member pricing or how Instacart/Amazon Fresh treat SNAP-eligible items: a small, calm badge of program participation — not a "you saved $X" splash.

This is an active design constraint. Sale framing trains customers to perceive the subsidy as a temporary promo that could be revoked. NNC isn't going anywhere.

## On product cards

Layout pattern:

```
[Product image]
KIRKLAND SIGNATURE         ← vendor (small, caps, faint)
Frozen Whole Strawberries  ← product title
$24.99                     ← all-in price (forest #003d1c, weight 600-700)
[NNC subsidized]           ← small pill below price
[Add to cart]
```

Rules:

- Show only the **all-in price** (base + shipping − subsidy). The selected community drives the calculation; the Northbound app delivers it.
- **No strikethrough pre-subsidy price on cards.** Confusing, looks like a discount.
- Eligibility pill: ~11–12px, weight 500, mint or forest tinted. Text: "NNC subsidized" or just "NNC". Optional: encode the tier (NNC1, NNC2, NNC7) in the pill variant if useful, but the customer-facing label stays plain English.
- **Coral (`#ff8b69`) is reserved for genuine sales/promos.** Don't use coral on NNC indicators — they collide visually otherwise.
- The card must indicate the selected community somewhere persistent on the page (header) so the customer understands why prices are what they are. The community selector handles this.

## On the product detail page (PDP)

The per-item NNC breakdown lives on the PDP — **collapsed by default**, expandable. Captures the April 2026 client feedback in `references/northbound/docs/waleed-feedback-2026-04-27.md`.

Collapsed:

```
$24.99
▸ View NNC breakdown
[Add to cart]
```

Expanded:

```
Base price                $42.50
Shipping (Iqaluit)        +$8.20
NNC subsidy applied       -$25.71
─────────────────────────────────
You pay                   $24.99
```

The collapsed-by-default rule keeps the PDP clean for browsing while making the math discoverable for customers who want to understand it. The expanded view is the one place a comparison-style line item is acceptable — it's a transparent breakdown, not a was/now sale frame.

## What never appears on the storefront

- "You saved $X" framing on cards or PDP.
- Strikethrough pre-subsidy prices on cards.
- Red or coral subsidy badges (read as sale).
- Live-changing prices without an indicator of which community is selected.

## References

- `references/northbound/docs/waleed-feedback-2026-04-27.md` — April 2026 client feedback on the PDP breakdown panel.
- Pattern anchors: Instacart SNAP-eligible, Amazon Fresh EBT-eligible, Costco member pricing — all show the eligible price as the price, with a small badge, no strikethrough.
