# Roadmap

Living plan for the Arctic Fresh Horizon theme rebuild. Update as scope shifts. Committed to the repo so fresh Claude sessions inherit it.

**Status legend:** `not started` · `in progress` · `done` · `blocked`

---

## Foundation

Setup work that unblocks everything else. Bias toward doing these early.

| Item | Status | Notes |
|------|--------|-------|
| Brand tokens (colors, fonts, spacing) defined in `config/settings_data.json` | not started | Pull palette from legacy theme + client brand if any. |
| `theme.liquid` skeleton (head, scripts, layout shells) reviewed for Arctic Fresh | not started | Horizon ships a sane default; mostly want to verify nothing legacy-Arctic crept in. |
| Page-template baseline (`templates/page.json`) for static pages | not started | Decision Principle #5 — one flexible template. |
| Northbound app block placement zones identified | not started | Where exactly do `@app` blocks need to be accepted? Cart, product, header (community selector). |

---

## Navigation / Header

Clean, modern, low-friction navigation. Mobile-first. **Search-dominant** — at 9k+ SKUs, search is the primary entry, not browse. Reasoning in `docs/research-reports/2026-04-29-design-research.md`.

| Item | Status | Notes |
|------|--------|-------|
| Header layout (logo, search, cart, account) | not started | Sticky on mobile. Search bar **prominent and full-width**, not a tucked-away icon. Persistent community indicator so customers know why prices are what they are. |
| Predictive search | not started | Primary discovery mechanism. Needs **typeahead with thumbnails**, **typo tolerance**, **recent searches**, **semantic dedupe** ("milk" not "milk + milks"). Horizon ships a predictive-search snippet — evaluate before custom. Shopify's native search + Search & Discovery app gets ~70% of the way; Algolia/Searchanise only if budget supports. |
| Department mega menu (desktop only) | not started | Driven by Shopify navigation, rendered via `snippets/mega-menu-list.liquid`. Mobile gets pills, not mega-menu. |
| Mobile category pills | not started | Horizontal swipeable pill row (Produce, Dairy, Pantry, Frozen, Meat & Seafood, Drinks, Baby & Kids, Household, Health & Beauty). Replaces a mobile mega-menu. |
| Mobile bottom nav | not started | Persistent 4-icon: **Home / Search / Buy Again / Cart**. Standard pattern across Instacart and Walmart Grocery. |
| Announcement bar | not started | Optional. Useful for delivery notices, holiday cutoffs. |

**Open questions**
- Community selector placement: header-persistent vs first-visit modal vs both?

---

## Homepage

Conversion-optimized for a large-catalog grocery site, **built for returning customers** (60-80% repeat-purchase rate per the design research). Order matters — repeat purchases above the fold, discovery below. Reasoning frozen in `docs/research-reports/2026-04-29-design-research.md`.

| Item | Status | Notes |
|------|--------|-------|
| Hero / banner section | not started | Slideshow snippet already exists in Horizon. **Autoplay off by default** — preloading multiple slide images is real bandwidth on northern networks. |
| **Buy It Again carousel** | not started | **Above the fold for returning customers.** Highest-impact returning-user pattern in grocery research. Source from `customer.orders` + a small carousel snippet. Hide for first-time visitors. |
| Department shortcuts | in progress | `sections/department-shortcuts.liquid` exists in dev-preview mode (hardcoded). Live data path is commented inline; waiting on confirmed admin images. |
| Featured collections / promos | not started | Reuse Horizon's `resource-list.liquid` with collection input. |
| Featured products row | not started | |
| Editorial / brand storytelling | not started | Optional. Tell the Inuit-operated, NNC story. Use real Nunavut photography per `docs/brand-guardrails.md`. |
| Newsletter / community signup | not started | Legacy had this. Likely worth keeping. |

**Resolved**
- Primary conversion goal: build for returners. Returning customers go straight to Buy It Again, then search. New customers orient via department pills + featured.

---

## Shop Pages (Collection + Search + Filters)

| Item | Status | Notes |
|------|--------|-------|
| Collection template (`templates/collection.json`) | not started | Use Horizon's `product-grid.liquid` with infinite scroll. |
| Filters / facets | not started | Horizon ships filter UI — evaluate before custom. |
| Sort | not started | |
| Search results page | not started | |
| Empty state UX | not started | Important when filters return zero. |

---

## Product Page & Product Card

NNC-aware. Source of truth for shipping class is `custom.shipping_class` metafield (set by Northbound).

| Item | Status | Notes |
|------|--------|-------|
| Product card variants (collection grid, featured row, search result) | not started | Card needs to host `@app` block for Northbound pricing. |
| NNC eligibility badge on card | not started | From Apr 2026 client feedback. Read `custom.shipping_class`. |
| Product page layout (gallery, info, ATC, related) | not started | Horizon's stock product template is a good starting point. |
| Per-item NNC breakdown panel on PDP | not started | From Apr 2026 client feedback. May need new app proxy data. |
| Variant picker (color/size/etc.) | not started | Horizon ships this. |

**Open questions**
- Where on the card does the NNC badge live without crowding the price/community-pricing zone?

---

## Cart

Tight integration with Northbound for pricing and shipping estimates.

| Item | Status | Notes |
|------|--------|-------|
| Cart drawer vs cart page (or both) | not started | Decision needed. Horizon ships both. |
| Line item +/- quantity buttons | not started | From Apr 2026 client feedback. |
| Substitution preferences per line | not started | Legacy has this. Confirm if still required. |
| Minimum order alert ($95) | not started | Northbound's `ShopConfig` is the source. Check before hardcoding. |
| Estimated shipping display | not started | Legacy showed this — Northbound carrier service computes the real number at checkout. |
| Empty cart state | not started | |

---

## Static Pages

Template-driven (Decision Principle #5). One flexible template, populate via admin.

| Item | Status | Notes |
|------|--------|-------|
| Inventory of legacy static pages | not started | Walk `references/arcticfresh-legacy-theme-export/` and live store. |
| Flexible page template (`templates/page.json`) | not started | Sections: rich text, image+text, FAQ accordion, contact form. |
| Migrate / rewrite copy for each page | not started | Likely a paste-and-clean task by the client team after template lands. |

---

## Community Selector

**Owned by Northbound, not the theme.** The theme's job is to provide a placement zone (`@app` block) and not conflict with the selector's behavior. Out of scope as a theme-side build.

| Item | Status | Notes |
|------|--------|-------|
| Header has an `@app` block placement zone | not started | |
| First-visit modal / overlay treatment | not started | If we want a forced selector on first visit, decide: theme-side or app-side? |

---

## Open / Cross-cutting

- Performance budget per page (Lighthouse target ≥60, see CLAUDE.md).
- Accessibility audit before launch (skill `accessibility` in `.claude/skills/`).
- Locales: French is likely needed (Nunavut / Quebec context). Confirm with client.
- Deployment plan: dev theme → unpublished theme → publish. Confirm cutover timing with client.
