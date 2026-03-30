# Horizon Theme

Shopify Horizon theme (v3.4.0) using Liquid templating, web components, and Shopify's theme architecture.

## Project Structure

- `assets/` — CSS, JS, and image files (flat directory, no subdirectories)
- `blocks/` — Reusable theme blocks (`.liquid`)
- `config/` — Theme settings (`settings_schema.json`, `settings_data.json`)
- `layout/` — Theme layouts
- `locales/` — Translation files (JSON)
- `schemas/` — Schema source files (JS/TS) compiled into liquid `{% schema %}` blocks
- `sections/` — Section templates (`.liquid`)
- `snippets/` — Reusable snippet components (`.liquid`)
- `templates/` — Page templates (JSON)

## Critical Workflow: Schemas

**Never edit `{% schema %}` blocks directly in `.liquid` files.** Schemas are generated from JS source files:

1. Edit files in `schemas/blocks/` or `schemas/sections/`
2. Run `npm run build:schemas` to regenerate

## Shopify Documentation

You can fetch Shopify docs as markdown by appending `.md` to any Shopify docs URL. Starting points:

- https://shopify.dev/docs/storefronts.md
- https://shopify.dev/docs/storefronts/themes.md
