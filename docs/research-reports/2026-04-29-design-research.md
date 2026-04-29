# Design research pass — 2026-04-29

Frozen-in-time research synthesis used to lock the design-system direction for the Arctic Fresh Horizon rebuild. Conducted 2026-04-29 via the `general-purpose` research subagent, drawing on `sonar` and direct inspection of brand style guides.

Distilled outcomes from this pass live in:

- `docs/brand-guardrails.md` — Inuit/Northern brand guardrails
- `docs/nnc-display.md` — NNC pricing visual pattern
- `docs/horizon-notes.md` → "Performance / bandwidth-aware defaults" section
- `docs/roadmap.md` — IA decisions in **Navigation / Header** and **Homepage** sections

This report is the source material for those distillations — keep around when revisiting the underlying reasoning.

---

## 1. Display font — single-family vs paired

The signal across observed brands is mixed but leans **toward single-family sans for grocery DTC at scale**, with custom or stylized variants providing display contrast within the same family.

Confirmed single-family sans across body and display:

- **HelloFresh** — Agrandir throughout (pangrampangram.com)
- **Misfits Market / Imperfect Foods** — Sharp Grotesk for all marketing and product surfaces (Fonts In Use)
- **Ocado** — single custom sans family
- **Trader Joe's** — custom hand-drawn sans, used for both body and display
- **HelloFresh, FreshDirect** — single sans-serif throughout

Confirmed paired:

- **Thrive Market** — Archivo (body) + Clash Display (headings). Notably, Clash Display is itself a stylized sans, not a serif — so the pairing is sans + display-sans, not sans + serif.

The "sans + serif headline" pattern that 2026 trend articles push (Lora/Lato, Abril Fatface/Lato) shows up in branding guides but **not** in the actual production grocery sites surveyed. Operating grocery brands lean utilitarian and consistent — too many serif headings across 9,000 SKU cards becomes visual noise on small screens.

**Conclusion: ship Manrope-only.** Achieve display contrast via weight (700/800), size, and tighter tracking (-0.02em on h1/h2), not a second family. Manrope is a humanist sans — it already has more warmth than a neutral grotesk like Inter. Adding a serif headline would fight Arctic Fresh's friendly rounded mint logo, which is itself a soft sans wordmark; serif headings would feel more "specialty natural foods boutique" than "friendly neighborhood grocery." If a second face is ever needed, reach for a single condensed/display weight of Manrope (e.g., display use of `Manrope 800` at -0.025em tracking) before introducing a serif.

Sources: pangrampangram.com (HelloFresh), fontsinuse.com (Misfits Market), Thrive Market site, fontfabric.com.

---

## 2. Indigenous-operated / Northern Canadian brand design

Concrete patterns from authoritative sources (Government of Nunavut Visual Identity Guidelines, Canadian North brand guide, Air Inuit, ITK):

**Typography conventions.** Sans-serif dominates for legibility and bilingual rendering. Canadian North uses Ranelte (extended humanist sans). Air Inuit commissioned a custom face that handles Roman + Inuktitut syllabics within one family. The Government of Nunavut style favors Calibri/Cambria. Avoid decorative or "tribal-styled" fonts entirely.

**Color.** Northern brands lean into **clean, restrained palettes** — red + grays + white (Canadian North, Air Inuit), or aurora/sun/water-evoking tones (GN, GNWT). They specifically avoid earth-tone overload and over-saturation. Arctic Fresh's mint + forest + coral palette already aligns: it's restrained, place-evocative without being kitschy, and the mint reads as "fresh produce" first, "cold north" second.

**Motifs.** Subtle and place-rooted. Inuksuit (Canadian North logo), polar bears (Nunavut wordmark), abstract aurora/sun/water shapes. Strongly avoid: igloos, totem poles (totems are West Coast First Nations, not Inuit — a basic geographic mistake), and "Eskimo" anything. Arctic Co-operatives uses simple geometry.

**Syllabics (ᐃᓄᒃᑎᑐᑦ).** When used by Inuit-operated brands, syllabics are **functional, not decorative** — bilingual labels, real Inuktitut words, treated as equal text alongside Roman. Air Inuit and GN both pair them at parity. The southern-designer trap: pasting random syllabic glyphs as ornament. Don't invent strings; consult a speaker. If Arctic Fresh wants syllabic presence on the site, the highest-integrity path is functional bilingual labels in the footer (community names in syllabics, the Inuktitut name "Nutaaq Niqituqtut" or similar if approved by the client).

**Photography.** Real people, real communities, working/everyday contexts — not exoticized landscapes or romanticized wildlife. Community-sourced over stock.

