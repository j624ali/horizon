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

Follow this process when creating any new section or snippet for the Arctic Fresh Horizon theme. The goal is consistency with Horizon's existing UI and codebase — never wing it.

## Step 1: Identify the closest existing pattern

Before writing any code, find the Horizon section or snippet that solves the most similar problem.

- For a collection grid → study `sections/collection-list.liquid`
- For a product list → study `sections/product-list.liquid`
- For a content section → study `sections/media-with-content.liquid`
- For a card component → study `snippets/resource-card.liquid`

Read the full file. Understand the HTML structure, what CSS classes it uses, and how it handles responsive behavior.

## Step 2: Audit available CSS classes in base.css

Before writing custom CSS, check what already exists in `assets/base.css`. Horizon has a rich set of utility classes:

**Typography presets:** `.h1` through `.h6` — each maps to theme font variables (family, size, weight, line-height, letter-spacing, color). Use the doubled-class pattern (`.h4.h4`) for correct specificity.

**Layout:** `.layout-panel-flex`, `.layout-panel-flex--row`, `.layout-panel-flex--column` — flex containers driven by CSS custom properties (`--horizontal-alignment`, `--vertical-alignment`).

**Links:** `.link` — standard underline link with hover state.

**Color schemes:** `.color-scheme-1` through `.color-scheme-6` — apply full color context.

**Section wrappers:** `.section`, `.section--page-width`, `.section--full-width`, `.section-background` — standard section framing.

Don't rewrite what already exists — if you're writing font-family, font-size, or font-weight declarations, check if a type preset class handles it first. But write as much new CSS as the section needs for its own behavior (grids, aspect ratios, hover effects, scroll behavior, etc.).

## Step 3: Check for reusable snippets and components

**CRITICAL:** Before building ANY interactive UI, check what Horizon already provides. The theme has production-grade infrastructure for common patterns. Using existing components ensures consistency, accessibility, and saves significant work.

### Carousel / Slideshow system

Use Horizon's built-in slideshow for any horizontally scrolling content:

- **`snippets/slideshow.liquid`** — Core carousel. Renders `<slideshow-component>` with arrows, pagination, autoplay, infinite loop, and keyboard nav. Params: `slides`, `slide_count`, `show_arrows`, `icon_style`, `icon_shape`, `infinite`, `autoplay`, `slideshow_gutters`.
- **`snippets/slideshow-slide.liquid`** — Individual slide wrapper. Params: `index`, `children`, `class`, `slide_size`.
- **`snippets/slideshow-arrows.liquid`** — Prev/next arrows. Params: `icon_style` (arrow), `icon_shape` (none/circle/square), `arrows_position`.
- **`snippets/slideshow-controls.liquid`** — Pagination dots, counter, or thumbnails. Params: `style` (dots/counter/thumbnails), `item_count`, `show_arrows`.
- **`snippets/resource-list-carousel.liquid`** — Higher-level carousel for resource lists. Wraps slideshow with gutter handling. Params: `ref`, `slides`, `slide_count`, `settings`, `slide_width_max`.
- **`snippets/resource-list.liquid`** — Master layout switcher (grid/bento/carousel/editorial) with automatic mobile carousel fallback. Params: `list_items`, `list_items_array`, `settings`, `content_type`.
- **`assets/slideshow.js`** — Web Component handling scroll, drag/swipe, keyboard nav, infinite loops, autoplay. No manual JS needed.

### Card components

- **`snippets/resource-card.liquid`** — Universal card for products, collections, articles. Overlay link pattern, image hover. Params: `resource`, `resource_type`, `style` (default/overlay), `image_aspect_ratio`, `image_hover`.
- **`snippets/product-card.liquid`** — Product card with quick-add and view transitions. Params: `product`, `children`, `block`.
- **`snippets/collection-card.liquid`** — Collection card with flexible image placement (on-image/below). Params: `collection`, `children`, `card_image`, `block`.
- **`snippets/card-gallery.liquid`** — Product image carousel inside cards with variant switching and badges.

### Layout helpers

- **`snippets/section-header.liquid`** — Title + "View all" link row. Params: `title`, `link_url`, `link_text`.
- **`snippets/image.liquid`** — Responsive image with retina srcsets and focal points. Params: `image`, `height`, `class`, `text_fallback`.
- **`snippets/background-media.liquid`** — Full-bleed background image or video. Params: `background_media`, `background_image`, `background_video`.
- **`snippets/overlay.liquid`** — Solid or gradient overlay. Params: `settings` (overlay_color, overlay_style, gradient_direction).
- **`snippets/button.liquid`** — Styled link button. Params: `link`, `block` (style_class, size).
- **`snippets/icon.liquid`** — Inline SVG icons from Horizon's icon library.
- **`snippets/divider.liquid`** — Horizontal line separator. Params: `id`, `settings`, `full_width`.
- **`snippets/price.liquid`** — Product pricing with compare-at, volume, and unit pricing. Params: `product_resource`, `show_unit_price`.

### Grid layouts

