---
name: section-builder
description: Workflow for creating new Liquid sections and snippets in the Arctic Fresh Horizon theme. Use when building a new homepage section, creating a reusable snippet, or adding any new section to a template. Triggers when the user asks to create, build, or add a section or snippet to the theme.
user-invocable: false
paths:
  - "sections/**"
  - "snippets/**"
  - "templates/**"
---

# Section Builder Workflow

For creating new sections / snippets in the Arctic Fresh Horizon theme. The biggest decision is **which doctrine applies** — that determines half of what follows.

## Doctrine: stock Horizon vs. net-new Arctic Fresh

Two patterns coexist (full rules in `CLAUDE.md` → "Shortcut doctrine"):

- **Stock Horizon work** — forking or extending a file that ships in vanilla Horizon (`sections/header.liquid`, `blocks/group.liquid`, `snippets/resource-card.liquid`, etc.): keep Horizon conventions. Theme blocks, scoped `{% stylesheet %}`, `Component` framework for JS, 3-tier tokens. Don't break the upstream merge path.
- **Net-new Arctic Fresh sections** — anything created from scratch for this project: take the shortcut. Section blocks or hardcoded markup (no theme blocks). Plain `class extends HTMLElement` (no `Component` framework). Per-feature `assets/arctic-*.css` or component-scoped `{% stylesheet %}`. `settings: []` by default. Separate files per variant rather than `{% case %}` mega-blocks.

The line: if it ships in vanilla Horizon, treat it as Horizon. If it's net-new, take the shortcut.

The snippet inventory below applies to **both** patterns — reaching for existing Horizon snippets is correct regardless of which doctrine the calling section follows.

## Step 1: Identify the closest existing pattern

Before writing any code, find the Horizon section or snippet that solves the most similar problem.

- For a collection grid → study `sections/collection-list.liquid`
- For a product list → study `sections/product-list.liquid`
- For a content section → study `sections/media-with-content.liquid`
- For a card component → study `snippets/resource-card.liquid`

Read the full file to understand HTML structure, classes, and responsive behavior.

## Step 2: Audit available CSS classes in `assets/base.css`

Before writing custom CSS, check what already exists. Horizon has a rich utility set:

- **Typography presets:** `.h1` through `.h6` — map to theme font variables. Use the doubled-class pattern (`.h4.h4`) for correct specificity.
- **Layout:** `.layout-panel-flex`, `.layout-panel-flex--row`, `.layout-panel-flex--column` — flex containers driven by CSS custom properties.
- **Links:** `.link` — standard underline link with hover state.
- **Color schemes:** `.color-scheme-1` through `.color-scheme-6` — apply full color context.
- **Section wrappers:** `.section`, `.section--page-width`, `.section--full-width`, `.section-background`.

Don't rewrite what already exists. But write as much new CSS as the section needs for its own behavior (grids, aspect ratios, hover effects, scroll behavior, etc.).

## Step 3: Check for reusable snippets and components

**CRITICAL:** Before building ANY interactive UI, check what Horizon already provides. Using existing components ensures consistency, accessibility, and saves significant work.

### Carousel / Slideshow system

Use Horizon's built-in slideshow for any horizontally scrolling content:

- **`snippets/slideshow.liquid`** — Core carousel. Renders `<slideshow-component>` with arrows, pagination, autoplay, infinite loop, keyboard nav. Params: `slides`, `slide_count`, `show_arrows`, `icon_style`, `icon_shape`, `infinite`, `autoplay`, `slideshow_gutters`.
- **`snippets/slideshow-slide.liquid`** — Individual slide wrapper. Params: `index`, `children`, `class`, `slide_size`.
- **`snippets/slideshow-arrows.liquid`** — Prev/next arrows. Params: `icon_style`, `icon_shape`, `arrows_position`.
- **`snippets/slideshow-controls.liquid`** — Pagination dots, counter, or thumbnails. Params: `style`, `item_count`, `show_arrows`.
- **`snippets/resource-list-carousel.liquid`** — Higher-level carousel for resource lists. Params: `ref`, `slides`, `slide_count`, `settings`, `slide_width_max`.
- **`snippets/resource-list.liquid`** — Master layout switcher (grid/bento/carousel/editorial) with automatic mobile carousel fallback. Params: `list_items`, `list_items_array`, `settings`, `content_type`.
- **`assets/slideshow.js`** — Web Component handling scroll, drag/swipe, keyboard nav, infinite loops, autoplay.

For chip-scale or compact card-row scrollers, plain `overflow-x: auto` + `scroll-snap-type: x proximity` is enough — don't reach for `slideshow.liquid` unless you need its full feature set (see `docs/horizon-notes.md` → "Pure-CSS scroll-snap for compact horizontal rows").

### Card components

