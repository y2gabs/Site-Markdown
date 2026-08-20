# Contemporary Design Systems

Structural design systems, layout patterns, art direction, and industry-specific direction.

Each industry entry specifies **Vibe, Palette, Type, Imagery, Layout, Radius, Icon, Signature,
and Motion**. Layout and Signature reference the pattern libraries below by name — learn those
first, then the industry table becomes a fast lookup rather than 44 things to memorize.

Cross-references: palettes → `02-color-system.md`, type pairings → `03-typography.md`, motion
vocabulary → `04-motion.md`, components (⚙) → `07-react-tailwind-snippets.md`.

---

## General Styling Rules (Apply to All Industries)

* **Borders:** Incredibly subtle — hairlines, never harsh lines. Dark themes use very
  low-opacity light borders (`border-white/10`). Prefer separating content with whitespace and
  background shifts over drawing a border at all.
* **Shadows:** Soft and diffused, tinted with the palette hue rather than gray. Expansive,
  low-opacity (`0 24px 60px -20px`), never Tailwind's default `shadow-lg`. Dark themes use
  subtle luminous glows.
* **Gradients:** Subtle. Blend analogous shades; vary lightness more than hue. Glassmorphism
  uses heavy background blur with low-opacity fills. Never gradient-fill headline text (the one
  sanctioned exception is Entertainment/Nightlife, §15).
* **Spacing:** Generous whitespace is non-negotiable. Major sections get `py-24` to `py-40`.
  **Vary section rhythm** — uniform padding on every section produces a metronome.
* **Border Radius:** Match the vibe, then apply it consistently across every card, button,
  input, and image. Mixing radii arbitrarily is a visible tell.
* **Hover States:** Every interactive element needs one. Elevate with subtle translation or
  shadow depth. Transition specific properties, never `transition-all`.
* **Buttons:** Substantial — generous padding, clear hierarchy, micro-animation on hover.
  Minimum 48px tall.
* **Composition:** Default to asymmetry. Offset grids, uneven column splits (5/7 over 6/6),
  images that bleed off one edge. One dominant element per viewport.

---

## Layout Archetypes

A named pattern library. Each industry entry below picks **one hero pattern** and **one or two
section patterns** rather than inventing structure from scratch — this is what makes a
generated site feel composed instead of assembled from a single repeating template.

### Hero Patterns

| Name | Description |
|---|---|
| **Cinematic Full-Bleed** | Full-viewport photo/video, heavy directional scrim, headline anchored low-left. ⚙ `ParallaxHero` |
| **Split Hero** | 45/55 or 50/50 text vs. image; the image bleeds off the outer edge, never fully contained |
| **Editorial Masthead** | Text-only, oversized display type, a single thin rule, no photography above the fold |
| **Product-Led Hero** | The product UI/object itself is the hero image, often tilted or in a device frame |
| **Founder/Portrait Hero** | A real human, direct gaze, first-person voice — trades trust for polish |
| **Data/Map Hero** | Abstract data visualization, route lines, or a map as hero background |
| **Menu/Catalog Hero** | The product grid starts almost immediately below a compressed headline band |

### Section Patterns

| Name | Description |
|---|---|
| **Bento Grid** | Asymmetric grid of unequal tiles — one large + several small, each tile one fact. ⚙ `BentoGrid` |
| **Marquee Wall** | Logos, keywords, or menu items scrolling in a seamless loop. ⚙ `Marquee` |
| **Masonry Gallery** | Varied-aspect-ratio photo grid, editorial rhythm |
| **Before/After Slider** | Draggable comparison — high trust value for trades, beauty, fitness, renovation. ⚙ `BeforeAfterSlider` |
| **Numbered Process Row** | 3–4 steps, oversized numerals, a connecting line or arrow |
| **Comparison/Pricing Table** | Tiered offer comparison, one tier visually elevated. ⚙ `PricingTable` |
| **Timeline** | Vertical or horizontal chronological narrative — origin story, milestones. ⚙ `Timeline` |
| **Stat Band** | Dark punctuation section, count-up numerals, high contrast |
| **Founder Letter** | Long-form editorial block, drop cap, a real signature or portrait |
| **Interactive Map Block** | Location(s) with custom markers — multi-location retail, service areas |
| **Split-Screen Compare** | Two contrasting halves: before/after, us/them, old way/new way |
| **Menu/Price List** | Item + dotted leader + price, grouped by category |

Pick patterns that don't repeat. If a page uses Bento Grid for features, its testimonials
section should not also be a symmetric card grid — vary the container, not just the content.

---

## Imagery & Art Direction

