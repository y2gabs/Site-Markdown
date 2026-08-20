# Anti-Patterns — The "Generated Site" Tells

Read this file **last, before finalizing**, and audit the build against it. Every item below
is a specific, recognizable marker of machine-generated web design. Avoiding them does more
for perceived quality than adding any new effect.

---

## 1. Color Tells

| ❌ Tell | ✅ Instead |
|---|---|
| `from-purple-600 to-indigo-600` on anything | One accent hue from `02-color-system.md` |
| Stock `blue-500` / `gray-900` / `slate-800` | A palette with chroma pushed into the neutrals |
| Pure `#000` text on pure `#fff` | `oklch(22% .014 75)` on `oklch(98% .006 85)` |
| Gradient headline text | Solid ink, with one word in display italic |
| Three cards in three different accent colors | One accent; differentiate by content |
| Rainbow / high-contrast-hue gradients | Analogous hues, lightness-driven |

## 2. Layout Tells

- **Everything centered.** A page where every section is a centered heading + centered
  paragraph + centered 3-card grid is the signature of generated output. Break it: left-align
  section headers, offset images, use asymmetric two-column splits (5/7, not 6/6).
- **Uniform section rhythm.** Every section the same height with the same `py-24` produces a
  metronome. Vary: a tall hero, a tight band, a generous editorial block, a short dark CTA.
- **The eternal 3-card grid.** If three feature cards appear more than once on a page, one of
  them should become something else — a two-column feature with a real image, an offset list,
  a numbered process row, or a stat band.
- **Full-width everything.** Not every section needs the same container. Alternate a
  `max-w-6xl` container with a full-bleed image band and a narrow `max-w-2xl` editorial column.
- **Perfectly symmetric hero.** Offset it. Let the image bleed off one edge.

## 3. Component Tells

- **Emoji as icons** (🚀 ⚡ 💡 ✨). Instantly reads as generated. Use inline SVG line icons at
  1.5px stroke, or a numbered/typographic treatment instead.
- **`rounded-lg` on everything.** Timid and default. Commit to a radius language:
  `rounded-none`/`rounded-sm` (editorial, luxury), `rounded-2xl`/`rounded-3xl` (soft, modern),
  `rounded-full` for pills and avatars. Then apply it consistently.
- **`shadow-lg` / `shadow-xl`.** Harsh and gray. Use expansive low-opacity custom shadows:
  `shadow-[0_24px_60px_-20px_oklch(22%_0.03_75_/_0.18)]`.
- **Gray placeholder rectangles** where photography belongs. If no image is available, use a
  tinted accent wash with a subtle pattern or a typographic composition — never `bg-gray-200`.
- **Borders on everything.** Prefer separation by whitespace and background shift; use
  hairlines (`border-line`) only where genuine division is needed.
- **A visible scrollbar-width layout shift** when a modal opens.

## 4. Copy Tells

