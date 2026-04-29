# Horizon Notes

Project-internal knowledge base for working with Shopify Horizon on this theme. Captures gotchas, working patterns, and non-obvious behavior so fresh Claude sessions don't re-discover the same lessons. Append to this as you learn — short entries are fine.

---

## Established patterns

### Dummy-data-first when admin state is uncertain

When building a section that depends on collection / product / metafield state in admin, render against **hardcoded placeholder data first**, then swap to live data once you've verified admin is correctly populated. The two reasons:

1. You can iterate on layout without depending on admin uploads (especially images).
2. Filters that hide elements when data is missing (`{% if collection.image != blank %}`) will silently produce an empty section if data is missing — which looks like the section is broken even though it's working as designed.

**Pattern:** keep both versions in the file, dummy active and live commented. Add a comment at the top noting which mode it's in and the steps to switch.

**Example in repo:** `sections/department-shortcuts.liquid` — see the `DEV PREVIEW MODE` header comment and the `LIVE DATA VERSION` block.

### `collection.image` vs `collection.featured_image`

For collection-card / collection-chip UIs that should ONLY show collections with an admin-uploaded image:

- `collection.image` — admin-uploaded collection image. **Blank if none.** This is what you want for filtering.
- `collection.featured_image` — falls back to the first product's image if no collection image is set. This will let imageless collections render with a random product photo, which usually looks wrong on a chip / card grid.

Filter on `collection.image != blank`, not `featured_image`.

**Subtle gotcha:** the Image drop is truthy in `if` even when empty (`{% if collection.image %}` passes for blank). Always use `!= blank` explicitly.

### `.section-background` + `.section` is the full-bleed pattern

Horizon's idiomatic way to render a full-bleed background under page-width content is two stacked divs:

```liquid
<div class="section-background color-scheme-1"></div>
<div class="section section--page-width color-scheme-1 my-section">…</div>
```

Reference: `sections/marquee.liquid`. Don't reinvent with absolute positioning or container queries.

### Render-time filters for incomplete admin data

Always design Liquid for the case where the admin data is missing or partial (`{% if collection.id and collection.image != blank %}`). Easier than trying to enforce admin completeness, and fails gracefully when the catalog changes.

### Pure-CSS scroll-snap for compact horizontal rows

For chip-scale or card-row horizontal scrollers, plain `overflow-x: auto` + `scroll-snap-type: x proximity` is enough. Don't reach for Horizon's `slideshow.liquid` — it's overkill for anything smaller than full-width slides.

### `prefers-reduced-motion`: opt-in, not opt-out

Wrap motion *inside* `@media (prefers-reduced-motion: no-preference)` rather than disabling motion in `@media (prefers-reduced-motion: reduce)`. The former is the safer default — motion only applies for users who haven't expressed a preference against it.

### Verifying admin state with `admin_gql`

Use `~/.local/bin/admin_gql` (documented in CLAUDE.md → "Live Store Recon") to confirm which collection handles exist and which have admin-uploaded images before relying on them in Liquid.

```bash
admin_gql 'query { collectionByHandle(handle: "fruits-vegetables") { handle title image { url } productsCount { count } } }' | jq .
```

If `image` is null, the collection has no admin-uploaded image (only product fallbacks).

**Confirmed live collection handles (verified 2026-04-29):** `meat`, `fruits-vegetables`, `dairy-eggs`, `pantry`, `frozen-food`, `drinks`, `home-lifestyle`, `health-beauty-pharmacy`. **Baby & Kids has no umbrella collection** — needs creating in admin if it should appear on the homepage department strip.

---

## Performance / bandwidth-aware defaults

Many Arctic Fresh customers shop on slow, expensive northern internet. These defaults push back against any "rich web" instincts. Sourced from production patterns in bandwidth-constrained ecommerce (Jumia, Mercado Libre, Flipkart Lite, Walmart emerging-markets work).

### Render text first, image second

Product title and price should be in the server-rendered HTML before the image loads. A customer on 3G sees the price in <1s even if the image takes 3-5s. Don't build cards that depend on JS or image-loaded callbacks for primary content.

### Use LQIP (low-quality image placeholder), not skeletons

For product images, prefer a 20–50px base64-inlined preview that blurs up to the full image, over animated skeleton blocks. Lower payload and renders before any JS runs. Verify `snippets/image.liquid` is emitting LQIP-ready markup; extend it if not. Solid background colors matching the dominant image color are also fine — better than animated skeletons.

### Skip animations on the critical path

