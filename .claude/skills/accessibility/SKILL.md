---
name: accessibility
description: WCAG accessibility standards for Shopify theme components. Use when creating or modifying UI components, interactive elements, forms, navigation, or any user-facing HTML/CSS/JS.
user-invocable: false
paths:
  - "**/*.liquid"
  - "assets/**/*.css"
  - "assets/**/*.js"
---

# Accessibility Standards

All theme components must meet WCAG 2.1 AA compliance.

## Global Requirements

- HTML `lang` attribute on root element
- Viewport meta must allow zooming (no `maximum-scale=1.0` or `user-scalable=no`)
- Skip link as first element in document body
- All iframes must have descriptive `title` attributes
- Avoid `title` attribute on non-iframe elements — use visible text, `aria-label`, or custom tooltips
- Maintain 4.5:1 contrast ratio for normal text, 3:1 for large text
- All interactive elements must have visible focus indicators (`:focus-visible`)
- Respect `prefers-reduced-motion` for all animations

## Key Principles

1. **Keyboard accessible** — all functionality operable via keyboard
2. **Screen reader compatible** — semantic HTML, proper ARIA roles and labels
3. **Focus management** — logical tab order, focus trapping in modals, skip links
4. **Motion safe** — respect `prefers-reduced-motion`, provide animation alternatives

## Component-Specific References

Consult these detailed guides when building or modifying specific components:

- [references/global-accessibility-standards.md](references/global-accessibility-standards.md) — Page language, viewport, skip links, title attributes
- [references/accordion-accessibility.md](references/accordion-accessibility.md)
- [references/animation-accessibility.md](references/animation-accessibility.md)
- [references/breadcrumb-accessibility.md](references/breadcrumb-accessibility.md)
- [references/carousel-accessibility.md](references/carousel-accessibility.md)
- [references/cart-drawer-accessibility.md](references/cart-drawer-accessibility.md)
- [references/chat-window-accessibility.md](references/chat-window-accessibility.md)
- [references/color-contrast-accessibility.md](references/color-contrast-accessibility.md)
- [references/color-swatch-accessibility.md](references/color-swatch-accessibility.md)
- [references/combobox-accessibility.md](references/combobox-accessibility.md)
- [references/disclosure-accessibility.md](references/disclosure-accessibility.md)
- [references/dropdown-navigation-accessibility.md](references/dropdown-navigation-accessibility.md)
- [references/flip-card-accessibility.md](references/flip-card-accessibility.md)
- [references/focus-order-and-styles-accessibility.md](references/focus-order-and-styles-accessibility.md)
- [references/form-accessibility.md](references/form-accessibility.md)
- [references/heading-accessibility.md](references/heading-accessibility.md)
- [references/image-alt-text-accessibility.md](references/image-alt-text-accessibility.md)
- [references/landmark-accessibility.md](references/landmark-accessibility.md)
- [references/mobile-accessibility-standards.md](references/mobile-accessibility-standards.md)
- [references/modal-accessibility.md](references/modal-accessibility.md)
- [references/product-card-accessibility.md](references/product-card-accessibility.md)
- [references/product-filter-accessibility.md](references/product-filter-accessibility.md)
- [references/product-media-gallery-accessibility.md](references/product-media-gallery-accessibility.md)
- [references/sale-price-accessibility.md](references/sale-price-accessibility.md)
- [references/slider-accessibility.md](references/slider-accessibility.md)
- [references/switch-accessibility.md](references/switch-accessibility.md)
- [references/tab-accessibility.md](references/tab-accessibility.md)
- [references/table-accessibility.md](references/table-accessibility.md)
- [references/tooltip-accessibility.md](references/tooltip-accessibility.md)