- **`snippets/bento-grid.liquid`** — Asymmetric bento box layout (12-item boxes). Params: `items` (array of HTML).
- **`snippets/editorial-product-grid.liquid`** — Magazine-style asymmetric product grid.
- **`snippets/editorial-collection-grid.liquid`** — Magazine-style collection grid.
- **`snippets/product-grid.liquid`** — Collection/search page product grid with infinite scroll. Params: `section`, `products`, `children`.

### Layout primitives (snippets that generate CSS variables)

- **`snippets/spacing-style.liquid`** — Responsive padding via CSS vars. Params: `settings` (padding-block-start, etc.).
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

**Rule of thumb:** If you're about to write custom JS for scrolling, modals, accordions, popovers, or carousels — stop and use the existing component. If you're about to write a card from scratch — check if `resource-card`, `product-card`, or `collection-card` covers the use case first.

If the same UI pattern will appear in 2+ sections, extract it into a snippet first. Follow the `{%- doc -%}` convention for documenting params.

## Step 4: Build with accessible markup

These are defaults, not afterthoughts:

- **Lists of items:** `<ul role="list">` + `<li>`, or `<nav>` with `aria-label` for navigation lists
- **Clickable cards:** overlay link pattern — `<a>` with `position: absolute; inset: 0; z-index: 1` and `<span class="visually-hidden">` for accessible text
- **Focus states:** `:focus-visible` outline on interactive elements
- **Motion:** wrap hover/transition effects in `@media (prefers-reduced-motion: no-preference)`
- **Images:** always `loading: 'lazy'`, responsive `widths` and `sizes` attributes via Liquid's `image_url` + `image_tag` filter chain

## Step 5: Write the section

Follow this file structure:

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
  /* Reuse base.css classes where they exist (typography, layout, links).
     Write whatever CSS is needed for section-specific behavior. */
{% endstylesheet %}

{% schema %}
{
  "name": "My Section",
  "settings": [],
  "presets": [
    {
      "name": "My Section",
      "category": "Collections"
    }
  ]
}
{% endschema %}
```

### CSS rules

- BEM naming: `.section-name__element--modifier`
- Specificity target: `0 1 0` (single class selectors)
- Use Horizon's CSS custom properties: `var(--color-foreground)`, `var(--font-body--family)`, `var(--card-corner-radius)`, `var(--padding-xs)`, etc.
- Responsive breakpoint: `750px` (Horizon's standard mobile/desktop break)
- Use logical properties: `padding-block`, `margin-inline`, etc.

### Hardcode values (code over configuration)

This project follows "code over configuration." Hardcode known values directly in Liquid — collection handles, section titles, layout choices. Keep `{% schema %}` minimal (empty settings, just name and presets). Theme editor configurability is not a priority.

## Step 6: Register in the template

Add the section to the relevant `templates/*.json` file:

1. Add a section definition in the `sections` object (just `type`, no sprawling settings)
2. Add the section key to the `order` array
3. Validate JSON with `python3 -c "import json; ..."`

Keep template JSON lean — section definitions should be 3 lines, not 100.

## When a section needs JavaScript

Most sections are pure HTML/CSS — scrolling, links, grids, and hover effects all work without JS. Only add JS when the section needs interactive behavior (filtering, dynamic loading, cart actions, etc.).

When JS is needed, use the `{% javascript %}` tag in the section file and follow the Component framework pattern. Read the **shopify-liquid** skill's JS reference at `.claude/skills/shopify-liquid/references/javascript-standards.md` for the full guide:

- Extend `Component` from `@theme/component`
- Use `ref="name"` attributes for DOM references (not `querySelector`)
- Use `on:click="/methodName"` for declarative event binding (not `addEventListener`)
- Use `AbortController` for fetch cleanup on `disconnectedCallback`
- Custom events with `bubbles: true` for component communication
- JSDoc typedefs for all ref objects

## Related skills and references

### Skills (auto-triggered)

- **`shopify-liquid`** — Horizon development standards for Liquid, CSS (BEM), JavaScript (Component framework), schemas, and localization. Consult for CSS property ordering, JS patterns, Liquid syntax rules, and schema editing workflow.
- **`accessibility`** — WCAG standards for interactive elements, forms, navigation, and ARIA patterns. Consult when building anything interactive beyond simple links.

### Theme references

1. **Our own theme's sections/snippets** — the primary reference for how Horizon does things
2. **`assets/base.css`** — search here for existing utility classes before writing custom CSS
3. **`assets/component.js`** — the Component framework source; read for `refs`, `requiredRefs`, and MutationObserver behavior
4. **`references/dawn/`** — Dawn theme for battle-tested collection/product grid patterns
5. **`references/arcticfresh-legacy-theme-export/`** — legacy theme for understanding what the store currently looks like (DO NOT copy code from it)
6. **`references/live-shopify-catalog-context.md`** — live catalog structure, collection handles, product counts

### Shopify docs

Append `.md` to any Shopify docs URL for clean markdown:

- `https://shopify.dev/docs/storefronts/themes/architecture.md` — theme architecture
- `https://shopify.dev/docs/storefronts/themes/architecture/sections.md` — section reference
- `https://shopify.dev/docs/api/liquid.md` — Liquid objects, filters, tags