Previously absent from this framework entirely, and one of the highest-leverage additions.
Color and type set the mood; photography proves it's real.

* **Authenticity over generic stock.** Real environments, real hands, real product — not
  handshake photography, thumbs-up shots, or people pointing at a whiteboard. If no real
  photography exists yet, prefer a typographic composition, an abstract mesh-gradient panel,
  or line-art illustration over a mismatched stock photo or a gray placeholder box.
* **One consistent grade across every photo on the page.** Pick a treatment — warm, cool,
  desaturated, high-contrast B&W — and apply it uniformly, as if every image was shot for this
  brand rather than sourced from five different libraries. A `mix-blend-multiply` accent-tinted
  overlay at low opacity (`bg-accent/10 mix-blend-multiply`) unifies mismatched source images.
* **Pick one or two aspect ratios and hold them.** `4:5` portrait + `16:9` wide is a complete
  system; five different crop ratios on one page reads as unplanned.
* **Crop with intent.** Faces and product focal points should never sit on a crop edge. Leave
  negative space on the side text will overlay.
* **Duotone as a unifying device**, not decoration: map shadows to `--ink` and highlights to a
  light accent tint via CSS `filter` or an SVG duotone filter, for a page with only 1–2 source
  photos that needs to feel intentional rather than sparse.

## Iconography

* **One icon style and stroke weight for the entire build.** Line icons at 1.5px stroke is the
  correct default and works across nearly every industry. Duotone reads more technical/modern.
  Filled/solid reads more playful/bold. Never mix styles on one page.
* **Icons never stand alone.** Always paired with a label — an icon is a scannable accent to
  text, not a substitute for it.
* **Size to context:** 20–24px inline with text, 32–40px as a feature-card header. Never an
  arbitrary size chosen per instance.
* Prefer a single well-made icon set (e.g., Heroicons, Phosphor, Lucide) over mixing sources —
  inconsistent stroke weight between icons is as visible a tell as inconsistent radii.

---

## Industry Directions

## 1. Agriculture, Forestry & Mining
**Vibe:** Grounded, rugged, honest, connected to the land.
**Palette:** Moss/olive (H 120–145), warm bark neutrals, bone ground.
**Type:** Pairing 3 or 11. **Imagery:** Warm-graded landscape and hands-on-work photography, `4:5` + `16:9`.
**Layout:** Cinematic Full-Bleed hero → Numbered Process Row (how the product is grown/sourced).
**Radius:** `rounded-sm` to `rounded-md`. **Icon:** Line, 1.5px.
**Signature:** Topographic contour line-art as section dividers; grain-textured backgrounds.
**Motion:** Slow reveals (800ms), gentle parallax on landscape imagery.

## 2. Automotive Sales, Repair & Parts
**Vibe:** Bold, kinetic, high-contrast, performance-driven.
**Palette:** Near-black ground, ember orange (H 25) or electric blue (H 250). Full dark theme.
**Type:** Pairing 11, heavy weights. **Imagery:** High-contrast, motion-blurred or studio-lit product shots.
**Layout:** Cinematic Full-Bleed hero → Stat Band (0–60, warranty miles) → Bento Grid (services).
**Radius:** `rounded-none` to `rounded-md` — angular. **Icon:** Line, bold.
**Signature:** Diagonal slashes and angled dividers; animated racing-stripe gradient borders; metallic sheen on card hover.
**Motion:** Faster than default (400–500ms). Horizontal slide-ins.

## 3. Beauty & Personal Care
**Vibe:** Elevated, sensorial, effortlessly chic.
**Palette:** Bone/clay ground, burnished copper (H 30–50), very low chroma.
**Type:** Pairing 1 or 8. **Imagery:** Soft-lit, low-contrast product and portrait photography, circular crops.
**Layout:** Split Hero → Before/After Slider (results) → Masonry Gallery.
**Radius:** `rounded-none`/`rounded-sm`; circles for portraits. **Icon:** Thin line.
**Signature:** Editorial whitespace; circular image masks; thin separator rules.
**Motion:** Very slow, soft fades. 16px translation max, nothing bouncy.

## 4. Barbershops & Men's Grooming
**Vibe:** Confident, textured, unfussy craft.
**Palette:** Charcoal/graphite ground, warm brass accent (H 45–60).
**Type:** Pairing 11 or 6, condensed feel. **Imagery:** High-contrast B&W or sepia-toned interior and chair shots.
**Layout:** Founder/Portrait Hero → Menu/Price List → Timeline (shop history).
**Radius:** `rounded-sm`. **Icon:** Line, medium weight.
**Signature:** A textured leather/wood-grain accent band; a wall-of-tools photo strip.
**Motion:** Direct, minimal decoration.

