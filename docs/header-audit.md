# Header / Navigation Audit

How the Arctic Fresh header is built today and what to keep / change when modifying it.

> **Reference baseline:** "Horizon stock" comparisons here are made against `references/horizon/` (one example Horizon theme). The project's vanilla source-of-truth is the `Horizon vX.X` merge commits in this repo's git history.

> **Status (2026-04-30):** The `_header-menu.liquid` variant split has happened. Dispatcher is 252 lines; variants in `snippets/header-menu-mega.liquid` (608 lines), `snippets/header-menu-drawer.liquid` (44 lines), `snippets/header-menu-rail.liquid` (122 lines). Reflects the shortcut doctrine ("separate files per variant rather than `{% case %}` mega-blocks"). The `predictive-search-styles` / `slideshow-styles` render drift previously flagged is resolved — both renders are present.

## Current state (this repo)

The header is **Horizon stock with Arctic Fresh-specific config**, no architectural divergence.

- **Files:** `sections/header.liquid`, `sections/header-announcements.liquid`, `sections/search-header.liquid`, `sections/header-group.json`. Blocks: `_header-logo`, `_header-menu`, `_announcement`, `menu`. Snippets: `header-actions`, `header-drawer`, `header-row`, `mega-menu-list`, `search`, `search-modal`, `predictive-search-*`, `header-menu-{mega,drawer,rail}`, `menu-font-styles`, `submenu-font-styles`.
- **Layout:** Two-row header (top + bottom), each with its own color scheme. Per-row placement of menu / search / localization is configurable. Optional secondary `header__navigation-bar-row` for desktop-only nav under the header. Announcement bar is a sibling section (`header-announcements`) inside the `header` group, not nested.
- **Search:** Full-screen modal via `search-modal.liquid` + `predictive-search-*`. Toggleable, can sit in either row, either side.
- **Mobile:** Slide-from-left drawer (`header-drawer.liquid`), multi-level. Header collapses to a 5-cell grid (logo center, search/menu/account/cart distributed).
- **Sticky:** `<header-component>` Web Component with three modes (`always`, `scroll-up`, `never`) via schema setting.
- **Mega menu:** `mega-menu-list.liquid` driven by Shopify nav linklist. Styles: `collection_images`, `featured_collections`, `featured_products`. Animates open via CSS height transition.
- **Account/cart:** `header-actions.liquid` renders `<shopify-account>` and `<cart-drawer-component>` (or link-to-cart) based on `settings.cart_type`.
- **Transparent header:** Per-template (home/product/collection) toggle for hero overlay. Auto-disables when sticky.
- **Localization:** `<dropdown-localization-component>` (desktop), `<drawer-localization-component>` (mobile drawer).
- **`@app` block support:** None. Schema is `blocks: []` — only static `_header-logo` and `_header-menu` via `content_for 'block'`. Not a Northbound blocker — Northbound is a body-level app embed + DOM attribute contract; the selector mounts wherever `<div data-northbound-location-slot>` is rendered.

### Live config (`header-group.json`)

Already customized for Arctic Fresh:

- Two announcements: NNC subsidy notice + "Delivering to 15 Nunavut communities".
- Menu uses `all-departments-new-site` linklist.
- `menu_row: bottom` (department nav under main row).
- `enable_sticky_header: always`.
- Color schemes 1 (top) / 2 (bottom) / 3 (announcement).
- `menu_style: collection_images` with `4/5` and `16/9` aspect ratios.

## Reference theme comparisons (one-line summaries)

Detailed snapshots are not load-bearing — the *recommendations* below are what matters when modifying the header.

- **`references/horizon/`** — identical architecture to current repo; trivial diff. Convenient diff target, but git history is the canonical baseline.
- **`references/dwell/`** — Horizon-derived; identical block/snippet inventory; stylistic diffs only in `sections/header.liquid`, `blocks/_header-menu.liquid`, `snippets/header-actions.liquid`. Useful as "ways to extend Horizon's header without rebuilding."
- **`references/hyper/`** — different architecture (3053-line monolithic section, settings-driven, Tailwind-style utilities). **Not a candidate base** — diverges from Horizon conventions.
- **`references/dawn/`** — older Shopify reference (pre-Horizon). Notable: header schema accepts `@app` blocks (`for block in section.blocks; if block.type == '@app'`). Worth porting only if we ever host non-Northbound apps in the header.