- **`snippets/resource-card.liquid`** — Universal card for products, collections, articles. Overlay link pattern, image hover. Params: `resource`, `resource_type`, `style`, `image_aspect_ratio`, `image_hover`.
- **`snippets/product-card.liquid`** — Product card with quick-add and view transitions. Params: `product`, `children`, `block`.
- **`snippets/collection-card.liquid`** — Collection card with flexible image placement. Params: `collection`, `children`, `card_image`, `block`.
- **`snippets/card-gallery.liquid`** — Product image carousel inside cards with variant switching and badges.

### Layout helpers

- **`snippets/section-header.liquid`** — Title + "View all" link row. Params: `title`, `link_url`, `link_text`.
- **`snippets/image.liquid`** — Responsive image with retina srcsets and focal points. Params: `image`, `height`, `class`, `text_fallback`.
- **`snippets/background-media.liquid`** — Full-bleed background image or video. Params: `background_media`, `background_image`, `background_video`.
- **`snippets/overlay.liquid`** — Solid or gradient overlay. Params: `settings`.
- **`snippets/button.liquid`** — Styled link button. Params: `link`, `block`.
- **`snippets/icon.liquid`** — Inline SVG icons from Horizon's icon library.
- **`snippets/divider.liquid`** — Horizontal line separator. Params: `id`, `settings`, `full_width`.
- **`snippets/price.liquid`** — Product pricing with compare-at, volume, unit pricing. Params: `product_resource`, `show_unit_price`. **Wrap this in a `data-northbound-price-surface` envelope** when emitting prices — see `docs/northbound-integration.md`.

### Grid layouts

- **`snippets/bento-grid.liquid`** — Asymmetric bento box layout (12-item boxes). Params: `items`.
- **`snippets/editorial-product-grid.liquid`** — Magazine-style asymmetric product grid.
- **`snippets/editorial-collection-grid.liquid`** — Magazine-style collection grid.
- **`snippets/product-grid.liquid`** — Collection/search page product grid with infinite scroll. Params: `section`, `products`, `children`.

### Layout primitives (snippets that generate CSS variables)

- **`snippets/spacing-style.liquid`** — Responsive padding via CSS vars. Params: `settings`.
- **`snippets/gap-style.liquid`** — Responsive gap with scaling. Params: `value`, `name`, `scale_min`.
- **`snippets/size-style.liquid`** — Width/height with custom/fill modes. Params: `settings`.
- **`snippets/layout-panel-style.liquid`** — Flex direction, alignment, gap. Params: `settings`.
- **`snippets/group.liquid`** — Flex container with background media and overlay.

### Interactive components (JS)

- **`assets/dialog.js`** — Modal dialogs with scroll lock and escape key. Methods: `showDialog()`, `closeDialog()`, `toggleDialog()`.
- **`assets/anchored-popover.js`** — Positioned popovers/dropdowns anchored to trigger elements.
- **`assets/floating-panel.js`** — Auto-repositioning panels that stay in viewport.
- **`assets/accordion-custom.js`** — Collapsible details/summary with breakpoint-aware behavior.
- **`assets/marquee.js`** — Continuous scrolling marquee with hover pause.

### Navigation

- **`snippets/mega-menu-list.liquid`** — Multi-column mega menu with featured products/collections.
- **`snippets/overflow-list.liquid`** — List that collapses overflow items into a "More" button.
- **`snippets/header-drawer.liquid`** — Mobile hamburger menu drawer.

**Rule of thumb:** if you're about to write custom JS for scrolling, modals, accordions, popovers, or carousels — stop and use the existing component. If you're about to write a card from scratch — check if `resource-card`, `product-card`, or `collection-card` covers it first.

If the same UI pattern will appear in 2+ sections, extract it into a snippet. Use the `{% doc %}` convention for params.

## Step 4: Write the section

### For net-new Arctic Fresh sections (shortcut doctrine)

```liquid
{% liquid
  assign ... = ...
%}

<div class="arctic-my-section" data-testid="my-section">
  {% render 'section-header', title: 'Section Title', link_url: '...' %}
  <!-- Section content -->
</div>

{% stylesheet %}
  /* Component-scoped CSS, BEM naming. Use --color-arctic-* role tokens
     defined in snippets/theme-styles-variables.liquid (see
     .claude/rules/code-style.md). For a self-contained component, this
     is fine; for cross-section primitives, add to assets/arctic-*.css
     instead. */
{% endstylesheet %}

{% schema %}
{
  "name": "My Section",
  "settings": [],
  "presets": [
    { "name": "My Section", "category": "Arctic Fresh" }
  ]
}
{% endschema %}
```

### For stock Horizon work (forking / extending vanilla files)

```liquid
{% liquid
  assign ... = ...
%}

<div class="section-background color-scheme-1"></div>
<div class="section section--page-width color-scheme-1 my-section" data-testid="my-section">
  {% render 'section-header', title: 'Section Title', link_url: '...' %}
  <!-- Section content -->
</div>

{% stylesheet %}
  /* Use Horizon's role tokens (--color-foreground, --color-accent,
     --card-corner-radius, --padding-xs, etc.). When forking a file with
     a large {% stylesheet %} block, extract it to a sibling
     *-styles.liquid snippet first — Horizon's own pattern. */
{% endstylesheet %}

{% schema %}
{ "name": "My Section", "settings": [], "presets": [{ "name": "My Section", "category": "Collections" }] }
{% endschema %}
```