## 5. Business & Professional Services
**Vibe:** Confident, refined, trustworthy, forward-thinking.
**Palette:** Deep navy/slate (H 240–260), near-white ground.
**Type:** Pairing 11 or 3. **Imagery:** Real team and workspace photography, cool-graded, `4:5`.
**Layout:** Split Hero → Bento Grid (capabilities) → Stat Band.
**Radius:** `rounded-lg` to `rounded-xl`. **Icon:** Line, consistent 1.5px.
**Signature:** Asymmetric grids with offset images; fine dot-grid background pattern; vertical accent border stripes on cards.
**Motion:** Crisp, restrained. 600ms reveals, subtle stagger.

## 6. Marketing & Creative Agencies
**Vibe:** Confident, expressive, a little unpredictable — the site is the portfolio.
**Palette:** High-chroma single accent (H chosen per agency identity) on a stark neutral ground.
**Type:** Pairing 7 or 11, oversized display. **Imagery:** Case-study screenshots and process shots, consistent duotone.
**Layout:** Editorial Masthead hero → Masonry Gallery (work) → Founder Letter.
**Radius:** `rounded-none`. **Icon:** Minimal — typography carries most of the weight.
**Signature:** Cursor-following micro-interaction; oversized project numbers; a distinctive custom cursor or hover reveal.
**Motion:** Confident and a touch playful — `spring` easing permitted on hover states only.

## 7. Computers, Technology & Electronics
**Vibe:** Precise, futuristic, intelligently minimal.
**Palette:** Charcoal ground, cyan/electric accent (H 190–230). Dark.
**Type:** Pairing 11 + mono for labels. **Imagery:** Studio product photography on gradient or void backgrounds.
**Layout:** Product-Led Hero → Bento Grid (specs) → Comparison Table.
**Radius:** `rounded-lg`, consistent. **Icon:** Line or duotone.
**Signature:** Glow effects on key elements; fine grid-line backgrounds; animated gradient borders on cards.
**Motion:** Precise, quick. Terminal-style reveals. Subtle glow pulse on primary CTA.

## 8. SaaS & Software Startups
**Vibe:** Clear, benefit-led, quietly ambitious.
**Palette:** One saturated accent (H varies by brand) on a light or near-white ground; avoid the stock purple→indigo default entirely.
**Type:** Pairing 11. **Imagery:** The product UI itself — real screenshots in a clean device or browser frame, never stock people.
**Layout:** Product-Led Hero → Bento Grid (features) → Comparison/Pricing Table → Marquee Wall (logos).
**Radius:** `rounded-xl`. **Icon:** Line, 1.5px, uniform set.
**Signature:** An interactive product preview (tabs that swap a screenshot) rather than a static image; animated gradient border on the primary pricing tier.
**Motion:** Fast and confident (300–400ms). Product screenshots crossfade rather than hard-cut.

## 9. AI & Machine Learning Products
**Vibe:** Intelligent, fluid, trustworthy despite the novelty.
**Palette:** Deep charcoal or near-white ground with an electric single accent (H 230–260 or 150–170); avoid neon-rainbow gradient clichés.
**Type:** Pairing 11 + mono for model/parameter labels. **Imagery:** Abstract generative visuals or real product screenshots — never stock robot/brain imagery.
**Layout:** Product-Led Hero with a live or animated demo → Bento Grid (capabilities) → Stat Band (benchmarks).
**Radius:** `rounded-xl`. **Icon:** Line or duotone, technical feel.
**Signature:** A subtle animated particle/mesh field that responds to scroll, used sparingly as atmosphere, not as the whole background.
**Motion:** Smooth, continuous — favor `in-out-soft` for anything simulating "thinking."

## 10. Web3, Crypto & Blockchain
**Vibe:** Confident, technical, allergic to hype-cliché.
**Palette:** True dark ground, one restrained accent (H 260–280 or 160–180) — avoid the genre's default neon-rainbow-on-black.
**Type:** Pairing 11 + mono for addresses/hashes. **Imagery:** Abstract geometric/network visuals, minimal.
**Layout:** Data/Map Hero (network visualization) → Stat Band (TVL, transactions) → Bento Grid.
**Radius:** `rounded-lg`. **Icon:** Line, technical.
**Signature:** A live-feeling animated network-node graphic; tabular-nums stat counters that feel like a live dashboard.
**Motion:** Precise, restrained — resist the urge toward gimmicky glitch effects.