## Legacy theme (`arcticfresh-legacy-theme-export`)

Bootstrap 4 / jQuery / Slick.js, three-tier layout. **Reference only** — preserve the Northbound contract, ignore the code style.

- **Tier 1 (top):** NNC subsidy disclaimer with `<span data-northbound-min-order-display>$95</span>`, top nav linklist, `<div data-northbound-location-slot>` (community selector mount).
- **Tier 2 (middle, sticky):** Logo, hamburger toggle, search form (GET to `/search`), cart button with item-count badge.
- **Tier 3 (bottom):** "All Departments" mega-menu trigger, main nav linklist, repeated top nav + Northbound location slot.

**Northbound integration hooks (preserve these contract names):**

- `data-northbound-min-order-display` — populated with the live minimum-order amount.
- `data-northbound-location-slot` — community selector mount point (legacy renders this in BOTH top and bottom rows; one is sufficient — the runtime populates every match).

**Content inventory worth preserving:** NNC disclaimer copy, "All Departments" affordance, persistent community indicator, mobile cart icon with badge, three menu link lists (top nav, main nav, departments nav).

**Structure to abandon:** jQuery, Bootstrap, Slick, `{% include %}`, repeating nav in two places, no predictive search.

---

## Recommendations

### Strongest base to extend

**Horizon stock (current repo).** The block-based architecture, multi-row layout primitive, sticky modes, predictive-search modal, accessible drawer, `<header-component>`/`<shopify-account>`/`<cart-drawer-component>` integrations, and BEM/scoped-CSS conventions are production-quality. The work is **extension, not rewrite.**

### Top 5 patterns to reuse

1. **`<header-component>` + `data-sticky-state` + sticky modes** — keep as-is; Arctic Fresh likely wants `scroll-up` for grocery (preserve viewport while showing cart on demand).
2. **Predictive search modal** (`search-modal.liquid` + `predictive-search-*`) — full-screen overlay is the right grocery UX.
3. **`header-drawer.liquid` for mobile** — multi-level, accessible, animated, supports collection images and featured content.
4. **`<cart-drawer-component>`** — already wired in `header-actions.liquid`.
5. **Multi-row header with per-row color scheme** — gives the 3-tier layout (announcement → main → departments) without inventing new primitives. Configure via `menu_row: bottom`.

### Top 3 patterns to avoid

1. **Hyper's monolithic 3000-line section.** Block-based is more maintainable.
2. **Dawn's external component CSS files with `media="print"` swap.** Horizon's inline `{% stylesheet %}` is the convention.
3. **Legacy's repeated nav in tier 1 and tier 3.** Same links shouldn't appear twice.

### Open questions

1. **Community selector placement.** Northbound's runtime injects the selector UI into any `<div data-northbound-location-slot>` the theme renders. Options: (a) inside the announcement bar — matches legacy tier-1 placement; (b) inside the main header row next to cart/account — selector lives in the persistent action zone; (c) both — runtime populates every match. **Recommend (b)** for visibility; cart and selector are the two community-driven controls.
2. **Sticky mode:** `always` (current) vs `scroll-up` (recommendation). `scroll-up` reclaims viewport on long collection pages but may feel less "always available" for cart.
3. **Search bar visibility on desktop.** Currently icon → modal. Many grocery sites use a persistent inline search bar in the main row. Worth deciding before the playground sketch.
4. **Account UI when accounts disabled.** `header-actions.liquid` only renders `<shopify-account>` when `shop.customer_accounts_enabled`. Confirm the live store has accounts on.
5. **Mobile bottom-row visibility.** Bottom row is `mobile:hidden`. Department nav doesn't appear on mobile by default — relies entirely on the drawer. Acceptable, or do we want a horizontal-scroll department strip on mobile too?
