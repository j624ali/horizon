# Header & Navigation Research — Arctic Fresh

Research compiled 2026-04-29 to lock down header design decisions for the Horizon theme build. Focus: online grocery, large catalog (9,000+ SKUs), mobile-heavy audience on limited bandwidth, Nunavut delivery zones with community-specific pricing.

---

## Top 5 evidence-backed conclusions

1. **Mega menus are the dominant pattern for large-catalog ecommerce.** Baymard 2025: 76% of large sites use them (up from 54% in 2019), 88% of top US sites. Hamburger-only halves discoverability per NN/g (desktop usage 27% vs 48–50% visible; users 39% slower).
2. **Bottom-tab nav wins on mobile apps (32.8% faster task completion vs hamburger), but on mobile *web* the slide-out drawer remains the standard** — bottom tab bars compete with browser chrome and Safari's bottom URL bar.
3. **Location/community selectors belong in the persistent header + a first-visit prompt.** Walmart, Instacart, Kroger, Loblaws, Whole Foods, FreshDirect, Amazon Fresh all do this; cart-only placement is universally rejected.
4. **Predictive search is table stakes and measurably converts.** Search users convert ~50% higher; autocomplete adds ~24% conversion and cuts keystrokes 25%. Use ARIA combobox pattern, not plain input.
5. **Slim sticky header (≤50–60px) is the 2025-2026 consensus, implemented with `position: sticky` to avoid CLS hits.** Reported +3% conversion, ~36s task-time savings; oversized sticky chrome causes 20–30% satisfaction drop.

## Surprising findings

- **Search bar prominence is *declining* in some 2026 redesigns** — not because search is less important, but because shoppers increasingly arrive with intent already formed via ChatGPT/Perplexity/Google AI Overviews. AI-referred visitors convert **23x higher** than traditional organic; structured product data (Schema.org, real-time pricing) is now a higher leverage point than header-search polish. For arrived shoppers, prominent + predictive search is still required.
- **Baymard flagged Kroger** for excessively tall horizontal-scroll components (>50% of viewport) hijacking vertical scroll — a friction pattern to avoid even when copying their general structure.
- **40% of mobile ecommerce apps fail to provide a visible "Shop"/"Categories" path in bottom nav** — Baymard reports users abandon thinking the catalog is empty.
- **Only 23% of users select autocomplete suggestions** (NN/g) — they still refine traditional results often, so autocomplete must coexist with a strong results page, not replace it.
- **WCAG 2.2's new SC 2.4.11 (Focus Not Obscured)** directly affects sticky headers — focused elements must not be fully hidden behind the sticky bar. Set `scroll-padding-top` to header height.

---

## 1. Tier structure for grocery headers

**Industry pattern: most leading grocery sites use 2-tier or 3-tier headers; Instacart and Whole Foods are explicitly 3-tier.**

| Site | Tiers | Composition |
|---|---|---|
| Instacart | 3-tier | Top utility bar (links) → main (logo, search, cart, ZIP) → departments menu |
| Whole Foods (Amazon) | 3-tier | Utility ("Pickup & Delivery") → main brand/search → sales highlights |
| Walmart Grocery | 2-tier | Hero promos + main nav |
| Loblaws / PC Express | 2-tier | Store dropdown at top + main nav |
| FreshDirect | 2-tier | Service-area prompt + main |

**Tradeoffs:**

- **3-tier:** Promo space + persistent department access aids discovery in large catalogs (Instacart explicitly caps at three department levels for simplicity). Risks clutter on mobile and pushes content below the fold.
- **2-tier:** Faster scanning, better for repeat-order flows; sacrifices announcement/promo real estate.
- **1-tier compact:** Only viable when search dominates discovery (Amazon-style); inadequate for grocery's browsing-heavy patterns.

Sticky main + collapsible departments is the optimization sweet spot — keeps promo + nav accessible without permanent vertical cost.

## 2. Mega menu vs flat nav vs hamburger (large catalog)

**Mega menus win for 9,000+ SKU sites; hamburger menus halve discoverability; flat nav fails at scale.**

Baymard 2025: **76% of large ecommerce sites use mega menus** (up from 54% in 2019); **88% of top US sites** use hover-based mega drop-downs. Baymard's online grocery audit explicitly recommends mega drop-downs for vast hierarchies.