## 11. Construction, Architecture & Engineering
**Vibe:** Structural, bold, blueprint-precise. Industrial sophistication.
**Palette:** Concrete neutrals, safety amber accent (H 60–80), steel undertone.
**Type:** Pairing 4 or 11. **Imagery:** Wide facility/site photography, natural light, minimal retouching.
**Layout:** Cinematic Full-Bleed hero → Numbered Process Row → Timeline (project history).
**Radius:** `rounded-none` — strictly geometric. **Icon:** Line, technical.
**Signature:** Strong geometric grids; blueprint-style thin-line illustrations; oversized section numbers as background watermarks.
**Motion:** Structural — elements draw in via clip-path or width, not fade.

## 12. Interior Design & Home Staging
**Vibe:** Curated, warm, aspirational — the space is the hero.
**Palette:** Warm neutral ground, one muted accent (H 30–50), very low chroma.
**Type:** Pairing 6 or 1. **Imagery:** Full-bleed room photography, consistent warm grade, `4:5` portfolio crops.
**Layout:** Split Hero → Masonry Gallery (portfolio) → Founder Letter.
**Radius:** `rounded-md`. **Icon:** Minimal, thin line.
**Signature:** A room-by-room before/after slider; a materials/palette swatch strip per project.
**Motion:** Slow, atmospheric. Gentle image crossfades between before/after states.

## 13. Coworking & Flexible Workspace
**Vibe:** Energetic but calm, community-forward, design-literate.
**Palette:** Warm neutral ground, one confident accent (H 30–50 or 150–170).
**Type:** Pairing 11. **Imagery:** Real member and space photography, natural light, candid over posed.
**Layout:** Split Hero → Bento Grid (amenities) → Interactive Map Block (locations).
**Radius:** `rounded-2xl`. **Icon:** Line, friendly weight.
**Signature:** A live-feeling occupancy or availability indicator; a member wall (Marquee) of logos/faces.
**Motion:** Light, quick, welcoming.

## 14. Education & Childcare
**Vibe:** Warm, approachable, optimistic, intellectually stimulating.
**Palette:** Warm sun accent (H 45–70) with a cool secondary (H 200). Bright ground.
**Type:** Pairing 10 or 5. **Imagery:** Candid classroom/activity photography, bright and natural.
**Layout:** Split Hero → Bento Grid (programs) → Timeline (a typical day/term).
**Radius:** `rounded-3xl` everywhere. **Icon:** Filled or duotone, friendly.
**Signature:** Playful line-icon illustrations; alternating section background shades; very low-opacity confetti/dot patterns.
**Motion:** Friendly — `spring` easing permitted. Gentle scale on hover.

## 15. Entertainment, Arts & Nightlife
**Vibe:** Electric, immersive, unapologetically bold.
**Palette:** True dark ground, magenta/violet accent (H 300–330), high chroma on small areas.
**Type:** Pairing 2 or 11. **Imagery:** High-contrast, saturated event photography; motion-blur encouraged.
**Layout:** Cinematic Full-Bleed hero (video ideal) → Marquee Wall (lineup/press) → Bento Grid.
**Radius:** `rounded-2xl` with glass fills. **Icon:** Filled, bold.
**Signature:** Gradient text permitted **here only**; glassmorphism cards with high blur; neon glow hover states.
**Motion:** The most expressive of any industry — layered parallax, glow pulses, marquees.

## 16. Music, Record Labels & Audio
**Vibe:** Atmospheric, tactile, sonically-led.
**Palette:** Deep ground (H varies with genre — warm for acoustic/soul, cool/dark for electronic), one saturated accent.
**Type:** Pairing 2 or 11. **Imagery:** Artist and session photography, grainy/film-textured for warmth.
**Layout:** Cinematic Full-Bleed hero (with audio-reactive option) → Marquee Wall (releases) → Masonry Gallery.
**Radius:** `rounded-md`. **Icon:** Line, minimal.
**Signature:** A waveform visualization as a decorative motif; an embedded audio player styled to match the palette rather than left as browser-default.
**Motion:** Rhythmic — consider tying reveal timing to a subtle beat-like cadence.

## 17. Podcasts, Newsletters & Creators
**Vibe:** Personal, direct, conversational authority.
**Palette:** One accent tied to the creator's existing brand; otherwise warm and approachable (H 30–50).
**Type:** Pairing 1 or 11. **Imagery:** A real host portrait, direct gaze, consistent across episodes/issues.
**Layout:** Founder/Portrait Hero → Masonry Gallery (episode/issue grid) → Marquee Wall (platforms/press).
**Radius:** `rounded-2xl`. **Icon:** Line, platform icons kept to a real, minimal set.
**Signature:** An embedded, styled audio player; a "start here" curated episode strip.
**Motion:** Warm, unhurried. Simple fades.

