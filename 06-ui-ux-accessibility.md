# UI/UX & Accessibility Guidelines

Non-negotiable standards. Beauty that fails these is not finished work.

---

## 1. The 8-Point Spacing Grid

All padding, margin, sizing, and gaps MUST be multiples of 8px (Tailwind's scale where
1 unit = 4px, so even-numbered utilities land on the grid).

* **Micro-spacing:** `gap-2` (8px), `gap-4` (16px) inside a card.
* **Component spacing:** `gap-6` (24px), `gap-8` (32px) between cards.
* **Macro-spacing:** `py-16` (64px), `py-24` (96px), `py-32` (128px) between major sections.
* **Rule:** Never use arbitrary values like `py-11` or `mt-7`.

**4px is permitted only for optical corrections** — nudging an icon into alignment with a text
baseline, or a 1px hairline offset. Optical alignment beats mathematical alignment when they
disagree; the grid is a default, not a straitjacket.

---

## 2. Touch Targets & Mobile First

* **Minimum interactive size: 44×44px**, target 48px. Applies to buttons, links, inputs, and
  icon buttons.
* **Implementation:** at least `py-3 px-6`, or `min-h-[48px]`. Icon-only buttons:
  `h-11 w-11 grid place-items-center`.
* **Spacing between targets:** at least 8px so adjacent taps don't misfire.
* **Stacking:** below 768px, flex/grid layouts stack vertically (`flex-col`, `grid-cols-1`).
* **Design at 375px first.** Not "does it break" — is it still *good*? Display type must scale
  down (`clamp()` handles this), section padding must reduce (`py-16 md:py-24 lg:py-32`), and
  parallax must be disabled.
* Horizontal scroll at any width is a bug. Watch for wide grids, long unbroken strings, and
  absolutely-positioned decorative elements without `overflow-hidden` on the parent.

---

## 3. Contrast & Accessibility

* **WCAG AA minimums:** body text **4.5:1**; large text (≥24px bold / ≥30px regular) **3:1**;
  UI component boundaries and icons **3:1**.
* **Muted text is still body text** — it needs 4.5:1, not 3:1. `text-gray-400` on
  `bg-gray-100` fails badly and is a common tell.
* **Text over images:** a scrim is mandatory. Never rely on the photograph for contrast — the
  image may crop or fail to load.
  ```html
  <div class="absolute inset-0 bg-gradient-to-t from-ink/80 via-ink/50 to-ink/30"></div>
  ```
  A directional gradient scrim looks better than a flat overlay because it preserves more of
  the image where text isn't sitting.
* **Never signal state by color alone.** Pair with an icon, a label, or a weight change.
* **Accent-on-ground check:** a mid-lightness accent (L 55–70%) often fails with *both* black
  and white labels. Test both and adjust L until one passes.

---

## 4. Semantics & Keyboard

* Use real elements: `<button>` for actions, `<a href>` for navigation, `<nav>`, `<main>`,
  `<section>`, `<footer>`. Never a clickable `<div>`.
* **One `<h1>` per page.** Heading levels descend without skipping — style controls size, not
  heading level.
* Every image has meaningful `alt`; decorative images get `alt=""`.
* Icon-only buttons need `aria-label`. Toggles need `aria-expanded`.
* **Never `outline-none` without a replacement.** Style focus instead:
  `focus-visible:ring-2 focus-visible:ring-accent focus-visible:ring-offset-2`.
* Provide a skip link as the first focusable element:
  ```html
  <a href="#main" class="sr-only focus:not-sr-only focus:absolute focus:top-4 focus:left-4
                         focus:z-50 focus:rounded-full focus:bg-surface focus:px-6 focus:py-3">
    Skip to content
  </a>
  ```
* Anchor targets need `scroll-mt-24` so a sticky header doesn't cover them.
* Modals and mobile menus: trap focus, close on `Escape`, restore focus on close, and lock body
  scroll while open.
* Respect `prefers-reduced-motion` (see `04-motion.md` §7).

---

## 5. Visual Hierarchy & Depth

* **Depth via blur and opacity**, not just borders. Layer: ground → surface → raised surface.
* **Sticky navigation** gets a glass treatment so content scrolling underneath stays legible:
  `bg-ground/80 backdrop-blur-md border-b border-line`. Always include a solid-color fallback —
  `backdrop-blur` fails on some browsers and unblurred transparency destroys legibility.
* **Rounding:** pick one radius language and apply it consistently (see `01-design-system.md`).
  Nested elements use a *smaller* radius than their container, never larger.
* **One dominant element per viewport.** If three things compete for attention, two must recede.
* **Z-index discipline:** use a small documented set (`z-10` content, `z-40` overlays,
  `z-50` nav/modals). Arbitrary large values are a debugging trap.

---

## 6. Performance & Robustness

* `loading="lazy"` on below-the-fold images; `loading="eager"` + `fetchpriority="high"` on the
  hero image.
* Always set `width`/`height` or an `aspect-[…]` container to prevent layout shift.
* Preconnect to font hosts; use `display=swap`.
* Animate only `transform` and `opacity`. Animating `width`, `height`, `top`, or `box-shadow`
  triggers layout or paint on every frame.
* `will-change: transform` only on elements actually animating, and remove it when idle.
* Test with images failing to load — the layout should hold and text should stay legible.

---

## 7. Pre-Ship Checklist

- [ ] Keyboard-only pass: every interactive element reachable, focus always visible
- [ ] 375px, 768px, 1440px, and 1920px all hold up
- [ ] No horizontal scroll at any width
- [ ] Contrast verified on body, muted text, and text-over-image
- [ ] `prefers-reduced-motion` honored
- [ ] All images have `alt`; icon buttons have `aria-label`
- [ ] Single `<h1>`; heading order descends
- [ ] Zero Lorem Ipsum; zero placeholder gray boxes
- [ ] Audited against `08-anti-patterns.md`