NN/g hidden-vs-visible discoverability data:

- Desktop usage: hamburger 27% vs visible nav 48–50%
- Mobile usage: hamburger 57% vs combo (visible + hamburger) 86%
- Users **39% slower on desktop tasks** with hidden navigation

**Implementation requirements (Baymard) for mega menus:**

- Hover delay 300–500ms to prevent flicker
- Clickable category headers (not hover-only)
- Subcategory thumbnails — 55% of sites fail this
- ~10-item chunks per panel
- Highlight current scope (95% of sites fail this)

Baymard rates **58% of desktop and 67% of mobile sites "mediocre" or "poor"** on homepage/navigation UX.

## 3. Mobile navigation patterns

**Bottom tab bar is winning in native grocery apps; for mobile *web*, slide-out drawer is the established pattern.**

Quantitative findings:

- Bottom-bar nav: **32.8% faster task completion** vs hamburger
- Optimal: 3–5 items in bottom tab bar
- Baymard: **40% of mobile apps fail to provide visible "Shop" / "Categories"** in bottom nav — users abandon thinking the catalog is empty
- Baymard: **71% of ecommerce mobile apps "mediocre" or worse**; only 29% "decent", none "good"

| Pattern | Best for | Verdict for grocery |
|---|---|---|
| Bottom tab bar | 3–5 destinations, thumb access | Winner for native apps; awkward on mobile web |
| Slide-out drawer | Editorial / secondary links | Standard for mobile web; acceptable as primary if dept rail stays visible |
| Bottom sheet | Filters, contextual actions | Not for primary nav |
| Full-screen overlay | Multi-step tasks, auth | Inappropriate for routine browse |

Caveat: Baymard flagged Kroger for **horizontal-scroll components occupying >50% of viewport** that hijack vertical scroll — friction pattern to avoid.

## 4. Community / zip / store selector placement

**Best practice: prominent always-visible header placement + first-visit prompt. Cart-only placement is universally rejected.**

| Site | Placement | First-visit |
|---|---|---|
| Walmart Grocery | Header (location menu, top-left) | Prompts ZIP on entry |
| Instacart | Header (top ZIP); compact mobile dropdown | Auto-detect + map switch |
| Kroger | Top-right corner | First-visit prompt |
| Loblaws / PC Express | Top dropdown arrow | Forces store selection |
| Whole Foods | Header top | ZIP first, then store |
| FreshDirect | Initial delivery-area prompt | Service area gates entry |
| Amazon Fresh | Header / service-area selector | ZIP-gated |

Pair with explicit availability indicators (delivery estimate, eligible/ineligible) to build trust.

Arctic Fresh implication: community is more determinative than ZIP elsewhere — sets pricing, subsidy, availability. Prominent + persistent + mandatory-on-first-visit is the right pattern.

## 5. Search prominence and predictive search

**Highly prominent + predictive autocomplete is the dominant pattern with measurable conversion lift.**

- Search users convert **~50% higher** than average ecommerce visitors (Baymard)
- **69% of shoppers use search first** in general ecommerce; poor results trigger **80% abandonment**
- Autocomplete cuts keystrokes **25%**, boosts conversion **~24%**
- NN/g: ecommerce search success now **92%** with better autocomplete; suggestion selection only **23%** (users still refine traditional results)
- Comparable case: Nespresso predictive search lifted conversions **18%**

Baymard recommendations for autocomplete:

- Live results as you type
- Mix products + scopes + refinements
- Keyboard nav through suggestions
- Always copy active suggestion to the search field
- Include "Search within current category" — **94% of sites fail this**

**2025-2026 AI-search shift:** search bar prominence is *declining* in some redesigns because shoppers arrive via ChatGPT/Perplexity/Google AI Overviews with intent formed (AI-referred visitors convert **23x higher** than traditional organic). Strategic implication: structured product data outranks header-search polish — but for the arrived shopper, prominent + predictive is still table stakes.

## 6. Sticky behavior

**Slim, always-visible sticky header is the dominant 2025-2026 pattern. Scroll-up-to-reveal is gaining ground.**