## 18. Events, Weddings & Photography
**Vibe:** Editorial, romantic, gallery-quality. Imagery dominant, typography unobtrusive.
**Palette:** Ivory/champagne ground, blush accent (H 20–40), near-zero chroma neutrals.
**Type:** Pairing 5 or 2; Pairing 9 as signature accent only. **Imagery:** Full-bleed, consistently color-graded, `4:5` portrait-led.
**Layout:** Cinematic Full-Bleed hero → Masonry Gallery → Founder Letter.
**Radius:** `rounded-none`. **Icon:** Minimal — thin line if used at all.
**Signature:** Masonry photo grids with varied aspect ratios; italic pull quotes; lightbox interactions; generous letter-spacing on uppercase labels.
**Motion:** Slow (800ms), fades only. Ken Burns drift on hero. Image-wipe reveals.

## 19. Wedding & Event Planning
**Vibe:** Composed, reassuring, quietly luxurious — the planner is the calm in the chaos.
**Palette:** Same family as §18 but slightly more restrained; the planner's brand accent, not the venue's.
**Type:** Pairing 5 or 6. **Imagery:** Real past-event photography, consistently graded.
**Layout:** Split Hero → Numbered Process Row (planning stages) → Masonry Gallery (past events).
**Radius:** `rounded-sm`. **Icon:** Thin line.
**Signature:** A day-of timeline visualization; a real client testimonial with the couple's names and date.
**Motion:** Slow, reassuring fades.

## 20. Financial Services & Banking
**Vibe:** Authoritative, secure, premium. Stable with a modern edge.
**Palette:** Deep teal/petrol (H 200–230) or forest (H 150). Dark sections as punctuation.
**Type:** Pairing 3 or 11. **Imagery:** Minimal — abstract data visuals over people photography.
**Layout:** Split Hero → Stat Band → Comparison Table (account/plan tiers).
**Radius:** `rounded-xl`, precise. **Icon:** Line, precise.
**Signature:** Subtle gradient-mesh hero backgrounds; thin-line charts and percentage indicators; trust badges.
**Motion:** Controlled. Count-up figures. No decorative movement.

## 21. Insurance
**Vibe:** Plain-spoken trust — clarity is the differentiator, not polish alone.
**Palette:** Confident blue or green (H 200–230 or 140–160), high contrast, uncluttered.
**Type:** Pairing 11. **Imagery:** Real people in real (not staged-generic) situations, if used at all — otherwise abstract/iconographic.
**Layout:** Split Hero → Comparison Table (coverage tiers) → Numbered Process Row (claims process).
**Radius:** `rounded-lg`. **Icon:** Line, very legible.
**Signature:** A coverage/quote calculator styled as a first-class component, not an afterthought form.
**Motion:** Minimal. Clarity outranks expression.

## 22. Food & Beverage: Dining
**Vibe:** Appetite-driven, warm, inviting.
**Palette:** Terracotta/ember accent (H 20–40), cream ground, rich saturated photography.
**Type:** Pairing 1 or 5. **Imagery:** Warm-graded food photography, shallow depth of field.
**Layout:** Cinematic Full-Bleed hero → Menu/Price List → Masonry Gallery (interior/dishes).
**Radius:** `rounded-md` to `rounded-xl`. **Icon:** Line, minimal.
**Signature:** Menu layouts with dotted leaders between item and price; decorative herb/utensil illustrations; textured paper backgrounds.
**Motion:** Warm and unhurried. Slow image zoom on hover.

## 23. Coffee Shops & Cafés
**Vibe:** Warm, tactile, unpretentious craft.
**Palette:** Warm brown/cream (H 40–60), one accent for seasonal menu callouts.
**Type:** Pairing 1 or 10. **Imagery:** Natural-light interior and product shots, consistent warm grade.
**Layout:** Menu/Catalog Hero → Marquee Wall (seasonal items) → Interactive Map Block (locations).
**Radius:** `rounded-2xl`. **Icon:** Line, friendly.
**Signature:** A rotating seasonal-menu ribbon; a hand-lettered or textured accent treatment on headings.
**Motion:** Light, quick.