**Net practical recommendation for Arctic Fresh:** keep the existing palette and friendly sans wordmark — it's already on-pattern. Add bilingual community names where communities are listed (selector dropdown, footer service-area list) using actual Inuktitut syllabics confirmed with the client. Skip syllabics as ornament. Use real photography of Nunavut food, kitchens, and communities (client-sourced) over generic grocery stock. Avoid icons of igloos, sled dogs, Arctic explorers — yes, even cute ones.

Sources: Canadian North Visual Identity Guidelines (PDF), Government of Nunavut Visual Identity Guidelines (PDF), GNWT VIP Standards 3.0, ITK.ca, Nunatsiaq News (Air Inuit logo redesign).

---

## 3. Bandwidth-constrained ecommerce design

Cross-referenced patterns from Jumia, Mercado Libre, Flipkart Lite, Walmart, FB/IG Lite:

**Image loading.** **LQIP (Low-Quality Image Placeholder) with blur-up is the dominant pattern**, not skeletons. A 20–50px base64-inlined preview renders immediately, then the full image swaps in. Flipkart Lite reduced product image payload from 300KB to ~300 bytes for the placeholder. Skeletons are reserved for complex multi-block layouts; product grids should use solid color placeholders (matching dominant image color) or LQIP. Shopify's `image_url` filter supports tiny renditions natively, and Horizon's `snippets/image.liquid` should be checked for whether it's already emitting LQIP.

**Text first.** Render product title and price in the HTML before the image — server-side rendered, no JS dependency. This is critical for Arctic Fresh: a customer on a slow connection should see "Heinz Ketchup 750ml — $6.49" in <1s even if the image takes 3-5s.

**Fonts.** `font-display: swap` (Horizon defaults to this), aggressive subsetting, and consider `font-display: optional` on body if Manrope is even slightly over-sized — system fallback (Helvetica/Arial/system-ui) is not a tragedy on a grocery site. Subset Manrope to Latin only; don't ship the full ext-glyphs file. If syllabics are added for community names, load them as a tiny separate subset only on pages that need them.

**Animations.** Skip them on the critical path. No homepage hero parallax, no card hover transforms beyond a simple `transform: translateY(-2px)` on hover, no autoplay carousels above the fold (also bandwidth-hungry — preloads multiple slide images). The `slideshow.liquid` autoplay setting should be **off by default** for hero use.

**Server vs JS.** Favor server-rendered HTML aggressively. Hydration only for genuinely interactive components (community selector, quick-add, cart drawer). Don't reach for client-side filtering/sorting on collection pages — Shopify's URL-based filter system is server-rendered and works.

**Universal pattern Western themes miss:** a **data-saver acknowledgment.** Either a soft "low data mode" toggle (which forces no-image grid views, minimal motion) or — more pragmatic — making the default experience already lean enough that a toggle isn't needed.

Sources: jumia case studies (xtransfer.com, MoEngage), edataindia.com (Flipkart progressive images), bluestout.com (Walmart mobile case), LinkedIn LQIP-in-Shopify guide.

---

## 4. Subsidy / community pricing display

The clear pattern across Costco, Sam's Club, Instacart EBT/SNAP, Amazon Fresh EBT, and NNC's own materials:

**The subsidy is shown as eligibility, not as a sale.** Instacart's SNAP-eligible badge is a small green "SNAP" tag *under* the price, in standard weight, no strikethrough, no "you saved $X" framing. Amazon Fresh same — "SNAP EBT eligible" label, secondary prominence. Costco never shows a non-member price next to its member prices. The signal is: *this price is what you pay,* not *look how much you saved.*

**Why this matters.** "You saved $12!" framing on every product trains the customer to perceive the subsidy as a temporary promo. NNC is a **structural** program, not a sale event. Treat it like Costco treats membership pricing: the price you see is the price you pay, with a small badge indicating program participation. Pre-subsidy "landed cost" should generally **not** be shown on product cards — it's confusing, makes the actual price look like a discount that could be revoked, and adds visual noise to a 2-up mobile grid.

**Visual treatment:**

