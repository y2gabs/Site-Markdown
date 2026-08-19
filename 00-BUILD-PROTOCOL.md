# Build Protocol

The operating procedure. Follow these phases in order. Do not begin writing markup before
Phase 2 is committed to in writing — the single most common failure is starting to code, then
inventing colors and fonts ad hoc, which produces a generic result no amount of later polish
recovers.

---

## Phase 0 — Intake

Establish before anything else. Ask only if genuinely unclear; otherwise decide and state the
assumption.

| Question | Default if unstated |
|---|---|
| Industry (map to `01-design-system.md`) | Infer from the brief |
| Primary conversion goal | Book / enquire |
| Audience | Infer from industry |
| Brand name, real copy points, real numbers | Invent plausible, industry-specific ones |
| Light, dark, or light-with-dark-punctuation | Light with dark punctuation |
| Existing brand colors / fonts | Derive from the industry table |
| Page count | Single landing page |

---

## Phase 1 — Choose the Signature Move

Before styling anything, decide **the one thing that will make this site memorable**. Pull it
from the industry's Signature Detail line, or invent something equivalent.

Examples: a topographic contour divider; a full-bleed image that scrubs on scroll; oversized
background section numerals; an accent panel that wipes away to reveal each image; a menu with
dotted price leaders.

A page with excellent fundamentals and no signature move looks *competent*. The signature move
is what makes it look *designed*. One is enough — two compete.

---

## Phase 2 — Commit the System in Writing

Write this block out explicitly before any markup. It becomes the `:root` and Tailwind config.

```
Industry:      …
Signature:     …
Palette:       --ground / --surface / --ink / --muted / --line / --accent  (OKLCH triplets)
Type:          Display = …   Text = …   (pairing # from 03-typography.md)
Radius:        rounded-…  (applied to every card, button, input, image)
Shadow:        soft / lift definitions
Motion:        base duration, easing, reveal distance
Section order: …
```

Rules that are non-negotiable at this step:
- **One** accent hue. Neutrals carry a trace of the accent's hue — never pure gray.
- **Two** type families (Display + Text). Weights limited to what's used.
- **One** radius language, applied consistently.

---

## Phase 3 — Write the Copy First

Draft all real copy before layout. Layout decisions depend on real text lengths — designing
around placeholder text and substituting real copy later breaks the composition every time.

Per `05-copywriting-cro.md`: hero headline under 8 words, PAS for the problem section, outcome-
led feature headings, specific and varied testimonials, value-driven button labels with
risk-reducing microcopy beneath. **Zero Lorem Ipsum.**

---

## Phase 4 — Build

Start from `templates/starter.html`. It already contains the Tailwind config, OKLCH bridge,
hooks, primitives, reduced-motion handling, skip link, and a full section set.

Build order: config and `:root` → nav → hero → sections in order → footer.

- Vary section rhythm — never `py-24` on every section.
- Default to asymmetry: 5/7 splits, offset images, images bleeding off one edge.
- If a three-card grid appears twice, convert the second into a different pattern.
- Every image gets a deliberate aspect ratio; every text-over-image gets a scrim.
- Wrap content in `<Reveal>` with staggered delays capped at ~6 items.

## Phase 5 — Responsive Pass

Check 375, 768, 1440, 1920. Not "does it break" — is it still good at each? Display type
scales via `clamp()`, section padding steps down, grids collapse to one column, parallax
disables below 768px, and every touch target clears 44px.

## Phase 6 — Accessibility Pass

Run the checklist in `06-ui-ux-accessibility.md` §7. Keyboard-only pass, contrast verification
(including muted text and text-over-image), semantics, focus visibility, reduced motion.

## Phase 7 — The Subtraction Pass

**Do not skip this.** Read `08-anti-patterns.md` end to end and audit the build against it,
then answer the five final questions honestly.

Then remove things. Almost every generated page is improved by deleting a decorative element,
cutting a section that repeats another's function, reducing accent usage, and increasing
whitespace. If a change is needed and the direction is unclear, **subtract**.

---

## Reading Order

| File | Read when |
|---|---|
| `00-BUILD-PROTOCOL.md` | First, always |
| `01-design-system.md` | Phase 1–2 — industry direction |
| `02-color-system.md` | Phase 2 — palette construction |
| `03-typography.md` | Phase 2 — pairing and scale |
| `04-motion.md` | Phase 4 — animation vocabulary |
| `05-copywriting-cro.md` | Phase 3 — all copy |
| `06-ui-ux-accessibility.md` | Phase 5–6 — standards |
| `07-react-tailwind-snippets.md` | Phase 4 — implementation |
| `08-anti-patterns.md` | Phase 7 — final audit |
| `templates/starter.html` | Phase 4 — starting point |

---

## Hard Rules (Never Violate)

1. No Lorem Ipsum.
2. No purple→indigo gradients. No stock `blue-500` / `gray-900`.
3. No pure black text, no pure gray neutrals.
4. No emoji used as icons.
5. No white text on an image without a scrim.
6. No `outline-none` without a visible replacement.
7. No spacing off the 8pt grid (except deliberate optical correction).
8. No touch target under 44px.
9. No animation that ignores `prefers-reduced-motion`.
10. No more than one accent hue, two type families, or one radius language.
