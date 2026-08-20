# Motion & Interaction

Motion is what separates a page that looks designed from a page that feels built. The rule is
restraint: contemporary premium motion is **slow, soft, and mostly on arrival**.

---

## 1. Easing

Never use `ease-in-out` or `linear` for UI. Define these once and use them everywhere:

```js
// tailwind.config → theme.extend.transitionTimingFunction
'out-expo':  'cubic-bezier(0.16, 1, 0.3, 1)',      // default for reveals & hovers
'out-quart': 'cubic-bezier(0.25, 1, 0.5, 1)',      // shorter UI transitions
'in-out-soft':'cubic-bezier(0.65, 0, 0.35, 1)',    // symmetric (accordions, drawers)
'spring':    'cubic-bezier(0.34, 1.56, 0.64, 1)',  // playful overshoot — use sparingly
```

`out-expo` is the workhorse. It moves fast then settles, which reads as "expensive."

## 2. Duration

| Interaction | Duration |
|---|---|
| Hover / focus state | 200–300ms |
| Button press | 100–150ms |
| Scroll reveal | 600–800ms |
| Section / page transition | 400–600ms |
| Parallax, marquee | Continuous, tied to scroll or CSS |

Anything under 150ms is invisible; anything over 900ms feels broken. Scroll reveals live at
the long end deliberately — they should feel like content settling, not popping.

---

## 3. Scroll Reveal — The Core Pattern

Use `IntersectionObserver`, **never** a scroll-position calculation per element. Reveal
**once** (`unobserve` after firing) — content that re-hides on scroll-up is an amateur tell.

Correct default: **fade + a short rise**. `opacity 0 → 1`, `translateY(24px → 0)`.

- Distance stays small. A 100px travel looks cheap; 16–32px looks intentional.
- **Never** reveal with scale, rotation, or blur on text.
- **Stagger** siblings by 60–100ms. Staggering is what makes a grid feel choreographed. Do not
  stagger more than ~6 items or the last one arrives too late.

See `useReveal` and `<Reveal>` in `07-react-tailwind-snippets.md`.

---

## 4. Hover States

Every interactive element needs one. Compose two properties, not five.

- **Cards:** lift + shadow deepen — `hover:-translate-y-1` with a shadow change. Keep scale at
  `1.02` or below if scaling at all; `hover:scale-105` on a large card is jarring.
- **Images in cards:** the *image* scales inside a fixed `overflow-hidden` frame, the card
  does not. `group-hover:scale-[1.04]` over 600–700ms. This is the single most effective
  premium hover.
- **Text links:** an underline that grows from the left, not a color change.
  ```html
  <a class="group relative inline-block">Text
    <span class="absolute -bottom-0.5 left-0 h-px w-0 bg-current
                 transition-[width] duration-300 ease-out-expo group-hover:w-full"></span>
  </a>
  ```
- **Primary buttons:** slight lift + shadow, or an accent-tone shift. Optionally an arrow that
  translates `group-hover:translate-x-1`.
- **Nav items:** the underline pattern, or an accent dot.

Always transition specific properties (`transition-[transform,box-shadow]`), not
`transition-all` — `transition-all` animates layout properties and causes jank.

---

## 5. Parallax

Subtle only. The hero background image moves at **0.15–0.4×** scroll speed; content moves at
1×. Beyond ~0.5× it detaches and looks broken.

Drive it with the `useScrollY` hook and `transform: translate3d()` — never `background-position`
or `top`. Add `will-change: transform` on the moving layer only, and disable parallax entirely
below 768px (it fights mobile scroll physics and hurts performance).

---

## 6. Micro-Details That Read as Craft

- **Sticky nav shift:** transparent over the hero, then glass + hairline border after ~40px
  scroll. Transition the background/blur/border, not the height (height changes cause reflow).
- **Count-up stats:** animate numbers when the stat block enters view. Use `tabular-nums`.
- **Marquee:** duplicate the track and translate `-50%` in a linear infinite loop for a seamless
  logo strip. Pause on hover.
- **Image reveal:** an accent-colored panel that wipes away to expose the image
  (`scaleX` origin-left → origin-right). Gallery-quality, cheap to implement.
- **Focus rings:** style them, don't remove them.
  `focus-visible:ring-2 focus-visible:ring-accent focus-visible:ring-offset-2`.

---

## 7. Reduced Motion — Non-Negotiable