| Pattern | UX | Performance |
|---|---|---|
| Full sticky on scroll | Always-visible nav/cart/search; reduces scroll-back friction; reported +3% conversion | `position: sticky` avoids CLS; fixed can shift layout |
| Collapse-to-compact | Saves vertical space; preserves access | Animations add minor repaint cost |
| No-sticky | Maximizes content; best perf | Forces scroll-back; ~36s task time penalty per study |
| Scroll-up-to-reveal | Hides on down, reveals on up; "intent-based" | Trending in 2026; can confuse if too subtle |

NN/g rule of thumb: sticky header height ≤ 50–60px; high contrast against page content. Oversized sticky chrome → 20–30% satisfaction drop.

Web.dev: prefer CSS `position: sticky` over `position: fixed` — avoids CLS hits to Core Web Vitals.

## 7. Accessibility patterns (non-obvious)

### WCAG 2.2 newer success criteria affecting headers

- **2.4.11 Focus Not Obscured (AA):** focused element must not be fully covered by sticky header. Use CSS `scroll-padding-top` matching sticky-header height so anchor jumps and Tab navigation aren't hidden.
- **2.4.13 Focus Appearance (AAA):** focus indicators ≥ 2px thick, ≥ 3:1 contrast against surroundings.

### Skip links

- Place immediately after `<body>`. Visually hidden, becomes visible on focus.
- Mobile reality: skip links work for keyboard/switch users but **do not work with screen-reader swipe gestures**. Still required.
- Consider multiple targets (skip to main, skip to navigation, skip to footer).

### Mobile drawer focus management

- **Trap focus inside drawer** when open — Tab/Shift+Tab cycle within drawer only.
- **Restore focus to the trigger button** when drawer closes.
- Use `inert` attribute on background content (modern; replaces aria-hidden + tabindex dance).
- Toggle `aria-expanded` on the trigger button.

### Search input — combobox pattern

For predictive search the input is **not a plain `<input>`** — it's a combobox per ARIA APG:

