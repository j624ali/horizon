---
paths:
  - "**/*.liquid"
  - "**/*.css"
  - "**/*.js"
  - "blocks/**"
  - "sections/**"
  - "snippets/**"
  - "assets/**"
---

# Code style — Liquid, CSS, JS

Project conventions for Arctic Fresh's Horizon theme. Skill files in `.claude/skills/shopify-liquid/` and `.claude/skills/accessibility/` are authoritative for general Horizon and accessibility patterns; this file captures the project-specific deltas.

## CSS

- **BEM naming** — `.block__element--modifier`. Specificity target `0 1 0`. Never IDs.
- **Scoping** — for stock Horizon blocks/sections/snippets, scope via `{% stylesheet %}`. For new Arctic Fresh sections, see the Shortcut Doctrine in `CLAUDE.md`.
- **CSS custom properties** — namespace per component (`--card-corner-radius`, not bare `--radius`).
- **Logical properties** for RTL support (`padding-inline-start`, not `padding-left`).
- **Color tokens** — reach for `--color-arctic-*` role tokens (`--color-arctic-surface-muted`, `--color-arctic-ink-strong`, `--color-arctic-border-hairline`, `--color-arctic-mint-wash`, etc.) defined in `snippets/theme-styles-variables.liquid`. **Do NOT** reach for stock Horizon scheme-scoped tokens (`--color-foreground-muted`, `--color-border`, `--color-background`) directly in component CSS — their names are functional, not visual (e.g. `--color-foreground-muted` is foreground @ 60% opacity, not a soft fill). If a needed role isn't defined in `theme-styles-variables.liquid`, add it there rather than abusing a stock token.

## Liquid

- **`{% render %}` over `{% include %}`** — `{% include %}` is deprecated.
- **`{% liquid %}`** for multiline blocks.
- **`{% doc %}`** for snippet params.
- **Inline Liquid** preferred over variable declarations where it stays readable.
- **Schemas** — edit `{% schema %}` inline in `.liquid` files. Keep minimal — usually empty `settings`, just `name` and `presets`. Add settings only when the user explicitly asks.

### Stylesheet extraction pattern

When forking a stock Horizon `.liquid` file that has a large `{% stylesheet %}` block, **extract that block into a sibling `*-styles.liquid` snippet first, then fork the markup.** This matches Horizon's own pattern (`predictive-search-styles.liquid`, `slideshow-styles.liquid`) and keeps forks small enough to diff cleanly against vanilla.

The original file then renders the styles via `{% render 'thing-styles' %}` in its `{% stylesheet %}` block (or directly via `{% style %}` depending on Horizon's convention for that file).

Skip extraction for trivial style blocks (under ~20 lines).

## JS

- **`const` over `let`.** `let` only when reassignment is required.
- **`for...of` over `.forEach()`** — better stack traces, cleaner control flow.
- **Web Components** — for stock Horizon blocks, extend the `Component` framework in `assets/component.js` (refs, declarative event binding, lifecycle). For new Arctic Fresh sections, plain `class extends HTMLElement` is preferred — see Shortcut Doctrine in `CLAUDE.md`.
- **Declarative event binding** when using `Component` — `on:click="/method"`.
- **JSDoc typedefs for refs** when using `Component`.
- **`AbortController`** for fetch cleanup in long-lived components.
- **No external dependencies.** No npm packages. JS must stay under 16KB minified per file.