Every animation must be suppressible. Include this in the base stylesheet of every build:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Additionally, gate JS-driven motion (parallax, count-ups) behind the
`usePrefersReducedMotion` hook so those effects render their final state immediately rather
than running at 0.01ms.

---

## 8. Motion Anti-Patterns

- Elements that animate *out* when scrolled past. **The one exception is the pinned hero's own
  content in §9** — a single, singular "curtain" element easing out as the page scrolls into
  view and back in as the visitor returns to the top is a deliberate, well-established pattern,
  not the failure mode this bullet targets. The failure mode is *repeating* content — a card in
  a grid, a section further down the page — re-animating every time it crosses the viewport
  edge. A pinned hero never repeats; there is exactly one of it, and it never leaves the DOM.
- Bounce/elastic easing on anything professional.
- Animated gradient backgrounds behind text.
- Auto-playing carousels that advance faster than ~6s (or at all, without pause controls).
- Anything triggered on `mousemove` without `requestAnimationFrame` throttling.
- Entrance animations on above-the-fold hero copy that delay the headline — the H1 should be
  visible at paint. Animate the hero image or subhead instead, or use a ≤150ms delay.

---

## 9. Fixed + Slide-Over Hero (No Dead Scroll Space)

The common approach to a hero that shrinks/anchors as the page scrolls is `position: sticky`
inside a container inflated to `height: 200vh`. **Don't do this.** It produces 100vh of dead,
content-free scrolling before the sticky element releases — the visitor scrolls and nothing
visibly happens, which reads as broken.

The correct recipe has no artificial height at all:

```html
<!-- Hero is fixed to the viewport, not sticky, and not interactive by default. -->
<section class="fixed inset-0 z-0 h-screen w-full pointer-events-none">
  <!-- background image, scrim, and Ken Burns / parallax effects. Re-enable
       pointer-events-auto on any actual controls (a CTA button) inside it. -->
</section>

<!-- Everything else starts exactly one viewport down and slides up over the fixed
     hero as the page scrolls — no spacer, no dead zone. -->
<main class="relative z-20 mt-[100vh]">
  …
</main>
```

The hero never needs to be `position: sticky` at all — it's simply pinned to the viewport
(`fixed inset-0`), and the content that follows starts at exactly `100vh` down and then behaves
like completely normal document flow. The "shrinking headline on scroll" effect, if wanted,
is layered separately: track `useScrollY` (`07-react-tailwind-snippets.md` §2) and swap size/
position classes on the headline itself, inside the fixed hero — that's a style change driven
by scroll position, not a layout technique, and the two should never be conflated.

The content itself sits in a **floating card** over the photograph, not directly on it, and
that card's position and text alignment are a separate mobile/desktop decision from the scroll
mechanic above — mobile anchors the card in the upper third and centers its text; desktop
centers the card vertically and left-aligns it. See `06-ui-ux-accessibility.md` §8 for why the
two breakpoints diverge here specifically.

### The Card Eases Out on the Way Down, Back In on the Way Up

Because the hero is genuinely pinned (`position: fixed`, not scrolling away like an in-flow
section), its content can do something an in-flow section can't: **fade and settle
continuously with scroll position, in both directions**, since the card never actually leaves
the viewport — it just recedes behind whatever is sliding over it. This is the "ease in up and
ease in down" feel: scroll down and the card eases out (opacity down, a slight scale-down);
scroll back toward the top and it eases back in, because it's a live function of `scrollY`, not
a one-time triggered reveal.

```jsx
const opacity = Math.max(0, 1 - scrollY / 600);   // fully faded by 600px of scroll
const scale   = Math.max(0.9, 1 - scrollY / 2000); // settles at 0.9, never smaller
```

Apply both to the **card wrapper as a whole** — one continuous transform on the outer
positioning element, not a separate animation per line or per button. The badge, headline,
subhead, and buttons ease out together as one composed unit; animating them individually here
would compete with the line-mask heading treatment in §11, which is for in-flow section
headings, not this pinned hero.

This is a **derived value recomputed on render**, not new state and not a second scroll
listener — it reads the same `scrollY` already tracked by `useScrollY`
(`07-react-tailwind-snippets.md` §2), so it stays exactly as cheap as the parallax background
already sitting on that value.

Divide by zero risk aside, the two divisors above (`600`, `2000`) are tuning knobs, not magic
constants — a taller hero or a slower-paced site can widen both; the shape (linear, clamped)
is what matters more than the exact numbers. Under `prefers-reduced-motion`, skip the
computation entirely and render `opacity: 1, scale: 1` unconditionally — a fade tied to scroll
position is exactly the kind of motion that rule exists to suppress (§7).

