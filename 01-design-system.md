# Contemporary Design Systems

Structural design systems, interactive patterns, and thematic direction across industries.

Each industry entry below specifies **Vibe, Palette, Type, Radius, Signature Detail, and
Motion**. The original version of this document was deliberately color- and type-neutral;
that neutrality was the main reason output looked generic, so each entry now commits to a
direction. Treat these as strong defaults to tune, not constraints.

Cross-references: palettes → `02-color-system.md`, pairing numbers → `03-typography.md`,
motion vocabulary → `04-motion.md`.

---

## General Styling Rules (Apply to All Industries)

* **Borders:** Incredibly subtle — hairlines, never harsh lines. Dark themes use very
  low-opacity light borders (`border-white/10`). Prefer separating content with whitespace and
  background shifts over drawing a border at all.
* **Shadows:** Soft and diffused. For floating elements use expansive, low-opacity shadows
  (`0 24px 60px -20px`) rather than Tailwind's default `shadow-lg`. Tint the shadow with the
  palette hue instead of using gray. Dark themes use subtle luminous glows.
* **Gradients:** Subtle. Blend analogous shades; vary lightness more than hue. Glassmorphism
  uses heavy background blur with low-opacity fills. Never gradient-fill headline text.
* **Spacing:** Generous whitespace is non-negotiable. Major sections get `py-24` to `py-40`.
  **Vary section rhythm** — uniform padding on every section produces a metronome.
* **Border Radius:** Match the vibe and then apply it consistently across every card, button,
  input, and image. Organic/friendly: highly rounded. Corporate: moderate. Editorial/luxury:
  sharp or minimal. Mixing radii arbitrarily is a visible tell.
* **Images:** Always `object-cover` with a deliberate aspect ratio. Any text over an image
  requires a scrim overlay. Consider blend modes and duotone treatments for editorial feel.
* **Hover States:** Every interactive element needs one. Elevate with subtle translation or
  shadow depth. Transition specific properties, never `transition-all`.
* **Buttons:** Primary buttons must feel substantial — generous padding, clear hierarchy,
  micro-animation on hover. Minimum 48px tall. Avoid thin, timid buttons.
* **Layout:** Default to asymmetry. Offset grids, uneven column splits (5/7 over 6/6), images
  that bleed off one edge. Centered-everything is the strongest generated-site tell.
* **Composition:** One dominant element per viewport. If three things compete, two shrink.

---

## 1. Agriculture, Forestry & Mining
**Vibe:** Grounded, rugged, honest, connected to the land. Natural refinement.
**Palette:** Moss/olive accent (H 120–145), warm bark neutrals, bone ground.
**Type:** Pairing 3 or 11. **Radius:** Minimal — `rounded-sm` to `rounded-md`.
**Signature:** Textured backgrounds with subtle grain overlays. Warm-filtered photo treatments.
Section dividers as topographic contour line art.
**Motion:** Slow reveals (800ms). Gentle parallax on landscape imagery.

## 2. Automotive Sales, Repair & Parts
**Vibe:** Bold, kinetic, high-contrast, performance-driven.
**Palette:** Near-black ground (`oklch(16% .012 250)`), ember orange (H 25) or electric blue
(H 250) accent. Full dark theme.
**Type:** Pairing 11, heavy weights. **Radius:** `rounded-none` to `rounded-md` — angular.
**Signature:** Diagonal slashes and angled section dividers. Animated gradient lines like
racing stripes on borders. Metallic sheen gradient on card hover. Background-blur panels over
dramatic hero photography.
**Motion:** Faster than default (400–500ms reveals). Horizontal slide-ins. Speed-line accents.

## 3. Beauty & Personal Care
**Vibe:** Elevated, sensorial, effortlessly chic.
**Palette:** Bone/clay ground, burnished copper accent (H 30–50), very low chroma throughout.
**Type:** Pairing 1 or 8. **Radius:** `rounded-none`/`rounded-sm`; circles for portraits.
**Signature:** Editorial whitespace. Product images with soft drop shadows. Subtle hero
parallax. Circular image masks. Thin separator rules.
**Motion:** Very slow, soft fades. Minimal translation (16px). Nothing bouncy.

