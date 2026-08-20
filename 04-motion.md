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

- Elements that animate *out* when scrolled past.
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
is layered separately: track `useScrollY` (§`07-react-tailwind-snippets.md`) and swap size/
position classes on the headline itself, inside the fixed hero — that's a style change driven
by scroll position, not a layout technique, and the two should never be conflated.

## 10. Seamless Marquee — Why the Width Must Be Content-Sized

The seamless-loop marquee (Proof band, logo walls, menu tickers — see `Marquee` in
`07-react-tailwind-snippets.md` §8) works by duplicating the item list once and animating the
combined track by exactly `-50%`. That only lands precisely on the seam between the two copies
if the track's rendered width is **exactly** twice the width of one copy — which is only true
when the track is sized to its own content (`w-max`), never to a fixed or fractional width like
`w-[200%]`. A percentage width is relative to the *parent*, not to the duplicated content, so it
drifts out of sync with the actual item widths and the loop visibly stutters or leaves a gap at
the seam. If a marquee stutters, this is the first thing to check.
