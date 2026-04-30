# Arctic Fresh — Horizon Theme

Custom Shopify Horizon theme for Arctic Fresh, an Inuit-operated grocery store delivering to 15 communities across Nunavut under the Nutrition North Canada (NNC) subsidy program.

Ground-up build on Horizon — not a migration from the legacy theme. Flag root causes; don't paper over broken abstractions.

## Client context

**Store:** `arcticfresh.ca` (storefront), `arcticfresh.myshopify.com` (admin). Parent company Friendship Fast (Ottawa) handles procurement and shipping; their site is on WooCommerce and out of scope.

## Theme architecture

**Base:** Shopify Horizon v3.4.0 — block-based OS2.0 theme with nested theme blocks (up to 8 levels).
**Stack:** Liquid + vanilla CSS (BEM, scoped via `{% stylesheet %}`) + vanilla JS (Web Components via the `Component` framework in `assets/component.js`).

Non-standard directories worth knowing:

```
playground/     Static HTML/CSS/JS prototypes (see playground/CLAUDE.md)
docs/           Project-internal docs (see "Reference documents" below)
references/     Read-only third-party material (gitignored)
.claude/rules/  Path-scoped instructions (lazy-loaded for matching files)
.claude/skills/ On-demand skills: shopify-liquid, accessibility, section-builder
```

### Constraints

- Max 25 sections per JSON template, max 50 blocks per section.
- Theme blocks and section blocks cannot coexist in the same section.
- Only ONE `{% content_for 'blocks' %}` per Liquid file.
- JS must be under 16KB minified per file. No external dependencies.
- Development themes auto-delete after 7 days of inactivity or on `shopify auth logout`.

## Commands

```bash
shopify theme dev --store arcticfresh   # Local dev with hot reload (localhost:9292)
shopify theme check                      # Lint Liquid and JSON
shopify theme push --unpublished         # First deploy: create new unpublished theme
shopify theme push                       # Update existing theme on store
shopify theme publish                    # Make theme live (confirm with user first)
```

## External tools

- **`sonar "<query>"`** — Perplexity sonar-pro-search CLI. Reach for it when Shopify docs or local context aren't enough (Horizon edge cases, NNC program details, ecommerce best practices).
- **`admin_gql '<query>'`** — direct Admin GraphQL access to the live store. Verify catalog state (collection handles, metafields, image presence) instead of guessing. **Don't mutate.** Recipes in `docs/admin-gql-recipes.md`.

Append `.md` to any Shopify docs URL for clean markdown. Start at https://shopify.dev/docs/storefronts/themes.md.

## Domain context

### Nutrition North Canada (NNC)

Shipping classes: `NNC1` (perishable, highest subsidy), `NNC2` (other food), `NNC7` (non-food essentials), `UNSUBSIDIZED`. Theme reads `custom.shipping_class` and `custom.nnc_item_code` metafields; Northbound owns pricing math. See `docs/nnc-display.md` for the storefront pricing pattern (NNC is treated as eligibility, not as a sale).

### Arctic Fresh specifics

- **15 Nunavut communities served** (Iqaluit, Rankin Inlet, Pangnirtung, …). Community selection is the first step — it determines all displayed pricing and shipping rates.
- **9,000+ SKUs across 9 departments.** Confirmed live umbrella collection handles in `docs/admin-gql-recipes.md`.
- **$95 minimum order.** Northbound's `ShopConfig` is authoritative.
- **Northbound is live in production**, processing real orders. Theme changes that touch pricing, cart, or shipping must not assume the app's storefront JS is absent.

## Decision principles

1. **Code over configuration.** Custom sections in Liquid over JSON-template-and-theme-editor composition. Keep `templates/*.json` lean. Theme editor configurability for the client is not a priority.
2. **DOM-attribute hooks for Northbound.** Northbound is a global app-embed + a `data-northbound-*` attribute contract. Theme renders markup; the app's runtime mutates it. Don't bake pricing/shipping logic into Liquid — emit attributes and let Northbound do the math. Full contract in `docs/northbound-integration.md`.
3. **Mobile-first for grocery.** Many customers browse on phones. Horizontal scrolling for department nav, accordion menus, 44×44px touch targets minimum.
4. **Performance matters.** Remote Nunavut communities often have limited bandwidth. Lazy load images, keep JS minimal, avoid render-blocking resources. Lighthouse target 60+ across home, product, collection.
5. **Static pages are template-driven.** One flexible `templates/page.json` + content-rich section, populated via the page editor. No custom section per static page.

