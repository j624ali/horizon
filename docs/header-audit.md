# Header / Navigation Audit

Snapshot of how each available reference theme implements the header, with implications for the Arctic Fresh build.

> **Path note:** The CLAUDE.md "References" section lists `references/horizon-export/` and `references/dawn-export/`. The actual on-disk paths are `references/arcticfresh-horizon-export/` and `references/arcticfresh-dawn-export/`. The "vanilla Horizon" baseline used in this audit is `arcticfresh-horizon-export/`. CLAUDE.md should be updated.

## This repo (current state)

The current header is **Horizon stock with minor drift** from `arcticfresh-horizon-export`.

- **Files:** `sections/header.liquid` (1646 lines), `sections/header-announcements.liquid`, `sections/search-header.liquid`, `sections/header-group.json`. Blocks: `_header-logo`, `_header-menu`, `_announcement`, `menu`. Snippets: `header-actions`, `header-drawer`, `header-row`, `mega-menu-list`, `search`, `search-modal`, `predictive-search-*`, `menu-font-styles`, `submenu-font-styles`.
- **Tier structure:** Two-row header (top + bottom), each with its own color scheme. Configurable per-row placement of menu / search / localization. Optional secondary `header__navigation-bar-row` for desktop-only nav under the header. Announcement bar is a separate **sibling section** (`header-announcements`) inside the `header` group, not nested.
- **Search:** Modal pattern (full-screen overlay) via `snippets/search-modal.liquid` + `predictive-search-*` snippets. Toggleable; can be in top or bottom row, left or right.
- **Mobile pattern:** Slide-from-left drawer (`header-drawer.liquid`). Multi-level (handles ≤3 levels with details/accordion). Optional collection-image grid in submenus, optional featured products/collections at top level. Mobile header collapses to 5-cell grid: `[leftA][leftB][center][rightA][rightB]` (logo center, search/menu/account/cart distributed).
- **Sticky behavior:** Three modes via schema setting — `always`, `scroll-up`, `never`. Driven by `<header-component>` Web Component (`header.js`) with `data-sticky-state` attribute.
- **Mega menu:** `snippets/mega-menu-list.liquid` driven by Shopify navigation link list. Multiple menu styles: `collection_images`, `featured_collections`, `featured_products`. Underlay div (`.header__underlay-open`) animates open via CSS height + transition.
- **Account/cart UI:** `snippets/header-actions.liquid` renders `<shopify-account>` (Shop's first-party account component) and `<cart-drawer-component>` (slide-out cart) or link-to-cart-page based on `settings.cart_type`. Cart bubble with item count.
- **Transparent header:** Per-template (home/product/collection) toggle for hero overlay. Auto-disables when sticky.
- **Localization:** `<dropdown-localization-component>` for desktop, `<drawer-localization-component>` for mobile drawer. Country flag, currency, language switcher.
- **`@app` block support:** **None.** `sections/header.liquid` schema has empty `blocks: []` (only static `_header-logo` and `_header-menu` blocks via `content_for 'block'`). `sections/header-announcements.liquid` accepts only `_announcement` blocks. **This is a blocker for placing the Northbound community selector inside the header without modifying the schema.**
- **Live config (`header-group.json`):** Already customized for Arctic Fresh. Two announcements running: NNC subsidy notice + "Delivering to 15 Nunavut communities". Menu uses `all-departments-new-site` linklist. `menu_row: bottom` (department nav under main row). `enable_sticky_header: always`. `divider_width: 1`. Color schemes 1 (top) / 2 (bottom) / 3 (announcement). `menu_style: collection_images` with `4/5` and `16/9` aspect ratios.
- **Drift from arcticfresh-horizon-export:** Current `sections/header.liquid` is 7 lines shorter — missing `render 'predictive-search-styles'` and `render 'slideshow-styles'` calls in the early liquid block. Likely an accidental edit during prior experimentation; should be reverted or intentional.

## arcticfresh-horizon-export (vanilla Horizon baseline)

Identical architecture to current repo (it's the baseline). Differences from current repo are minimal — the missing predictive-search/slideshow style renders noted above. Treat as the canonical Horizon stock for diff/comparison purposes.

## dwell

**Identical file inventory and same block/snippet names as Horizon stock.** Hex-level diff with this repo:

- `sections/header.liquid` differs (Dwell has its own tweaks to layout/scheme handling).
- `blocks/_header-menu.liquid` differs.
- `snippets/header-actions.liquid` differs.

Dwell is **a Horizon-derived theme that follows the same architectural convention** — block-based header, same drawer/modal patterns, same Web Components. It extends rather than replaces. Useful as a reference for "ways to extend Horizon's header without rebuilding," but the diffs are stylistic, not structural.

## hyper

**Different architecture — not block-based, single-file section approach.**

- `sections/header.liquid` is **3053 lines** (vs Horizon's 1646). Self-contained. No header blocks; everything is settings-driven inside the section.
- Snippets: `desktop-menu.liquid` (208 lines), `mega-menu.liquid` (397 lines), `menu-drawer.liquid` (168 lines), `predictive-search.liquid` (317 lines).
- **Tier structure:** `.header__top` and `.header__bottom` rows, layout selectable via `header_layout` setting. Configurable padding per row.
- **Sticky:** Custom `<sticky-header is="sticky-header">` Web Component with `data-sticky-type` and `data-collapse-on-scroll` options.
- **Mega menu:** Larger, more elaborate than Horizon's. Multiple layout patterns inside `mega-menu.liquid`.
- **Mobile drawer:** `menu-drawer-button` with `aria-controls="MenuDrawer"`, separate `menu_mobile` link list setting (so mobile menu can differ from desktop).
- **Search:** Dedicated `predictive-search.liquid` (317 lines) — heavier predictive search UX.
- **Tailwind-style utility classes** (`flex items-center lg:hidden`) — different convention from Horizon's BEM.
- **`@app` blocks:** Not surveyed in detail — Hyper isn't a candidate for the base.

Useful as a reference for "what a maximalist header-as-section looks like," but diverges from Horizon conventions; not a candidate base.

## dawn

**Older Shopify reference theme (pre-Horizon).**

- `sections/header.liquid` (638 lines) + `header-mega-menu.liquid` (94), `header-drawer.liquid` (295), `header-search.liquid` (108).
- Section iterates `section.blocks` looking for `@app` type — Dawn **does** accept `@app` blocks in header (`for block in section.blocks; if block.type == '@app'`). This pattern is what Horizon currently lacks.
- `<sticky-header data-sticky-type="...">` Web Component (similar pattern to hyper, predates Horizon's `<header-component>`).
- **Mega menu:** Single snippet, simpler than Horizon's. Driven by Shopify nav linklist.
- **Search:** `<details-modal>`/`<predictive-search>` modal pattern — solid baseline, similar in spirit to Horizon's.
- **Mobile drawer:** Stacked details/summary tree, simpler than Horizon's drawer (which has level-3 submenu navigation, featured content panels, animations).
- **CSS:** External component CSS files (`component-list-menu.css`, `component-search.css`, `component-menu-drawer.css`) loaded with `media="print" onload="this.media='all'"` trick. Horizon uses inline `{% stylesheet %}`.

Useful for understanding **how `@app` blocks slot into a header schema** (Dawn pattern can be ported to Horizon). Otherwise the architecture is older.

## arcticfresh-legacy-theme-export (live legacy site)

Three-tier Bootstrap 4 / jQuery / Slick.js theme. **Reference only.**

- **`snippets/header.liquid` (114 lines)** + `snippets/navigation-all-departments.liquid` (38 lines). Loaded via `{% include 'header' %}` (deprecated tag) from `layout/theme.liquid`.
- **Tier 1 (top, primary-bg):** NNC subsidy disclaimer with `<span data-northbound-min-order-display>$95</span>`, top nav linklist (`settings.top_nav`), and `<div data-northbound-location-slot>` (community selector mount point).
- **Tier 2 (middle, wood-bg, sticky):** Logo, hamburger toggle (Bootstrap collapse), search form (GET to `/search`), cart button (text + icon, with item count badge), mobile cart icon.
- **Tier 3 (bottom):** "All Departments" mega-menu trigger (renders `navigation-all-departments`), main nav linklist (`settings.main_nav`), repeated top nav + Northbound location slot.
- **Mega menu:** Bootstrap col-md-4/col-md-8 split. Left rail = parent department list, right rail = children grid. Supports `product_link` type rendering inline `product-item` snippet.
- **Search:** Inline form, no autocomplete, no predictive search.
- **Northbound integration hooks (preserve these contract names):**
  - `data-northbound-min-order-display` — populated with the live minimum-order amount.
  - `data-northbound-location-slot` — community selector mount point (appears in BOTH top and bottom rows).
- **Content inventory worth preserving:** NNC disclaimer copy, "All Departments" affordance, persistent community indicator, mobile cart icon with badge, three menu link lists (top nav, main nav, departments nav).

Structure to abandon: jQuery, Bootstrap, Slick, `{% include %}`, repeating nav in two places, no predictive search.

---

## Recommendations

### Strongest base to extend

**Horizon stock (current repo, with the small drift cleaned up).** The block-based architecture, multi-row layout primitive, sticky modes, predictive-search modal, accessible drawer, `<header-component>`/`<shopify-account>`/`<cart-drawer-component>` integrations, and BEM/scoped-CSS conventions are all already production-quality. Replacing them with Dawn-style or Hyper-style code would be regression. The work is **extension, not rewrite.**

### Top 5 patterns to reuse

1. **`<header-component>` + `data-sticky-state` + sticky modes** (`always` / `scroll-up` / `never`) — keep as-is; Arctic Fresh likely wants `scroll-up` for grocery (preserve viewport while showing cart on demand).
2. **Predictive search modal** (`search-modal.liquid` + `predictive-search-*`) — full-screen overlay is the right grocery UX. Don't downgrade.
3. **`header-drawer.liquid` for mobile** — multi-level, accessible, animated, supports collection images and featured content. Keep.
4. **`<cart-drawer-component>`** — slide-out cart with view-transitions. Already wired in `header-actions.liquid`.
5. **Multi-row header with per-row color scheme** — gives us the 3-tier layout (announcement → main → departments) without inventing new layout primitives. Configure via `menu_row: bottom` (already set in `header-group.json`).

### Top 3 patterns to avoid

1. **Hyper's monolithic 3000-line section.** Block-based header is more maintainable; a fresh dev session can navigate it without spelunking.
2. **Dawn's external component CSS files with `media="print"` swap.** Horizon's inline `{% stylesheet %}` is the convention; mixing styles will fragment the codebase.
3. **Legacy's repeated nav in tier 1 and tier 3.** The same links shouldn't appear twice. Consolidate.

### Open questions

1. **Community selector placement.** Horizon's header sections do not accept `@app` blocks. Three options to resolve:
   - **(a) Extend `header-announcements.liquid` schema** to accept `{ "type": "@app" }` alongside `_announcement`. Mounts the selector in the announcement bar (matches the legacy theme's tier-1 placement). Lowest-risk change.
   - **(b) Extend `header.liquid` schema** to accept `@app` blocks in a new "actions" zone. Selector lives next to cart/account.
   - **(c) Build a new section between announcement-bar and header** that accepts only `@app` blocks. Cleanest separation.
   - Recommend (a) for parity with legacy + minimal schema surgery. Decision needed.
2. **Restore the missing `predictive-search-styles` / `slideshow-styles` renders** in `sections/header.liquid` (current repo is 7 lines shorter than `arcticfresh-horizon-export`). Was this intentional?
3. **Sticky mode for Arctic Fresh:** `always` (current setting) vs `scroll-up` (recommendation). `scroll-up` reclaims viewport on long collection pages but may feel less "always available" for cart. Decide based on mobile cart-access UX priority.
4. **Search bar visibility on desktop.** Currently icon → modal. Many grocery sites use a persistent inline search bar in the main row. Worth deciding before the playground sketch.
5. **Account UI when accounts disabled.** `header-actions.liquid` only renders `<shopify-account>` when `shop.customer_accounts_enabled`. Confirm the live store has accounts on (legacy theme has account UI but it's not visible in this snippet).
6. **Mobile bottom-row visibility.** The bottom row has `mobile:hidden` (`sections/header.liquid:346`). Department nav doesn't appear on mobile by default — relies entirely on the drawer. Acceptable, or do we want a horizontal-scroll department strip on mobile too?