## 24. Craft Brewery, Winery & Distillery
**Vibe:** Crafted, characterful, a little rebellious within a premium frame.
**Palette:** Rich amber/copper (H 30–50) or deep burgundy (H 0–15), dark sections common.
**Type:** Pairing 6 or 11. **Imagery:** Process/production photography (barrels, tanks, hands), moody lighting.
**Layout:** Cinematic Full-Bleed hero → Menu/Price List (tasting menu) → Timeline (founding story).
**Radius:** `rounded-sm`. **Icon:** Line, industrial.
**Signature:** A label/bottle showcase with hover-rotate; a tasting-notes accordion per product.
**Motion:** Moderate, confident.

## 25. Food & Beverage: Groceries & Retail
**Vibe:** Fresh, clean, wholesome, organic.
**Palette:** Leaf green accent (H 130–150), kraft neutrals, bright ground.
**Type:** Pairing 10 or 11. **Imagery:** Bright, natural-light product photography.
**Layout:** Menu/Catalog Hero → Bento Grid (categories) → Marquee Wall (brands carried).
**Radius:** `rounded-2xl`; `rounded-full` badges. **Icon:** Line, friendly.
**Signature:** Rounded category badges; kraft-paper texture accents; color-coded category system.
**Motion:** Light and quick. Hover lift on product cards.

## 26. Bookstores & Stationery
**Vibe:** Quiet, curated, intellectually warm.
**Palette:** Warm paper ground, one muted accent (H 20–40 or 200–220).
**Type:** Pairing 3 or 6. **Imagery:** Shelf and product detail photography, natural light.
**Layout:** Editorial Masthead hero → Masonry Gallery (staff picks) → Timeline (shop history).
**Radius:** `rounded-sm`. **Icon:** Thin line.
**Signature:** A "staff pick" pull-quote card style; a reading-list/curated-shelf component.
**Motion:** Slow, quiet fades.

## 27. Government, Community & Non-Profits
**Vibe:** Accessible, trustworthy, inclusive, clear. Dignified without being cold.
**Palette:** Civic blue (H 230–250), maximum contrast, minimal decoration.
**Type:** Pairing 11 or 3. **Imagery:** Real community photography, documentary style.
**Layout:** Editorial Masthead hero → Numbered Process Row (how to access a service) → Bento Grid.
**Radius:** `rounded-md`. **Icon:** Line, very legible.
**Signature:** High contrast ratios throughout; landmark-driven structure; icon + text pairings.
**Motion:** Minimal. Accessibility outranks expression — reveals only, short distances.

## 28. Nonprofit Advocacy & Fundraising
**Vibe:** Urgent but dignified — moves people to act without exploiting the cause.
**Palette:** One confident accent tied to the cause, high contrast, restrained otherwise.
**Type:** Pairing 11 or 3. **Imagery:** Real, consented documentary photography — never stock suffering imagery.
**Layout:** Cinematic Full-Bleed hero → Stat Band (impact numbers) → Timeline (program history).
**Radius:** `rounded-lg`. **Icon:** Line.
**Signature:** A donation-impact calculator ("$50 provides X"); a transparent stat block sourced from real reporting.
**Motion:** Restrained, respectful of subject matter.

## 29. Healthcare & Medical Services
**Vibe:** Clean, calming, clinically precise. Trust through clarity and restraint.
**Palette:** Calm teal (H 180–200), soft tinted surfaces, very light ground.
**Type:** Pairing 8 or 11. **Imagery:** Real clinical/staff photography, soft natural light — never generic stock doctors.
**Layout:** Split Hero → Bento Grid (services/departments) → Founder Letter (from the lead clinician).
**Radius:** `rounded-2xl`, soft throughout. **Icon:** Line, clean.
**Signature:** Soft rounded cards on light tinted backgrounds; a subtle pulse on the primary CTA.
**Motion:** Gentle, slow, reassuring. Nothing abrupt.

## 30. Dental & Orthodontics
**Vibe:** Bright, precise, quietly modern — clinical without feeling cold.
**Palette:** Fresh mint or sky (H 170–200), high-key bright ground.
**Type:** Pairing 8 or 11. **Imagery:** Real before/after smile photography, consistent lighting.
**Layout:** Split Hero → Before/After Slider → Numbered Process Row (new-patient journey).
**Radius:** `rounded-2xl`. **Icon:** Line, precise.
**Signature:** A smile-gallery before/after wall; an online-booking widget as a first-class component.
**Motion:** Light, precise, reassuring.