- Hero autoplay slideshows: **off by default.** Autoplay preloads multiple slide images, which is real bandwidth.
- No parallax, no above-the-fold carousel auto-advance.
- Card hover effects: a 2px translate is fine; skip shadow-bouncing or scale transforms beyond very subtle.
- Existing `prefers-reduced-motion: no-preference` opt-in pattern still applies — but be conservative about adding motion in the first place.

### Server-rendered over client-side where possible

- Use Shopify's URL-based collection filtering instead of client-side filter JS.
- Use `{% paginate %}` server pagination over client-side infinite scroll where the page can support it.
- Hydrate JS only for genuinely interactive components (community selector, cart drawer, quick-add).

### Font loading

Horizon ships `font-display: swap` by default — correct. Subset web fonts to Latin only; don't ship full extended-glyph files. If syllabics get added (e.g. for community names), load them as a separate small subset (Noto Sans Canadian Aboriginal works) only on pages that need them. Consider `font-display: optional` for body text if a chosen face feels even slightly oversized — system fallback (Helvetica/Arial/system-ui) is not a tragedy on a grocery site.

### Why this matters here

Defaults that are fine for southern Canadian customers can be punishing on northern bandwidth. The Lighthouse target in `CLAUDE.md` (Decision Principle #4) is 60+; aim higher when work supports it.

---

## Lessons learned

### `shopify theme console` is barely useful for catalog recon

The Liquid REPL doesn't accept `{{ }}`, `collections["handle"]`, or `collections.handle`. Only workaround is launching with `--url /collections/<handle>` to bind the `collection` global. Slow, not scriptable. **Use `admin_gql` for any catalog/data investigation;** keep `theme console` only for testing whether a single Liquid expression evaluates.

### `{% render 'image' %}` expects a Shopify Image drop, not a URL string

If you want to render a placeholder URL (e.g. `placehold.co/...`) during dev-preview, use a plain `<img src="...">`. The `image` snippet will fail or render nothing for string URLs.

### `templates/index.json` opens with a `/* … */` comment block

The auto-generated warning at the top of `templates/index.json` is a JS-style comment. Some JSON parsers will reject it — strip the comment before parsing programmatically. **Hand-editing the file is fine** despite the warning (Decision Principle #1: code over configuration). Pick stable, hand-chosen section keys; the theme editor may rewrite them later, that's expected.

### `assets/base.css` already defines common tokens

Before declaring a CSS custom property, grep `assets/base.css`. It already includes `--padding-sm`, `--page-margin`, `--focus-outline-width`, `--focus-outline-offset`, `--card-corner-radius`, and the `.h6` typography class. Redefining these locally creates drift.

### Safari strips implicit list role from unstyled `<ul>`

If you remove default list styling (`list-style: none`), Safari's screen reader stops announcing it as a list. Add `role="list"` explicitly on the `<ul>` to keep accessibility intact.

---

## Avoid

- **Schema settings on speculation.** Start every section/block with `settings: []`. Add settings only when the user explicitly asks for them. Decision Principle #1.
- **Treating `shopify theme check` as a behavior gate.** It's syntax-only — won't catch layout bugs, broken Liquid logic, or data binding issues. Always also load the page in the dev server and visually inspect.
- **Adding a Web Component / `Component` framework usage when no JS is needed.** Horizon ships the framework but that doesn't mean every section needs to extend it. If the section is pure CSS and Liquid, leave it that way.

---

## Open questions / things to verify

- Horizon's `Component` framework (`assets/component.js`) — confirm the lifecycle order (constructor → connectedCallback → ref population) when nesting Web Components.
- App blocks inside theme blocks — Horizon docs say `@app` is supported in block schemas; verify the app extension actually mounts correctly when nested several levels deep.
- Performance impact of multiple `{% stylesheet %}` blocks per section — does Horizon dedupe, or does each section ship its own CSS?

---

## Useful Horizon source files to read

When unsure how to do X in Horizon, read these before writing custom:

- `snippets/resource-list.liquid` — universal grid/carousel/bento/editorial layout switcher
- `snippets/resource-card.liquid` — universal card primitive (used by product/collection/article cards)
- `snippets/slideshow.liquid` (+ `slideshow-slide.liquid`, `slideshow-arrows.liquid`, `slideshow-controls.liquid`) — full carousel
- `snippets/image.liquid` — responsive images
- `snippets/section-header.liquid` — title + "View all" pattern
- `assets/component.js` — Web Component base class (refs, event binding, lifecycle)
- `blocks/group.liquid` — the foundational layout primitive

The full inventory is in CLAUDE.md → "Built-in UI Infrastructure" and the `section-builder` skill.
