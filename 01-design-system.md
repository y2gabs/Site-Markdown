# Contemporary Design Systems

Structural design systems, layout patterns, art direction, and industry-specific direction.

Each industry entry specifies **Vibe, Palette, Type, Imagery, Layout, Radius, Icon, Signature,
and Motion**. Layout and Signature reference the pattern libraries below by name — learn those
first, then the industry table becomes a fast lookup.

**Scoped to health & wellness for now.** The full framework covers 44 industries; this build
is narrowed to the six below while work focuses on this vertical. The general sections
(Layout Archetypes, Imagery & Art Direction, Iconography, and everything in `02`–`08`) are
unchanged and still apply universally — only the industry-specific list is trimmed. Restoring
the rest is a `git revert` of the commit that narrowed it, not a rewrite.

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
  uses heavy background blur with low-opacity fills. Never gradient-fill headline text — none
  of the health & wellness industries in scope call for that exception.
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
| **Interactive Map Block** | Location(s) with custom markers — multi-location retail, service areas. ⚙ `LocationMap` |
| **Split-Screen Compare** | Two contrasting halves: before/after, us/them, old way/new way |
| **Menu/Price List** | Item + dotted leader + price, grouped by category |
| **Wizard Flow** | Multi-step modal (booking, quote request) with a progress indicator and back control. ⚙ `BookingWizard` |
| **Icon-Row Contact Block** | Phone/email/directions collapsed to a row of icon-only circular buttons — the mobile counterpart to a detailed contact list. See `06-ui-ux-accessibility.md` §8. |
| **Mobile Auto-Advance Slider** | A grid's mobile counterpart — one item shown at a time, auto-advancing, pausing after interaction. ⚙ `MobileSlider` |

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
  text, not a substitute for it. **Social profile links are the deliberate exception:** X,
  Facebook, Instagram, LinkedIn, etc. are recognized by their mark alone, so render them as
  icon-only circular buttons, never a text link list ("Facebook · Instagram · LinkedIn"). Carry
  the label in `aria-label` instead of visible text — see `SocialLinks` in
  `07-react-tailwind-snippets.md` §23.
* **Size to context:** 20–24px inline with text, 32–40px as a feature-card header, 44px minimum
  touch target for any icon-only button (social links included). Never an arbitrary size chosen
  per instance.
* Prefer a single well-made icon set (e.g., Heroicons, Phosphor, Lucide) over mixing sources —
  inconsistent stroke weight between icons is as visible a tell as inconsistent radii.

---

## Industry Directions

## 1. Beauty & Personal Care
**Vibe:** Elevated, sensorial, effortlessly chic.
**Palette:** Bone/clay ground, burnished copper (H 30–50), very low chroma.
**Type:** Pairing 1 or 8. **Imagery:** Soft-lit, low-contrast product and portrait photography, circular crops.
**Layout:** Split Hero → Before/After Slider (results) → Masonry Gallery.
**Radius:** `rounded-none`/`rounded-sm`; circles for portraits. **Icon:** Thin line.
**Signature:** Editorial whitespace; circular image masks; thin separator rules.
**Motion:** Very slow, soft fades. 16px translation max, nothing bouncy.

## 2. Healthcare & Medical Services
**Vibe:** Clean, calming, clinically precise. Trust through clarity and restraint.
**Palette:** Calm teal (H 180–200), soft tinted surfaces, very light ground.
**Type:** Pairing 8 or 11. **Imagery:** Real clinical/staff photography, soft natural light — never generic stock doctors.
**Layout:** Split Hero → Bento Grid (services/departments) → Founder Letter (from the lead clinician).
**Radius:** `rounded-2xl`, soft throughout. **Icon:** Line, clean.
**Signature:** Soft rounded cards on light tinted backgrounds; a subtle pulse on the primary CTA.
**Motion:** Gentle, slow, reassuring. Nothing abrupt.

## 3. Dental & Orthodontics
**Vibe:** Bright, precise, quietly modern — clinical without feeling cold.
**Palette:** Fresh mint or sky (H 170–200), high-key bright ground.
**Type:** Pairing 8 or 11. **Imagery:** Real before/after smile photography, consistent lighting.
**Layout:** Split Hero → Before/After Slider → Numbered Process Row (new-patient journey).
**Radius:** `rounded-2xl`. **Icon:** Line, precise.
**Signature:** A smile-gallery before/after wall; an online-booking widget as a first-class component.
**Motion:** Light, precise, reassuring.

## 4. Senior Living & Elder Care
**Vibe:** Warm, dignified, unmistakably human — never clinical-cold or infantilizing.
**Palette:** Warm neutral ground, a soft confident accent (H 30–50 or 150–170).
**Type:** Pairing 1 or 6, generous size for readability. **Imagery:** Real residents and staff, candid warmth, never stock elderly-couple-on-beach clichés.
**Layout:** Founder/Portrait Hero → Masonry Gallery (community life) → Timeline (a resident's day).
**Radius:** `rounded-2xl`. **Icon:** Line, larger than default for legibility.
**Signature:** A virtual-tour component; real family testimonials with full attribution.
**Motion:** Slow, gentle — respect larger text needs and calmer pacing throughout.

## 5. Sports, Fitness & Recreation
**Vibe:** Energetic, motivational, high-performance.
**Palette:** High-energy orange (H 15–35) or volt (H 90), dark ground, maximum contrast.
**Type:** Pairing 11, heavy display weights, tight tracking. **Imagery:** High-contrast action photography, motion-blur encouraged.
**Layout:** Cinematic Full-Bleed hero → Stat Band → Numbered Process Row (class/program structure).
**Radius:** `rounded-md`. **Icon:** Filled, bold.
**Signature:** Diagonal lines and dynamic angles; large stat counters; class/schedule grids.
**Motion:** Fast and punchy (400ms). Count-ups. Aggressive hover states.

## 6. Yoga, Pilates & Studio Fitness
**Vibe:** Calm intensity — disciplined but never harsh.
**Palette:** Soft sage or clay (H 100–140 or 20–40), warm-neutral ground, low chroma.
**Type:** Pairing 1 or 8. **Imagery:** Natural-light studio and practice photography, soft and unposed.
**Layout:** Split Hero → Numbered Process Row (class levels) → Timeline (class schedule).
**Radius:** `rounded-3xl`. **Icon:** Thin line.
**Signature:** A class-schedule component styled as a first-class UI element, not a plain table; soft instructor portraits in circular frames.
**Motion:** Slow, breath-paced — favor long, soft transitions over anything punchy.
