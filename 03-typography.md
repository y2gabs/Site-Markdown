# Typography

Typography carries more of the "is this expensive?" signal than color, layout, or animation.
The original font list was a good instinct but gave no pairing logic, no scale, and no
setting rules — so output defaulted to one sans at three sizes.

---

## 1. Always Pair. Never Solo.

Use **two** families: a **Display** face for headings and a **Text** face for everything else
(body, UI, labels, buttons). Optionally a third **Mono** for technical accents only.

A pairing works when the two faces differ clearly in *skeleton* (serif vs. sans, or
high-contrast vs. low-contrast) but agree in *era and warmth*. Two similar sans faces look
like a mistake; a serif and a sans look like a decision.

### Vetted Pairings

| # | Display | Text | Reads as |
|---|---|---|---|
| 1 | Cormorant Garamond | Work Sans | Luxury editorial, wellness, beauty |
| 2 | Italiana | Lato | Fashion, jewelry, high-end events |
| 3 | Noto Serif Display | Questrial | Modern editorial, publishing |
| 4 | Aboreto | Cantarell | Architectural, minimal, gallery |
| 5 | Arapey | Raleway | Romantic, weddings, florals |
| 6 | Tenor Sans | Lato | Quiet luxury, apparel, interiors |
| 7 | Antic Didone | Work Sans | Fashion-forward, high contrast |
| 8 | Nanum Myeongjo | Questrial | Calm, spa, mindfulness, tea |
| 9 | Petite Formal Script | Tenor Sans | Signature accent only — never body |
| 10 | Gotu | Lato | Soft geometric, friendly-premium |
| 11 | Work Sans (700) | Work Sans (400) | Single-family fallback via weight contrast |

Pairing 11 is the honest escape hatch: if a single family is required, create hierarchy with
**dramatic weight and size contrast**, never with two similar families.

### Industry → Pairing

Scoped to health & wellness for now (see `01-design-system.md`) — the full cross-industry list
lives in that commit's history; restore via `git revert` rather than re-deriving it.

- Beauty / Personal Care / Yoga / Studio Fitness → 1, 8
- Healthcare / Dental & Orthodontics → 8, 11
- Senior Living / Elder Care → 1, 6 *(generous size for readability)*
- Sports, Fitness & Recreation → 11 *(heavy display weights, tight tracking)*
- Massage Therapy & Bodywork → 8
- Physiotherapy & Sports Rehab → 8, 11
- Hair Salon & Makeup Studio → 2, 7
- Brow, Lash & Skin Studio → 6
- Nail Salon & Spa → 10

Load only the weights actually used — typically Display 400/600 and Text 400/500/600.

---

## 2. Fluid Type Scale

Never hard-code heading sizes at each breakpoint. Use `clamp()` so type scales continuously.
A **1.25 ratio** for dense/corporate, **1.333** for editorial, **1.5** for dramatic.

```js
// tailwind.config → theme.extend.fontSize
'display-xl': ['clamp(3rem, 8vw, 7rem)',      { lineHeight: '0.95', letterSpacing: '-0.03em' }],
'display-lg': ['clamp(2.5rem, 6vw, 5rem)',    { lineHeight: '1.0',  letterSpacing: '-0.025em' }],
'display-md': ['clamp(2rem, 4.5vw, 3.5rem)',  { lineHeight: '1.08', letterSpacing: '-0.02em' }],
'display-sm': ['clamp(1.5rem, 3vw, 2.25rem)', { lineHeight: '1.2',  letterSpacing: '-0.01em' }],
'body-lg':    ['clamp(1.05rem, 1.4vw, 1.25rem)', { lineHeight: '1.65' }],
'overline':   ['0.75rem', { lineHeight: '1', letterSpacing: '0.18em' }],
```

---

## 3. Setting Rules

**Tracking scales inversely with size.**
- Display (> 40px): `-0.02em` to `-0.04em`. Large type set at default tracking looks loose
  and amateur — this is the most commonly missed detail in generated pages.
- Body: `0`.
- Small caps / overlines (< 14px, uppercase): `+0.14em` to `+0.20em`.

**Leading scales inversely with size.**
- Display: `0.95`–`1.1`.
- Subheads: `1.3`–`1.4`.
- Body: `1.6`–`1.75`. Generous leading is a luxury signal.

**Measure (line length).**
- Body copy: **60–75 characters** → `max-w-[65ch]`. Never let paragraphs run the full width
  of a wide container.
- Display headlines: **20–36 characters** → `max-w-[16ch]` for a hero H1. Forcing a headline
  to break across 2–3 lines is a compositional choice, not an accident. Those authored breaks
  double as the animation units for the line-mask heading reveal (`04-motion.md` §11), so a
  heading composed into deliberate lines gets a better entrance for free.

**Weight.**
- Body at 400. UI labels at 500.
- Display serifs usually look best at **400 or 500**, not 700 — heavy weights destroy the
  contrast that makes a serif elegant.
- Only geometric sans display type wants 600–800.

**Numerals.** Use `font-variant-numeric: tabular-nums` for stats, prices, and counters so
digits don't jitter during count-up animations. Tailwind: `tabular-nums`.

---

## 4. The Overline

A short uppercase, letterspaced, accent-colored label above a section heading. It is the
cheapest, highest-return typographic detail available and appears on nearly every well-
designed site.

```html
<p class="text-overline uppercase font-medium text-accent">02 — Our Process</p>
<h2 class="mt-4 font-display text-display-md text-ink max-w-[18ch]">…</h2>
```

Optionally prefix with a section number, or precede with a short accent rule
(`<span class="inline-block h-px w-8 bg-accent align-middle mr-3"></span>`).

---

## 5. Editorial Details Worth Using

- **Drop cap** on a long-form opening paragraph:
  `first-letter:float-left first-letter:font-display first-letter:text-7xl
   first-letter:leading-[0.8] first-letter:mr-3 first-letter:mt-1`
- **Pull quote:** display face, italic, large, with a thin accent rule above — not quotation-
  mark clip art.
- **Mixed-style headline:** set one word of the H1 in the display face's italic to create
  emphasis without color. Far more sophisticated than a gradient.
- **Hanging punctuation / optical alignment:** nudge a leading quote mark with `-ml-2` so the
  text edge stays visually flush.
- **Balanced headings:** `text-balance` on headlines and `text-pretty` on paragraphs prevents
  orphans. Cheap, and it works.

---

## 6. Typographic Anti-Patterns

- All-caps for anything longer than 4 words.
- Center-aligned body paragraphs. Center headings if you must; never center a paragraph over
  two lines.
- Justified text on the web — it creates rivers with no hyphenation engine.
- More than three type sizes visible in one section.
- Letter-spacing added to lowercase body text.
- A delicate high-contrast serif (Italiana, Antic Didone) used below ~20px, where its thin
  strokes disappear.