## Shortcut doctrine

Horizon's full conventions (Component framework, theme blocks, scoped `{% stylesheet %}`, `{% content_for 'blocks' %}`, 3-tier tokens) are heavyweight and not always worth the ceremony for net-new work. Two patterns coexist:

**Stock Horizon files** — anything that ships in vanilla Horizon (`sections/header.liquid`, `blocks/group.liquid`, `snippets/resource-card.liquid`, etc.): keep using Horizon conventions. Don't fork the file's pattern; that breaks future merges from upstream. When forking a stock file's `{% stylesheet %}` block, extract it into a sibling `*-styles.liquid` snippet first (Horizon's own pattern — `predictive-search-styles.liquid`, `slideshow-styles.liquid`).

**New Arctic Fresh sections** — anything net-new for this project: take the shortcut. Section blocks or hardcoded markup (no theme blocks). Plain `class extends HTMLElement` (no `Component` framework). Per-feature `assets/arctic-*.css` or component-scoped `{% stylesheet %}` if self-contained. `settings: []` by default. Separate files per variant rather than `{% case %}` mega-blocks.

The line: if it ships in vanilla Horizon, treat it as Horizon. If it's net-new, take the shortcut. Detailed code-style rules are in `.claude/rules/code-style.md` (lazy-loaded when editing matching files).

## How Horizon themes are typically extended

Brand work on Horizon is mostly token + thin CSS overlay (`*-styles.liquid` snippets, additions in `theme-styles-variables.liquid`). Liquid markup stays close to vanilla. Reach for tokens and `*-styles.liquid` snippets first; fork Liquid only when the markup itself must change. See `docs/horizon-notes.md`.

## Workflow

1. **Research first** for non-trivial design/architecture decisions — `sonar` for ecommerce best practices, Shopify docs for platform specifics. Cheaper than rework.
2. **Test in the browser.** `shopify theme check` won't catch layout bugs. Load via `shopify theme dev` and inspect at mobile + desktop, golden path + at least one edge case (long text, missing image, empty state).
3. **Document Horizon learnings as you go** in `docs/horizon-notes.md`.

## Reference documents

Progressive disclosure — identify which are relevant to the task and read them. Don't load everything every session.

- `docs/roadmap.md` — Major work areas, current status, open questions. **Read when:** scoping new work or unsure if something is in-flight.
- `docs/horizon-notes.md` — Horizon-specific gotchas, working patterns, token system, theme delta findings. **Read when:** writing any new section/block/snippet, or hitting a Horizon-specific bug.
- `docs/northbound-integration.md` — DOM contract, app proxy endpoints, integration approach, app-modification scope. **Read when:** touching pricing surfaces, cart, ATC buttons, community selector, or shipping display.
- `docs/nnc-display.md` — Storefront pricing patterns (cards, PDP breakdown, what to never show). **Read when:** building or editing any UI that shows price.
- `docs/brand-guardrails.md` — Inuit-operated context, what to avoid, reference brands. **Read when:** any visual or copy decision touching the brand surface.
- `docs/header-audit.md` — Current header analysis vs. references. **Read when:** modifying header/nav.
- `docs/admin-gql-recipes.md` — Common Admin GraphQL queries for catalog recon. **Read when:** verifying live store state.
- `docs/research-reports/` — Deep-dive investigations (design, header, web research). **Read when:** doing exploratory work in those areas.

**Skills** in `.claude/skills/` (`shopify-liquid`, `accessibility`, `section-builder`) load on-demand. The `section-builder` skill has the full inventory of Horizon's reusable snippets — reach for it before building custom UI.

**References** in `references/` (gitignored, fresh clones won't have them):

- `horizon/`, `dawn/`, `dwell/`, `hyper/` — example Shopify themes for "how does Horizon do X" lookups. None are this project's vanilla baseline; for that, `git diff` against the upstream Horizon merge commits in this repo's history.
- `northbound/` — the Northbound app source (React Router v7 + Prisma + TypeScript). Modifiable in scope when extending the app for new theme features. See `references/northbound/CLAUDE.md`.
- `arcticfresh-legacy-theme-export/` — pre-OS2.0 theme currently live. Reference only for the Northbound DOM contract — **do not migrate code from it.**