## 4. Business & Professional Services
**Vibe:** Confident, refined, trustworthy, forward-thinking.
**Palette:** Deep navy/slate (H 240–260), near-white ground, restrained accent.
**Type:** Pairing 11 or 3. **Radius:** `rounded-lg` to `rounded-xl` — moderate.
**Signature:** Asymmetric grids with offset image placement. Fine dot-grid background pattern.
Decorative data-visualization elements. Cards with vertical accent border stripes.
**Motion:** Crisp, restrained. 600ms reveals, subtle stagger. Count-up stats.

## 5. Computers, Technology & Electronics
**Vibe:** Precise, futuristic, intelligently minimal.
**Palette:** Charcoal ground (`oklch(18% .015 250)`), cyan/electric accent (H 190–230). Dark.
**Type:** Pairing 11 + a mono for labels. **Radius:** `rounded-lg`, consistent.
**Signature:** Glow effects on key elements. Fine grid-line backgrounds (graph paper). Monospace
tags and labels. Animated gradient borders on cards.
**Motion:** Precise, quick. Terminal-style reveals. Subtle glow pulse on primary CTA.

## 6. Construction, Architecture & Engineering
**Vibe:** Structural, bold, blueprint-precise. Industrial sophistication.
**Palette:** Concrete neutrals, safety amber accent (H 60–80), steel undertone.
**Type:** Pairing 4 or 11. **Radius:** `rounded-none` — strictly geometric.
**Signature:** Strong geometric grids. Angular section transitions. Blueprint-style thin-line
illustrations. Progress-bar accent strips. Oversized section numbers as background watermarks.
**Motion:** Structural — elements draw in via clip-path or width, not fade.

## 7. Education & Childcare
**Vibe:** Warm, approachable, optimistic, intellectually stimulating.
**Palette:** Warm sun accent (H 45–70) with a cool secondary (H 200). Bright, high-value ground.
**Type:** Pairing 10 or 5. **Radius:** `rounded-3xl` everywhere.
**Signature:** Rounded corners throughout. Playful line-icon illustrations. Alternating section
background shades for rhythm. Very low-opacity dot/confetti patterns. Generous card padding.
**Motion:** Friendly — `spring` easing permitted here. Gentle scale on hover.

## 8. Entertainment, Arts & Nightlife
**Vibe:** Electric, immersive, unapologetically bold.
**Palette:** True dark ground, magenta/violet accent (H 300–330), high chroma on small areas.
**Type:** Pairing 2 or 11. **Radius:** `rounded-2xl` with glass fills.
**Signature:** Gradient text permitted **here only**. Glassmorphism cards, high blur, low-opacity
borders. Animated floating background shapes. Neon glow hover states.
**Motion:** The most expressive of any industry. Layered parallax, glow pulses, marquees.

## 9. Events, Weddings & Photography
**Vibe:** Editorial, romantic, gallery-quality. Imagery dominant, typography unobtrusive.
**Palette:** Ivory/champagne ground, blush accent (H 20–40), near-zero chroma neutrals.
**Type:** Pairing 5 or 2; Pairing 9 as signature accent only. **Radius:** `rounded-none`.
**Signature:** Masonry photo grids with varied aspect ratios. Full-bleed hero with slow zoom.
Elegant thin-line dividers. Italic pull quotes. Lightbox interactions. Generous letter-spacing
on uppercase labels.
**Motion:** Slow (800ms), fades only. Ken Burns drift on hero. Image-wipe reveals.

## 10. Financial Services & Banking
**Vibe:** Authoritative, secure, premium. Stable with a modern edge.
**Palette:** Deep teal/petrol (H 200–230) or forest (H 150). Dark sections as punctuation.
**Type:** Pairing 3 or 11. **Radius:** `rounded-xl`, precise.
**Signature:** Subtle gradient-mesh hero backgrounds. Data-forward — clean stat callouts,
thin-line charts, percentage indicators. Refined shadows, exact alignment. Trust badges.
**Motion:** Controlled. Count-up figures. No decorative movement.

