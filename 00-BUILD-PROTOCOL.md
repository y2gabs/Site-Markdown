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

## Phase 1 — Choose the Layout Archetypes and Signature Move

Look up the industry's entry in `01-design-system.md`. It names a Hero pattern and one or two
Section patterns from the **Layout Archetype** library — use those rather than improvising
section structure from scratch. If no industry entry fits, pick archetypes directly from the
library that match the Vibe.

Then decide **the one thing that will make this site memorable** — the Signature line from the
industry entry, or an equivalent you invent.

Examples: a topographic contour divider; a full-bleed image that scrubs on scroll; oversized
background section numerals; an accent panel that wipes away to reveal each image; a menu with
dotted price leaders; a draggable before/after slider.

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

## Phase 7 — Completeness Gate, Then the Subtraction Pass

**Before auditing quality, verify completeness.** List every section and every modal/dialog
this build committed to in Phase 1–2, then check the rendered output against that list one item
at a time. On a page with many sections and several modals, it's easy for one to get dropped
silently mid-build without anything looking obviously wrong — the rest of the page still
renders fine. This is a distinct failure mode from a quality problem and needs its own explicit
pass, not a hope that it would have been noticed.

For this framework's current default build (the 14-section local-business structure in
`05-copywriting-cro.md` §1), check against that section's numbered table and four-modal table
directly rather than reconstructing the list from memory — Sections 9 (News), 11 (Location
Map), and 12 (FAQ) are the ones most likely to go missing precisely because they land late in
the build and nothing else on the page visibly breaks when they're absent.

While checking, also confirm no two sections are independently claiming the same content — a
"Visit Us" block that restates an address already covered by the Contact section and a Location
Map is the classic version of this, and it produces duplicated or conflicting information rather
than reinforcement.

**Then, and only then, read `08-anti-patterns.md` end to end** and audit the build against it,
answering the five final questions honestly.

Finally, remove things. Almost every generated page is improved by deleting a decorative
element, cutting a section that repeats another's function, reducing accent usage, and
increasing whitespace. If a change is needed and the direction is unclear, **subtract**.

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
11. No contact form as the default — `tel:`/`mailto:`/directions links first (see
    `05-copywriting-cro.md` §9); reach for a form only where structured intake is genuinely
    required.
12. No section or modal quietly missing from the shipped build — run the Phase 7 completeness
    gate before calling anything done.
13. No hero, About, or contact section that ships with only its desktop alignment. If the
    pattern specifies `text-center md:text-left` (or similar) for mobile, that class must
    appear in the actual markup — verify it's present, don't assume it survived from the
    snippet. See `06-ui-ux-accessibility.md` §8.
14. No CTA button crowded next to the mobile hamburger icon. The hamburger's own drawer, or the
    hero directly below it, already carries the primary CTA — a second one beside the toggle is
    redundant and cramps two touch targets together.
15. No icon used against its established meaning — a hamburger toggle drawn as a chat bubble, a
    close button drawn as a checkmark, and so on. `01-design-system.md`'s Iconography section
    already requires one consistent icon *set*; this is the companion rule that each icon must
    also be the *correct* glyph for what it does, not a lookalike swapped in by mistake.
16. No literal special-character glyph (`×`, `→`, `←`, `✓`, curly quotes) typed directly into
    JSX text for an icon, close button, or arrow. These are the most fragile bytes in any
    copy/paste or AI-regeneration pipeline and silently become `?` the moment something in that
    pipeline re-saves the file under a different encoding. Inline SVG only — see
    `07-react-tailwind-snippets.md`'s Environment Gotchas.