### CSS rules (both)

- BEM naming: `.section-name__element--modifier`. Specificity target `0 1 0`.
- Responsive breakpoint: `750px` (Horizon's standard mobile/desktop break).
- Logical properties: `padding-block`, `margin-inline`.
- Token guidance in `.claude/rules/code-style.md` — **don't** reach for stock Horizon scheme tokens like `--color-foreground-muted` directly; those names are functional (foreground @ 60% opacity), not visual roles. Use `--color-arctic-*` role tokens for project work.

### Hardcode values (code over configuration)

Hardcode known values directly in Liquid — collection handles, section titles, layout choices. Keep `{% schema %}` minimal (`settings: []`, just `name` and `presets`). Theme editor configurability is not a priority.

## Step 5: Register in the template

Add the section to the relevant `templates/*.json` file:

1. Add a section definition in the `sections` object (just `type`, no sprawling settings).
2. Add the section key to the `order` array.
3. Validate JSON.

Keep template JSON lean — section definitions should be 3 lines, not 100.

## Accessibility defaults

Not afterthoughts:

- **Lists of items:** `<ul role="list">` + `<li>`, or `<nav aria-label="…">` for navigation lists. (Safari strips the implicit list role from unstyled `<ul>` — `role="list"` is required.)
- **Clickable cards:** overlay link pattern — `<a>` with `position: absolute; inset: 0; z-index: 1` and `<span class="visually-hidden">` for accessible text.
- **Focus states:** `:focus-visible` outline on interactive elements.
- **Motion:** wrap hover/transition effects in `@media (prefers-reduced-motion: no-preference)` (opt-in, not opt-out — see `docs/horizon-notes.md`).
- **Images:** always `loading: 'lazy'`, responsive `widths` and `sizes` via `image_url` + `image_tag` filter chain.

The `accessibility` skill has the full WCAG patterns. Consult it for anything beyond simple links.

## When a section needs JavaScript

**Net-new Arctic Fresh sections:** plain `class extends HTMLElement` (per shortcut doctrine). Manual `querySelector` + `addEventListener` is fine — Horizon's `Component` framework adds boilerplate that isn't worth it for self-contained components.

```js
class ArcticMySection extends HTMLElement {
  connectedCallback() {
    this.button = this.querySelector('[data-action]');
    this.button?.addEventListener('click', this.#handleClick);
  }
  disconnectedCallback() {
    this.button?.removeEventListener('click', this.#handleClick);
  }
  #handleClick = (event) => { /* ... */ };
}
customElements.define('arctic-my-section', ArcticMySection);
```

**Stock Horizon files** (forking `sections/header.liquid` etc.): use the `Component` framework — `extends Component` from `@theme/component`, `ref="name"` for DOM references, `on:click="/methodName"` for declarative event binding, `AbortController` for fetch cleanup, JSDoc typedefs for refs. Don't break Horizon's pattern. The full guide is at `.claude/skills/shopify-liquid/references/javascript-standards.md`.

Most sections are pure HTML/CSS — only add JS when the section needs interactive behavior (filtering, dynamic loading, cart actions, etc.).

## Related skills and references

### Skills (auto-triggered)

- **`shopify-liquid`** — Liquid, CSS (BEM), JavaScript (Component framework), schemas, localization. Consult for CSS property ordering, JS patterns, Liquid syntax rules, schema editing workflow.
- **`accessibility`** — WCAG standards for interactive elements, forms, navigation, ARIA patterns. Consult when building anything interactive beyond simple links.

### Project rules

- **`.claude/rules/code-style.md`** — Liquid/CSS/JS conventions specific to this project (BEM, scoped `{% stylesheet %}`, `--color-arctic-*` token guidance, `{% render %}` over `{% include %}`, the stylesheet extraction pattern for forking stock files).

### Theme references

1. **Our own theme's sections/snippets** — primary reference for how Horizon does things.
2. **`assets/base.css`** — search here for existing utility classes before writing custom CSS.
3. **`assets/component.js`** — the Component framework source; read when working in stock Horizon files.
4. **`references/horizon/`, `references/dawn/`, `references/dwell/`, `references/hyper/`** — example themes for "how does X work" lookups.
5. **`references/arcticfresh-legacy-theme-export/`** — legacy theme. Reference only for the Northbound DOM contract (`docs/northbound-integration.md`); do not migrate code from it.
6. **`docs/admin-gql-recipes.md`** — verify live catalog state (collection handles, product metafields) before relying on them in Liquid.

### Shopify docs

Append `.md` to any Shopify docs URL for clean markdown:

- `https://shopify.dev/docs/storefronts/themes/architecture.md` — theme architecture
- `https://shopify.dev/docs/storefronts/themes/architecture/sections.md` — section reference
- `https://shopify.dev/docs/api/liquid.md` — Liquid objects, filters, tags