## 11. Food & Beverage: Dining
**Vibe:** Appetite-driven, warm, inviting.
**Palette:** Terracotta/ember accent (H 20–40), cream ground, rich saturated photography.
**Type:** Pairing 1 or 5. **Radius:** `rounded-md` to `rounded-xl`.
**Signature:** Full-width food photography with warm grading. Menu layouts with dotted leaders
between item and price. Decorative herb/utensil illustrations. Textured paper backgrounds.
**Motion:** Warm and unhurried. Slow image zoom on hover.

## 12. Food & Beverage: Groceries & Retail
**Vibe:** Fresh, clean, wholesome, organic.
**Palette:** Leaf green accent (H 130–150), kraft neutrals, bright ground.
**Type:** Pairing 10 or 11. **Radius:** `rounded-2xl`; `rounded-full` badges.
**Signature:** Clean product grids with ample padding. Rounded category badges. Organic
decorative motifs. Kraft-paper texture accents. Color-coded category system.
**Motion:** Light and quick. Hover lift on product cards.

## 13. Government, Community & Non-Profits
**Vibe:** Accessible, trustworthy, inclusive, clear. Dignified without being cold.
**Palette:** Civic blue (H 230–250), maximum contrast, minimal decoration.
**Type:** Pairing 11 or 3. **Radius:** `rounded-md`.
**Signature:** High contrast ratios throughout. Strong heading scale and clear hierarchy.
Prominent CTAs with generous touch targets. Landmark-driven structure. Icon + text pairings.
**Motion:** Minimal. Accessibility outranks expression — reveals only, short distances.

## 14. Healthcare & Medical Services
**Vibe:** Clean, calming, clinically precise. Trust through clarity and restraint.
**Palette:** Calm teal (H 180–200), soft tinted surfaces, very light ground.
**Type:** Pairing 8 or 11. **Radius:** `rounded-2xl`, soft throughout.
**Signature:** Soft rounded cards on light tinted backgrounds. Clean service iconography.
Generous body line-height (1.7+). Subtle pulse on primary CTA. Calming gradient wash on hero.
**Motion:** Gentle, slow, reassuring. Nothing abrupt.

## 15. Home Maintenance, Trades & Repairs
**Vibe:** Dependable, straightforward, professional.
**Palette:** Work orange (H 30–50), slate neutrals, high contrast.
**Type:** Pairing 11, bold weights. **Radius:** `rounded-lg`.
**Signature:** Before/after image sliders. Star rating displays. Bold stat callouts for years
and projects. Chunky confident buttons. Service cards with icon headers. Trust badges.
**Motion:** Direct and quick. Functional, not decorative.

## 16. Industrial, Manufacturing & Wholesale
**Vibe:** Powerful, efficient, precision-engineered. Scale and technical capability.
**Palette:** Steel blue (H 210–230), graphite ground, dark sections.
**Type:** Pairing 11 + mono for specs. **Radius:** `rounded-sm` to `rounded-md`.
**Signature:** Full-width facility photography with dark overlays. Technical spec tables with
clean grid lines. Large numerical capacity callouts. Diagonal accent lines. Capacity bars.
**Motion:** Mechanical precision. Staggered reveals, count-ups.

## 17. Legal Services
**Vibe:** Authoritative, distinguished, reassuringly competent.
**Palette:** Oxblood (H 20–30) or deep indigo (H 250–270) with brass accent. Rich, low-value.
**Type:** Pairing 3 or 7. **Radius:** `rounded-none` to `rounded-sm`.
**Signature:** Sophisticated two-column layouts. Vertical accent rules. Pull quotes from case
results. Practice-area cards with bottom-border accents. Consistently framed headshots.
**Motion:** Restrained and slow. Fades only, no translation on text.

## 18. Logistics, Transportation & Storage
**Vibe:** Dynamic, efficient, globally connected. Movement and reliability.
**Palette:** Signal blue (H 200–220), clean neutrals.
**Type:** Pairing 11. **Radius:** `rounded-lg`.
**Signature:** Map-inspired elements — subtle grid lines, route-line dividers. Tracking-timeline
components. Animated counter stats. Line-art iconography. Dashboard-style data displays.
**Motion:** Directional — elements enter along the axis of travel. Animated route paths.