## 31. Senior Living & Elder Care
**Vibe:** Warm, dignified, unmistakably human — never clinical-cold or infantilizing.
**Palette:** Warm neutral ground, a soft confident accent (H 30–50 or 150–170).
**Type:** Pairing 1 or 6, generous size for readability. **Imagery:** Real residents and staff, candid warmth, never stock elderly-couple-on-beach clichés.
**Layout:** Founder/Portrait Hero → Masonry Gallery (community life) → Timeline (a resident's day).
**Radius:** `rounded-2xl`. **Icon:** Line, larger than default for legibility.
**Signature:** A virtual-tour component; real family testimonials with full attribution.
**Motion:** Slow, gentle — respect larger text needs and calmer pacing throughout.

## 32. Home Maintenance, Trades & Repairs
**Vibe:** Dependable, straightforward, professional.
**Palette:** Work orange (H 30–50), slate neutrals, high contrast.
**Type:** Pairing 11, bold weights. **Imagery:** Real job-site and crew photography.
**Layout:** Split Hero → Before/After Slider → Stat Band (years, jobs completed).
**Radius:** `rounded-lg`. **Icon:** Line, bold.
**Signature:** Before/after image sliders; star rating displays; chunky confident buttons.
**Motion:** Direct and quick. Functional, not decorative.

## 33. Industrial, Manufacturing & Wholesale
**Vibe:** Powerful, efficient, precision-engineered. Scale and technical capability.
**Palette:** Steel blue (H 210–230), graphite ground, dark sections.
**Type:** Pairing 11 + mono for specs. **Imagery:** Full-width facility photography, dark overlays.
**Layout:** Cinematic Full-Bleed hero → Comparison Table (spec sheets) → Stat Band (capacity).
**Radius:** `rounded-sm` to `rounded-md`. **Icon:** Line, technical.
**Signature:** Technical spec tables with clean grid lines; large numerical capacity callouts; diagonal accent lines.
**Motion:** Mechanical precision. Staggered reveals, count-ups.

## 34. Legal Services
**Vibe:** Authoritative, distinguished, reassuringly competent.
**Palette:** Oxblood (H 20–30) or deep indigo (H 250–270) with brass accent. Rich, low-value.
**Type:** Pairing 3 or 7. **Imagery:** Consistently framed professional headshots; minimal decorative photography.
**Layout:** Editorial Masthead hero → Bento Grid (practice areas) → Founder Letter (pull quotes from results).
**Radius:** `rounded-none` to `rounded-sm`. **Icon:** Minimal.
**Signature:** Sophisticated two-column layouts; vertical accent rules; practice-area cards with bottom-border accents.
**Motion:** Restrained and slow. Fades only, no translation on text.

## 35. Logistics, Transportation & Storage
**Vibe:** Dynamic, efficient, globally connected. Movement and reliability.
**Palette:** Signal blue (H 200–220), clean neutrals.
**Type:** Pairing 11. **Imagery:** Fleet and facility photography, clean and well-lit.
**Layout:** Data/Map Hero (route visualization) → Stat Band (on-time %, fleet size) → Bento Grid.
**Radius:** `rounded-lg`. **Icon:** Line, technical.
**Signature:** Map-inspired route-line dividers; a live-feeling tracking-timeline component; animated counter stats.
**Motion:** Directional — elements enter along the axis of travel.

## 36. Media, Communications & Publishing
**Vibe:** Editorial, intelligent, culturally aware. Typographic craft signals credibility.
**Palette:** Paper white ground, editorial red accent (H 0–20), near-black ink.
**Type:** Pairing 3 or 7 — typography is the design here. **Imagery:** Consistently graded editorial photography with clear byline treatment.
**Layout:** Editorial Masthead hero → Masonry Gallery (article grid, mixed featured/standard sizes).
**Radius:** `rounded-none`. **Icon:** Minimal.
**Signature:** Strong display type scale; multi-column layouts; drop caps on long-form; distinct metadata styling.
**Motion:** Nearly none. Let the typography carry it. Fades only.

## 37. Pets, Animals & Veterinary Services
**Vibe:** Warm, playful, reassuringly caring.
**Palette:** Warm honey (H 35–60) or soft mint (H 170), bright friendly ground.
**Type:** Pairing 10 or 5. **Imagery:** Real patient/pet photography, bright and candid.
**Layout:** Split Hero → Masonry Gallery (patient photos) → Founder/Portrait Hero (the vet team).
**Radius:** `rounded-3xl` everywhere. **Icon:** Filled, friendly.
**Signature:** Photo galleries with white-border frames and slight rotation; very low-opacity animal silhouettes.
**Motion:** Playful — `spring` easing permitted. Gentle scale and tilt on hover.

## 38. Real Estate & Property Management
**Vibe:** Aspirational, polished, high-value. Property photography leads.
**Palette:** Warm stone ground, brass accent (H 40–60), charcoal dark sections.
**Type:** Pairing 4 or 6. **Imagery:** Professional, wide-angle, twilight/golden-hour exteriors.
**Layout:** Cinematic Full-Bleed hero → Interactive Map Block (listings) → Bento Grid (property stats).
**Radius:** `rounded-lg`, restrained. **Icon:** Line, precise.
**Signature:** Property card grids with prominent pricing and image hover zoom; refined filter/search bar.
**Motion:** Smooth and premium. Slow image zoom. Parallax on hero.

## 39. Retail Shopping: Apparel & Jewelry
**Vibe:** Minimalist, editorial, aspirational. Lookbook design — the product is the interface.
**Palette:** Near-monochrome, chroma below 0.02. The product supplies the color.
**Type:** Pairing 2, 7, or 6. **Imagery:** Full-bleed studio and lookbook photography, one consistent lighting style.
**Layout:** Editorial Masthead or Product-Led Hero → Menu/Catalog grid → Marquee Wall (press mentions).
**Radius:** `rounded-none`. **Icon:** Minimal.
**Signature:** Hover-to-reveal quick-shop overlays; precise spacing and alignment; thin tracking on nav and category labels.
**Motion:** Slow, sparse, confident. Crossfade product images on hover.

## 40. Retail Shopping: Home Goods & Specialty
**Vibe:** Curated, artisanal, tactile.
**Palette:** Ochre/terracotta accent (H 50–75), natural linen neutrals, warm ground.
**Type:** Pairing 6 or 1. **Imagery:** Product photography on natural material backgrounds, warm and textured.
**Layout:** Split Hero → Masonry Gallery → Founder Letter (brand story).
**Radius:** `rounded-md` to `rounded-xl`. **Icon:** Line, warm.
**Signature:** Organic asymmetric grids; category navigation with lifestyle imagery.
**Motion:** Unhurried, organic. Staggered masonry reveals.

## 41. Sports, Fitness & Recreation
**Vibe:** Energetic, motivational, high-performance.
**Palette:** High-energy orange (H 15–35) or volt (H 90), dark ground, maximum contrast.
**Type:** Pairing 11, heavy display weights, tight tracking. **Imagery:** High-contrast action photography, motion-blur encouraged.
**Layout:** Cinematic Full-Bleed hero → Stat Band → Numbered Process Row (class/program structure).
**Radius:** `rounded-md`. **Icon:** Filled, bold.
**Signature:** Diagonal lines and dynamic angles; large stat counters; class/schedule grids.
**Motion:** Fast and punchy (400ms). Count-ups. Aggressive hover states.

## 42. Yoga, Pilates & Studio Fitness
**Vibe:** Calm intensity — disciplined but never harsh.
**Palette:** Soft sage or clay (H 100–140 or 20–40), warm-neutral ground, low chroma.
**Type:** Pairing 1 or 8. **Imagery:** Natural-light studio and practice photography, soft and unposed.
**Layout:** Split Hero → Numbered Process Row (class levels) → Timeline (class schedule).
**Radius:** `rounded-3xl`. **Icon:** Thin line.
**Signature:** A class-schedule component styled as a first-class UI element, not a plain table; soft instructor portraits in circular frames.
**Motion:** Slow, breath-paced — favor long, soft transitions over anything punchy.

## 43. Sustainability, CleanTech & Renewable Energy
**Vibe:** Optimistic, credible, technically serious — hope backed by data.
**Palette:** Fresh green or sky blue (H 130–160 or 190–210), bright clean ground.
**Type:** Pairing 11 or 3. **Imagery:** Real installation/facility photography, natural light, avoid stock-earth-in-hands clichés.
**Layout:** Data/Map Hero (impact visualization) → Stat Band (emissions/output data) → Bento Grid.
**Radius:** `rounded-xl`. **Icon:** Line, clean.
**Signature:** A live-feeling impact counter grounded in real, sourced data; a process diagram showing how the technology works.
**Motion:** Smooth, optimistic — count-ups central to credibility here.

## 44. Travel, Tourism & Hospitality
**Vibe:** Wanderlust-inducing, immersive, experiential.
**Palette:** Sea/sky accent (H 190–215), sun-bleached sand neutrals.
**Type:** Pairing 1 or 5. **Imagery:** Full-viewport destination photography, consistent warm/sun-bleached grade.
**Layout:** Cinematic Full-Bleed hero (parallax) → Masonry Gallery (destinations) → Interactive Map Block.
**Radius:** `rounded-2xl`. **Icon:** Line.
**Signature:** Image-dominant destination cards; interactive itinerary components; carousels with peek-through.
**Motion:** Immersive. Strong (but capped) parallax, slow zoom, layered depth.
