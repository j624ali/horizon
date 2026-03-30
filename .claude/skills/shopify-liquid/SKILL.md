---
name: shopify-liquid
description: Shopify Horizon theme development standards for Liquid, HTML, CSS, JavaScript, sections, blocks, snippets, schemas, templates, and localization. Use when writing or editing any theme code.
user-invocable: false
paths:
  - "**/*.liquid"
  - "assets/**"
  - "schemas/**"
  - "locales/**"
  - "templates/**"
  - "config/**"
---

# Horizon Theme Development Standards

## Critical: Schema Editing

**NEVER edit the `{% schema %}` block directly in `.liquid` files!**

Schemas are generated from source files in the `schemas/` folder:
1. Find the corresponding `.js` file in `schemas/blocks/` or `schemas/sections/`
2. Edit the JavaScript source file
3. Run `npm run build:schemas` to regenerate the `.liquid` files

## Core Conventions

### Liquid
- Use `{% liquid %}` for multiline code blocks
- Use `{% # comment %}` for inline comments
- Never invent new filters, tags, or objects
- Prefer inlining Liquid over declaring extra variables for simple props
- All snippets must include `{% doc %}` documentation
- Follow proper tag closing order (last opened, first closed)
- Use object dot notation: `product.title` not `product['title']`

### HTML
- Use semantic HTML elements
- Use native `<details>`, `<dialog>`, `popover` over custom JS
- Use CamelCase for IDs with section/block identifiers: `id="FeaturedCollection-{{ section.id }}"`
- Progressive enhancement: start with semantic HTML, layer CSS, add JS
- Use client-side form validation with native HTML5 attributes

### CSS (BEM Convention)
- Block: `.product-card`, Element: `.product-card__title`, Modifier: `.product-card--featured`
- Never use IDs as selectors, avoid `!important`
- Target `0 1 0` specificity (single class selector)
- Use CSS variables scoped to components, namespaced: `--component-padding`
- Use logical properties (`padding-inline`, `margin-block`) for RTL support
- Use inline `style` attributes for section/block-scoped CSS variable values
- Mobile first with `min-width` media queries
- Never nest beyond first level (except media queries/states)
- Property order: Layout > Box Model > Typography > Visual > Animation

### JavaScript
- Zero external dependencies — use native browser APIs
- Use the Component framework (`import { Component } from '@theme/component'`)
- Web Components pattern with `customElements.define()`
- Async/await over `.then()` chaining
- JSDoc type annotations for all components and functions
- Early returns over nested conditionals
- Use `const` over `let`, `for...of` over `.forEach()`

### Localization
- Every user-facing text must use `{{ 'key' | t }}` translation filter
- Update `locales/en.default.json` with all new keys
- Use descriptive, hierarchical keys (max 3 levels deep, snake_case)
- Only add English text — translators handle other languages
- Use interpolation rather than appending strings together

## Detailed References

When working in specific areas, consult these detailed guides:

- **CSS**: [references/css-standards.md](references/css-standards.md) — Specificity, BEM, variables, nesting, media queries, layout patterns
- **JavaScript**: [references/javascript-standards.md](references/javascript-standards.md) — Component framework, web components, event architecture, error handling
- **Liquid syntax**: [references/liquid.md](references/liquid.md) — Valid tags, filters, syntax rules, doc tags, inline variables
- **HTML**: [references/html-standards.md](references/html-standards.md) — Native elements, modern CSS features, progressive enhancement
- **Sections**: [references/sections.md](references/sections.md) — Section structure, requirements, performance patterns
- **Blocks**: [references/blocks.md](references/blocks.md) — Theme blocks, static blocks, nested blocks, CSS scoping
- **Snippets**: [references/snippets.md](references/snippets.md) — Documentation, parameter handling, common patterns
- **Schemas**: [references/schemas.md](references/schemas.md) — Schema structure, setting types, label guidelines, translation keys
- **Templates**: [references/templates.md](references/templates.md) — JSON template structure, template types
- **Locales**: [references/locales.md](references/locales.md) — File structure, key organization, translation guidelines
- **Localization**: [references/localization.md](references/localization.md) — Translation filters, variable usage
- **Assets**: [references/assets.md](references/assets.md) — Asset directory and usage
- **Theme settings**: [references/theme-settings.md](references/theme-settings.md) — Settings schema structure

## Examples

- [examples/section-example.liquid](examples/section-example.liquid)
- [examples/block-example-text.liquid](examples/block-example-text.liquid)
- [examples/block-example-group.liquid](examples/block-example-group.liquid)
- [examples/snippet-example.liquid](examples/snippet-example.liquid)