- `role="combobox"` with `aria-haspopup="listbox"`, `aria-expanded` toggled on focus/results
- `aria-controls` pointing to listbox ID
- `aria-activedescendant` for arrow-key highlight (don't move DOM focus into the listbox)
- `aria-autocomplete="list"`
- Visible `<label>` (not just placeholder)
- `aria-live="polite"` region announcing result count / "no results"
- Keyboard: Arrow Up/Down navigate, Enter selects, Escape closes

### Mega menu keyboard navigation

Beyond Tab — implement WAI-ARIA menu pattern:

- Right/Left Arrow: between top-level items
- Down Arrow on top-level: opens submenu, focus first item
- Enter/Space: open submenu
- Escape: close submenu, focus parent
- Type-ahead: jump to items by first character

### Community selector announcements

Switching community changes pricing and availability across the page — announce via `aria-live="polite"` (e.g., "Community changed to Iqaluit. Prices updated."). Use `assertive` only if changes are critical/blocking.

### Semantics

- `<header>` for page header
- `<nav aria-label="Primary">` for main nav, `<nav aria-label="Departments">` for second nav (multiple navs require unique labels)
- `<main>` for content area (skip-link target)
- Form controls in header use `<label for>` — never placeholder-as-label

---

## Recommendations for Arctic Fresh

1. **3-tier header on desktop, 2-tier on mobile.** Top: announcement + community selector. Main: logo, search, cart, account. Third: department mega menu (desktop) / horizontal scroll dept rail (mobile). Mirrors Instacart/Whole Foods.
2. **Mega menu on desktop, drawer on mobile.** Pure flat fails at 9k SKUs; pure hamburger halves discoverability. Mega menu requires subcategory thumbnails + clickable headers + ~10 items per panel.
3. **Mobile: slide-out drawer from hamburger, with a persistent department rail under the main bar.** On mobile web a bottom tab bar competes with browser chrome — drawer is the proven Shopify pattern.
4. **Community selector: persistent in top utility bar + mandatory first-visit modal.** Mirrors Walmart/Kroger/FreshDirect and matches Northbound's data model.
5. **Search: prominent and centered desktop, full-width under main header on mobile, with predictive autocomplete (ARIA combobox).** ~50% higher conversion + ~24% from autocomplete.
6. **Sticky main bar (logo + search + cart) at ~56–64px via `position: sticky`.** Announcement + departments scroll away. Skip scroll-up-to-reveal — adds JS, can confuse older shoppers.
7. **Accessibility floor: skip link, focus trap on drawer, combobox ARIA on search, WAI-ARIA menu pattern on mega menu, `scroll-padding-top` matching sticky height, `aria-live` for community changes.** WCAG 2.2 AA, not stretch.

## Sources

- [Homepage & Navigation UX Best Practices 2025 — Baymard](https://baymard.com/blog/ecommerce-navigation-best-practice)
- [Online Grocery Ecommerce UX Audit Findings — Baymard](https://baymard.com/audits/online-grocery)
- [Mobile UX Trends 2025 — Baymard](https://baymard.com/blog/mobile-ux-ecommerce)
- [Ecommerce Mobile App UX Trends 2026 — Baymard](https://baymard.com/blog/mobile-app-ux-trends)
- [Mobile App UX Benchmark 2026 — Baymard](https://baymard.com/blog/mobile-app-ux-benchmark-2026)
- [Make Product Categories the Top-Level Navigation Items on Mobile — Baymard](https://baymard.com/blog/main-navigation-product-categories)
- [E-Commerce Search Field Design and Its Implications — Baymard](https://baymard.com/blog/search-field-design)
- [Allow Users to 'Search Within' Their Current Category (94% Don't) — Baymard](https://baymard.com/blog/search-within-current-category)
- [Always Copy the Active Autocomplete Suggestion to the Search Field — Baymard](https://baymard.com/blog/copy-search-suggestion-to-search-field)
- [E-Commerce Search Usability Research Studies — Baymard](https://baymard.com/research/ecommerce-search)
- [Year in Review 2025 and 2026 Roadmap — Baymard](https://baymard.com/blog/year-in-review-2025-and-2026-roadmap)
- [Responsive Upscaling: Large-Screen E-Commerce — Baymard](https://baymard.com/blog/responsive-upscaling)
- [Traditional and Hybrid Category Pages — NN/g](https://www.nngroup.com/articles/category-pages/)
- [Custom header — Instacart Docs](https://docs.instacart.com/storefront/learn_about_your_storefront/shopping/custom_header/)
- [Departments — Instacart Docs](https://docs.instacart.com/storefront/learn_about_your_storefront/shopping/departments/)
- [Choose a Store — PC Express](https://www.pcexpress.ca/choose-a-store)
- [Walmart Store Finder](https://www.walmart.com/store-finder)
- [Kroger Eligible Zip Codes](https://www.kroger.com/i/delivery-early-access/eligible-zip-codes)
- [FreshDirect Delivery Information](https://www.freshdirect.com/help/delivery_info)
- [What is a Sticky Header? UX Best Practices & 2026 Design Guide — Parallel](https://www.parallelhq.com/blog/what-sticky-header)
- [Sticky Header Design: Best Practices — Pro-Prestashop](https://www.pro-prestashop.com/sticky-header-design-best-practices-examples-and-common-mistakes-to-avoid/)
- [Should navigation bars be sticky or fixed? — LogRocket](https://blog.logrocket.com/ux-design/sticky-vs-fixed-navigation/)
- [Tab Bar VS Hamburger Menu — Conflux](https://www.weareconflux.com/en/blog/tab-bar-vs-hamburger-menu)
- [The Future of Mobile Navigation: Hamburger vs Tab Bars — Acclaim](https://acclaim.agency/blog/the-future-of-mobile-navigation-hamburger-menus-vs-tab-bars)
- [How AI Is Redefining Search, Discovery, and Ecommerce Growth — Nudge](https://www.nudgenow.com/blogs/ai-search-transforming-shopping-discovery)
- [ChatGPT vs Perplexity vs Google AI Mode: Merchant Guide — Alhena](https://alhena.ai/blog/ai-shopping-platforms-comparison-chatgpt-perplexity-gemini/)
- [E-Commerce Accessibility 2025: WCAG Compliance Guide — AllAccessible](https://www.allaccessible.org/blog/ecommerce-accessibility-complete-guide-wcag)
- [Ensure All Skip Links Have a Focusable Target — Accessibility Checker](https://www.accessibilitychecker.org/wcag-guides/ensure-all-skip-links-have-a-focusable-target/)
- [Accessible Mega Menu — BSK reference implementation](https://www.bsk.com/assets/js/accessible-mega-menu-master-orig/)
- [Autocomplete vs Predictive Search — Monetate](https://monetate.com/resource/autocomplete-vs-predictive-search-what-is-the-difference/)