## 19. Media, Communications & Publishing
**Vibe:** Editorial, intelligent, culturally aware. Typographic craft signals credibility.
**Palette:** Paper white ground, editorial red accent (H 0–20), near-black ink.
**Type:** Pairing 3 or 7 — typography is the design here. **Radius:** `rounded-none`.
**Signature:** Strong display type scale. Multi-column article layouts. Drop caps on long-form.
Thin rule lines. Distinct metadata styling. Article grids mixing featured and standard cards.
**Motion:** Nearly none. Let the typography carry it. Fades only.

## 20. Pets, Animals & Veterinary Services
**Vibe:** Warm, playful, reassuringly caring.
**Palette:** Warm honey (H 35–60) or soft mint (H 170), bright friendly ground.
**Type:** Pairing 10 or 5. **Radius:** `rounded-3xl` everywhere.
**Signature:** Highly rounded cards, buttons, images. Soft warm shadows. Very low-opacity animal
silhouettes. Photo galleries with white-border frames and slight rotation. Friendly icons.
**Motion:** Playful — `spring` easing permitted. Gentle scale and tilt on hover.

## 21. Real Estate & Property Management
**Vibe:** Aspirational, polished, high-value. Property photography leads.
**Palette:** Warm stone ground, brass accent (H 40–60), charcoal dark sections.
**Type:** Pairing 4 or 6. **Radius:** `rounded-lg`, restrained.
**Signature:** Large hero images with overlay text. Property card grids with prominent pricing
and image hover zoom. Map integration with custom markers. Refined filter bars. Detail stat
blocks.
**Motion:** Smooth and premium. Slow image zoom. Parallax on hero.

## 22. Retail Shopping: Apparel & Jewelry
**Vibe:** Minimalist, editorial, aspirational. Lookbook design — the product is the interface.
**Palette:** Near-monochrome, chroma below 0.02. The product supplies the color.
**Type:** Pairing 2, 7, or 6. **Radius:** `rounded-none`.
**Signature:** Full-bleed product photography. Minimal UI. Hover-to-reveal quick-shop overlays.
Precise spacing and alignment. Thin tracking on nav and category labels. Fade-in on scroll.
**Motion:** Slow, sparse, confident. Crossfade product images on hover.

## 23. Retail Shopping: Home Goods & Specialty
**Vibe:** Curated, artisanal, tactile.
**Palette:** Ochre/terracotta accent (H 50–75), natural linen neutrals, warm ground.
**Type:** Pairing 6 or 1. **Radius:** `rounded-md` to `rounded-xl`.
**Signature:** Organic asymmetric grids. Product photography on natural material backgrounds.
Earthy textured backgrounds. Category navigation with lifestyle imagery. Brand storytelling
sections.
**Motion:** Unhurried, organic. Staggered masonry reveals.

## 24. Sports, Fitness & Recreation
**Vibe:** Energetic, motivational, high-performance.
**Palette:** High-energy orange (H 15–35) or volt (H 90), dark ground, maximum contrast.
**Type:** Pairing 11, heavy display weights, tight tracking. **Radius:** `rounded-md`.
**Signature:** High-contrast hero with action photography. Diagonal lines and dynamic angles.
Large stat counters. Class/schedule grids. Progress indicators. Split-screen layouts.
**Motion:** Fast and punchy (400ms). Count-ups. Aggressive hover states.

## 25. Travel, Tourism & Hospitality
**Vibe:** Wanderlust-inducing, immersive, experiential.
**Palette:** Sea/sky accent (H 190–215), sun-bleached sand neutrals.
**Type:** Pairing 1 or 5. **Radius:** `rounded-2xl`.
**Signature:** Full-viewport hero with parallax. Image-dominant destination cards. Interactive
itinerary components. Star and review aggregates. Booking date pickers. Map integration.
Carousels with peek-through.
**Motion:** Immersive. Strong (but capped) parallax, slow zoom, layered depth.
