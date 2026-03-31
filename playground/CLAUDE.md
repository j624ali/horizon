# Playground

Static HTML/CSS/JS prototypes for visually testing layout ideas, section designs, and UI patterns before building them as Liquid sections.

## Purpose

This folder is a scratch space for rapid visual experimentation. Nothing here ships to the theme — it's for the developer and Claude to iterate on designs in the browser before committing to a Liquid implementation.

## Rules

- **One HTML file per experiment.** Each file is a self-contained prototype — inline CSS and JS, no build step, just open in a browser.
- **Use placeholder images.** Use `https://placehold.co/{width}x{height}/{color}/{text-color}?text={label}` for mock images.
- **Use real data.** Pull department names, collection handles, product counts, and other values from the actual store context (`references/live-shopify-catalog-context.md`) so prototypes feel realistic.
- **Show options side by side.** When comparing layout approaches, use tabs or stacked sections so the user can switch between them in one page.
- **Keep it simple.** Vanilla HTML/CSS/JS only. No frameworks, no npm, no dependencies. The point is speed.
- **Responsive.** Always include mobile breakpoints — Arctic Fresh customers are primarily on phones.
- **Match theme tokens where possible.** Use the Arctic Fresh color palette, font stack, and spacing values from `config/settings_data.json` so prototypes approximate the real look.

## Naming

Name files by what they're testing:

- `collection.html` — department/collection grid layouts
- `hero.html` — hero banner variations
- `product-card.html` — card design options
- `navigation.html` — nav/menu patterns

## Cleanup

Delete prototype files once the corresponding Liquid section is built and approved. This folder should not accumulate stale experiments.