See `PinnedHero` in `07-react-tailwind-snippets.md` §6 for the full implementation — the fixed
positioning, the floating card, the mobile/desktop divergence, and this scroll-linked fade,
composed together. `ParallaxHero` (also §6) is the simpler alternative for a hero that's
allowed to scroll away normally; the two are different techniques, not two names for the same
thing, and a page uses one or the other.

## 10. Seamless Marquee — Why the Width Must Be Content-Sized

The seamless-loop marquee (Proof band, logo walls, menu tickers — see `Marquee` in
`07-react-tailwind-snippets.md` §8) works by duplicating the item list once and animating the
combined track by exactly `-50%`. That only lands precisely on the seam between the two copies
if the track's rendered width is **exactly** twice the width of one copy — which is only true
when the track is sized to its own content (`w-max`), never to a fixed or fractional width like
`w-[200%]`. A percentage width is relative to the *parent*, not to the duplicated content, so it
drifts out of sync with the actual item widths and the loop visibly stutters or leaves a gap at
the seam. If a marquee stutters, this is the first thing to check.

---

## 11. Section Headings — Line-Mask Reveal

Section headings deserve a better entrance than the generic fade-and-rise of §3. The
contemporary treatment is a **line mask**: each line of the heading sits inside its own
`overflow-hidden` wrapper and slides up from behind that clipped edge, lines staggered. The
text appears to be *uncovered* rather than faded in — the single most recognizable "this was
designed" motion detail on the current web, and it costs one wrapper element per line.

```
┌─────────────────────┐  ← overflow-hidden mask (fixed, doesn't move)
│  Quiet the noise    │  ← inner span: translateY(100% → 0)
└─────────────────────┘
```

**Rules:**

- **Travel exactly one line-height** (`100%` of the inner span), not a fixed pixel value — the
  line must start fully hidden behind the mask regardless of font size.
- **Slower than a body reveal:** 800–1000ms with `out-expo`. A heading is the thing the eye
  lands on; let it settle.
- **Stagger lines by 80–120ms.** Two or three lines is the sweet spot. Past four the last line
  arrives after the reader has already started reading the first.
- **Mask the lines, not the letters.** Per-character (or per-word) reveals read as a gimmick on
  a marketing page and wreck screen-reader output and text selection. Line granularity keeps
  the heading one intact text node per line.
- **Pad for descenders.** An `overflow-hidden` box clips the tails of `g`, `y`, `p`, `j` and the
  overhang of italics. Add a hair of bottom padding to the mask and pull it back with an equal
  negative margin so the clip sits below the descender line without changing layout:
  `pb-[0.12em] -mb-[0.12em]`. Omitting this is the number-one bug in line-mask reveals, and it
  only shows up on headings that happen to contain a descender — so it survives casual review.
- **Author the line breaks.** `03-typography.md` §3 already treats a headline's break points as
  a compositional decision rather than an accident of container width; those same authored lines
  become the animation units. Pass the heading as an array of lines, don't try to detect wraps.

### Direction-Aware Entry

Lines rise up when the reader is scrolling **down**, and settle down when the reader is
approaching the heading from **above** (scrolling back up, or arriving below a hash-link jump
and scrolling upward). Motion that always travels the same way regardless of approach fights
the reader on the way back up; matching the direction makes the page feel like one continuous
surface rather than a list of independently animating blocks.

**This does not contradict "reveal once" in §3 — and must not be implemented as if it did.**
The direction decides *which side a heading enters from on its single, permanent reveal*. It
never re-hides content that has already been revealed. A heading that animates out when it
leaves the viewport, or re-animates on every pass, is the amateur tell §3 names; a heading that
enters from whichever side the reader came from is direction-aware. The difference is entirely
in whether anything is ever hidden *again*.

Implementation notes that matter:

- Read the approach direction from the `IntersectionObserver` entry's own
  `boundingClientRect`, which arrives free with the callback — never from a scroll listener
  calling `getBoundingClientRect()` per element per frame, which thrashes layout.
- Keep `transition: none` while the heading is still hidden, so re-positioning it from the
  "below" start to the "above" start is instantaneous and invisible rather than an animated
  slide of an already-hidden element.
- Under `prefers-reduced-motion`, render the final state immediately — no travel, no stagger,
  no transition (§7).

See `useDirectionalReveal` and `<LineReveal>` in `07-react-tailwind-snippets.md` §24.