- Final all-in price: bold, primary color (deep forest #003d1c), main weight 600-700.
- NNC eligibility badge: small pill below price, mint or forest tinted (not red, not orange — those read as sale). Text: "NNC subsidized" or just "NNC" with a short tooltip/info-on-tap explaining what that means. Roughly 11-12px, weight 500.
- No strikethrough pre-subsidy price on cards.
- On the **product detail page (PDP)**, a per-item NNC breakdown panel is appropriate (this matches the April 2026 Waleed feedback) — that's a deeper, expandable disclosure: base price, shipping cost, NNC subsidy applied, your total. Keep that panel collapsed by default ("View NNC breakdown") so the card stays clean.
- Use coral (#ff8b69) only for genuine sale/promo states, never for NNC. Keep the subsidy in the green/forest family so it reads as "ongoing program" and never confuses with markdown.

Sources: Instacart Storefront docs (SNAP EBT), nutritionnorthcanada.gc.ca, Costco price-tag analyses, Sam's Club pricing pattern reviews.

---

## 5. High-SKU search & navigation (9,000+ SKUs)

The convergent IA across Amazon Fresh, Instacart, Walmart Grocery, Tesco, and Ocado — and the recommendation for Arctic Fresh:

**Search is the primary entry point at this scale, not browse.** With 9,000 SKUs, a customer looking for rice should never traverse Pantry → Rice & Grains → Rice → White Rice. They type "rice." So the homepage hierarchy must be:

1. **Sticky header with prominent search bar.** Full-width on mobile, with a large tap target. Typeahead with thumbnails.
2. **"Buy It Again" carousel above the fold** for returning customers. This is the single highest-impact pattern in the research — every UX study of grocery returning users says they want recent purchases first, not categories. Horizon's `customer.orders` data + a small carousel snippet handles this.
3. **Horizontal scrolling category pills** for browse — "Produce, Dairy, Pantry, Frozen, Meat & Seafood, Drinks, Baby & Kids, Household, Health & Beauty" — 6 visible, swipeable. **Not** a mega-menu on mobile. Mega-menu is desktop-only.
4. **Featured/seasonal content** below.

**Predictive search must include:**

- Typo tolerance ("ketchp" → "ketchup")
- Recent searches (per-customer)
- Semantic deduplication (don't show "milk" and "milks")
- Popularity-ranked suggestions normalized across the catalog (Instacart's published approach)
- Thumbnails on top suggestions (drives engagement)

Shopify's native search + Search & Discovery app gets ~70% of the way. For better recall, consider Algolia or Searchanise — but only if budget allows. Native is the starting point.

**Department vs search-first.** For new customers, departments matter (orientation). For returning (the majority in grocery — 60-80% repurchase rate), "Buy It Again" + search dominate. Build for returners; don't sacrifice them for new-user category education.

**Substitution surfacing.** When a search turns up nothing or low stock, show substitutes ("Out of Heinz 750ml? Try French's 750ml") — reduces cart abandonment.

**Mobile bottom nav.** Consider a 4-icon bottom nav: Home / Search / Buy Again / Cart. Persistent. This is standard on Instacart and Walmart Grocery.

Sources: tech.instacart.com (autocomplete ML article), uxdesign.cc (Amazon nav patterns), corporate.walmart.com (omni store design), Sigosoft (Amazon Fresh UI changes).

---

## Net effect on Arctic Fresh — punch list

1. **Ship Manrope-only** for body and display. Use weight + size + tracking for hierarchy. Don't introduce a serif headline face. Subset to Latin; add a separate syllabics subset only if/when bilingual community names ship.
2. **Add bilingual community names (Inuktitut syllabics)** in the community selector and footer service-area list. Validate the syllabic strings with the client — never invent. Skip syllabics as decorative ornament anywhere else.
3. **Use real Nunavut photography** sourced from the client (community life, kitchens, food in context) — not Arctic stock. Avoid igloos, sled dogs, Arctic explorers, and totems (the last is a different culture entirely).
4. **NNC pricing displays as eligibility, not as a sale.** Mint/forest pill below the all-in price reading "NNC subsidized." No strikethrough pre-subsidy price on cards. Coral (#ff8b69) is reserved for actual sale/promo.
5. **PDP gets an expandable "NNC breakdown" panel** — collapsed by default, showing base + shipping + subsidy = total when expanded.
6. **Use LQIP (blur-up) for product images, not skeleton screens.** Render product title and price in HTML before the image. Verify Horizon's `snippets/image.liquid` is emitting LQIP-style preview; if not, extend it.
7. **Kill autoplay/parallax/animation on critical paths.** Hero slideshow auto-advance off by default. Card hover effects: keep to a 2px translate, no shadows-bouncing-around. No render-blocking animations.
8. **Homepage IA: sticky search → "Buy It Again" carousel → horizontal category pills → featured content.** Add a 4-icon mobile bottom nav (Home / Search / Buy Again / Cart). Mega-menu desktop-only; mobile gets pills + collapsible drawer.