- Lorem Ipsum. (Already banned in `05-copywriting-cro.md` — it remains the #1 violation.)
- "Welcome to our website" / "We are a company that…"
- Buttons reading "Learn More," "Submit," "Click Here," "Get Started" with no object.
- Testimonials from "John D." with no title, company, or photo.
- Feature headings that name the feature instead of the outcome ("Advanced Analytics
  Dashboard" vs. "Know what's working by Monday morning").
- Three testimonials of identical length — real quotes vary.
- Stat blocks with implausibly round numbers (100%, 1000+, 24/7 on every site).

## 5. Motion Tells

- No motion at all.
- Elements re-animating every time they scroll into view.
- `transition-all duration-300` on every element indiscriminately.
- Bounce easing on a professional service site.
- Parallax so strong the background visibly detaches.

## 6. Accessibility Tells

- White text directly on a photo with no scrim.
- `outline-none` with nothing replacing it.
- `text-gray-400` on `bg-gray-100`.
- Icon-only buttons with no `aria-label`.
- Clickable `<div>`s instead of `<button>`/`<a>`.
- A sticky header that covers content when jumping to an anchor (fix with `scroll-mt-24`).

## 7. Technical & Build Tells

Bugs and omissions specific to how these pages get assembled — easy to introduce, easy to miss
in a visual review because the page still *looks* finished.

- **A scroll-triggered hero built with `height: 200vh` + `position: sticky`.** Produces 100vh
  of dead scrolling where nothing visibly happens. Use the Fixed + Slide-Over technique instead
  — see `04-motion.md` §9.
- **A marquee track sized with a fixed percentage** (`w-[200%]`) instead of its actual content
  width (`w-max`). The `-50%` loop only lands on the seam when the track is exactly twice one
  copy's rendered width — a percentage width drifts, and the loop visibly stutters or gaps.
  See `04-motion.md` §10.
- **A contact form where direct links would convert better and need no backend.** See
  `05-copywriting-cro.md` §9.
- **A required section or modal silently missing from the shipped build.** On a page with many
  sections, it's common for one (an FAQ, a map, a specific modal) to get dropped somewhere in
  the process without anyone noticing, because the rest of the page still renders fine. Count
  what Phase 1 committed to against what actually renders before calling it done.
- **Two sections independently claiming the same content** — e.g., a "Visit Us" section *and* a
  Location Map section that both restate the address, producing duplicated or conflicting
  information. Each fact should have exactly one home on the page.
- **Pagination or "show more" controls gated on a hardcoded count** (`items.length === 9`)
  instead of derived from the actual data (`items.length > PAGE_SIZE`). Works by coincidence on
  the demo data, breaks the day real content is dropped in.
- **A gallery lightbox whose counter/navigation only knows about the currently visible page** of
  a paginated grid, rather than the full underlying array — makes "next" dead-end at the edge
  of a page instead of crossing into the next one.
- **Four modals on one page, each with different overlay opacity, radius, or close behavior.**
  Build every dialog on one shared shell; see `06-ui-ux-accessibility.md` §9.
- **A line-mask heading reveal that clips its own descenders.** An `overflow-hidden` mask cuts
  the tails off `g`, `y`, `p`, `j` and the overhang of italics. Pad the mask and pull it back
  (`pb-[0.12em] -mb-[0.12em]`). This one hides from casual review because it only appears on
  headings that happen to contain a descender — check a heading that does.
- **Per-character or per-word text reveals.** Reads as a gimmick on a marketing page, and it
  shatters the heading into dozens of nodes that wreck text selection and screen-reader output.
  Mask whole lines instead; see `04-motion.md` §11.
- **Direction-aware motion implemented as re-hiding.** Entering from the side the reader
  approached from is good; animating content back *out* when it leaves the viewport, or
  re-animating it on every pass, is the amateur tell from `04-motion.md` §3. Direction picks
  which side an element enters from on its single permanent reveal — nothing is ever hidden
  again.
- **A raw linear scroll-to-value mapping presented as "eased" motion** (`1 - scrollY / 600`
  with no curve applied). Constant velocity the whole way through isn't easing — it's a ratio.
  Run the `0→1` scroll progress through a curve like `smoothstep` before mapping it to opacity
  or scale; see `04-motion.md` §9.
- **A `transition-*` utility class on an element that also has a per-frame scroll-linked inline
  `style`.** The element ends up chasing a constantly-moving target, which reads as laggy or
  stepped — the opposite of the smoothness the transition class was added to achieve. Delete
  the class; the easing belongs in the value's own curve, not in CSS.
- **A hero, About, or contact section that quietly drops its documented mobile divergence** —
  shipping with only the desktop alignment (`items-center`, left-aligned text, image visible)
  at every breakpoint because the `md:` variants were simplified away when hand-copying the
  pattern. Copy the class list exactly rather than approximating it from memory; see
  `06-ui-ux-accessibility.md` §8.
- **A floating panel — chat widget, cookie banner, promo toast — anchored to a screen edge at a
  fixed pixel width with no mobile-specific treatment.** It overflows off the opposite edge the
  moment its width exceeds the room between it and that edge on a narrow phone. See `ChatWidget`
  in `07-react-tailwind-snippets.md` §25 for the centered, viewport-clamped mobile pattern.
- **A CTA button placed directly beside the mobile hamburger icon.** Crowds two touch targets
  together and duplicates a CTA the hero or the drawer itself already carries; see
  `06-ui-ux-accessibility.md` §8.
- **The mobile nav toggle drawn as a chat bubble (or any icon other than a hamburger/close
  pair).** A message-bubble glyph reads as "open chat," not "open menu" — swapping it in for
  the nav toggle, easy to do if a build's icon set puts both glyphs near each other, breaks the
  one piece of mobile navigation every visitor already knows how to find. The toggle is a
  hamburger (three or two horizontal lines) that becomes an × when open; nothing else.

---

## The Final Audit

Before shipping, ask these five questions honestly:

1. **Could I identify the industry with the text removed?** If not, the visual system is doing
   no work — the photography, palette, and type aren't specific enough.
2. **Is there one thing here I haven't seen on a hundred other sites?** Every build needs one
   signature move (see the Signature Detail line in `01-design-system.md`).
3. **Does the eye have a clear path through each section?** One dominant element per viewport.
   If three things compete, two need to shrink.
4. **Is the accent scarce enough to still mean something?**
5. **Does it survive at 375px wide?** Not just "doesn't break" — is it still *good*?

If any answer is no, the fix is almost always **subtraction**, not addition.
