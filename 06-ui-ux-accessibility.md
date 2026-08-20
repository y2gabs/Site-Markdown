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
- [ ] Every section and modal the build committed to (Phase 1–4) is present exactly once —
      re-count against the plan, since sections and modals are what silently go missing in a
      large build
- [ ] Every dialog closes on Escape, closes on outside click, closes on its × button, and locks
      body scroll while open — and all of them share one visual language (§9)
- [ ] Mobile reviewed as its own layout per §8, not just the desktop layout reflowed narrower

---

## 8. Mobile Is a Separate Design, Not a Reflow

Tailwind's default behavior — stack the grid, shrink the type — is correct for most sections.
But for a handful of section types, the *right* mobile experience has a genuinely different
structure from desktop, not just a narrower version of the same one. Build these as two
explicit layouts with `hidden md:flex` / `flex md:hidden` (or `md:block` / `md:hidden`) rather
than trying to force one layout to reflow into both:

| Section | Desktop | Mobile |
|---|---|---|
| Navigation | Full inline link row | Collapsed hamburger menu |
| Hero content panel | Card at `w-[55%]`, vertically centered with a slight upward lift (`-translate-y-8`), text and buttons **left-aligned** | Card widens to `w-full`, anchored in the upper third (`items-start pt-[20vh]`), text and buttons **centered** — see `ParallaxHero` in `07-react-tailwind-snippets.md` §6 |
| About | Two columns, image alongside text, text left-aligned | Single column, text **centered**, **image hidden entirely** (`hidden md:block` on the image) — a stacked image-then-text block reads as worse than no image. `text-center` only centers text and inline content — a stat block or social-icon row inside the same column is a `flex`/`grid` container and needs its own `justify-center` (and `mx-auto` if it has a fixed width), or it stays left-aligned inside an otherwise-centered column |
| Logo wall / "Trusted by" | Static row | Continuous marquee (see `04-motion.md` §10) |
| Feature/service grid | Multi-column card grid | Single-column `CompactTile` list — small icon media (`gap-3`), a compact action button. See `07-react-tailwind-snippets.md` §22 |
| Team grid | Centered photo cards | `CompactTile` list like the above, but with a **larger** photo and **more** gap (`gap-4`) than a service tile gets — the whole tile is the click target, opening the profile modal |
| Testimonials / news cards | Multi-column grid | One-at-a-time auto-advancing slider (see `MobileSlider` in `07-react-tailwind-snippets.md` §19) |
| Contact details | Rows of icon + label + value inside one card — icon `w-6 h-6` in a `p-3` box, value `text-2xl font-bold`, label `text-xs uppercase` | Either (a) a row of icon-only circular buttons (`grid-cols-3`) when contact is a compact footer-style block, or (b) separate stacked cards, one per link (`flex flex-col gap-4`, each `p-5`) when it's a full Contact section — icon drops to `w-5 h-5`, value to `text-lg`/`text-base`, label to `text-[10px] uppercase`. Either way, `aria-label` carries the full text even where it's visually reduced or hidden |
| Photo gallery | Fixed `grid-cols-3` grid | **Also fixed `grid-cols-3`** — never drop to fewer columns to "fit" mobile. Tighten the gap instead (`gap-1.5` vs `gap-4`); the grid's own fluid sizing shrinks each tile to fit. See `07-react-tailwind-snippets.md` §18 |
| Hero button row | Full, value-driven labels (`flex flex-row gap-3`) | Same row, same layout — but each button swaps to a **one-word** label (`sm:hidden` short label / `hidden sm:inline` full label) so two buttons never wrap inside a narrow flex row. See the Hero row above and `05-copywriting-cro.md` §4 |
| Social profile links | Icon-only row, never a text link list | Same — icon-only at every breakpoint. See `SocialLinks` in `07-react-tailwind-snippets.md` §23 |
| Floating chat/support widget | Fixed-width panel anchored to a corner (`right-6`) | **Centered, viewport-clamped overlay** (`inset-x-4`, no separate width) — an edge-anchored fixed-width panel overflows off the opposite edge the moment its width exceeds the room between it and that edge. See `ChatWidget` in `07-react-tailwind-snippets.md` §25 |
| Nav CTA button | Sits inline with the link row | **Do not also place it next to the hamburger icon.** A "Book Now" button crowded beside the menu toggle duplicates a CTA the hero (or the drawer, if it has one) already carries, and cramps the two touch targets together. The hamburger opens to reveal navigation; it doesn't need a second CTA riding along next to it |

The reasoning is the same in every row: some content (a full nav, a two-column layout, a value
label) earns its place on a wide viewport but becomes clutter on a narrow one, and the fix is
not a smaller version of the same thing — it's a different, deliberately designed alternative.
Treat mobile as a second design pass in Phase 5 of `00-BUILD-PROTOCOL.md`, not a checkbox.

**Default block copy to centered text on mobile**, even where desktop is left-aligned — every
row above that has running copy (Hero, About) centers it on mobile, and every section heading
in this framework's reference build (`templates/starter.html`) is centered at every breakpoint.
Centered short blocks of text read as intentional on a narrow column in a way left-aligned text
usually doesn't. The exception is a **data list** — contact rows, FAQ items, a `CompactTile`'s
title/meta — where left alignment stays more scannable and should be kept even on mobile.

## 9. Modal Dialog Standard

Every dialog on a page — a booking flow, a team-member profile, an article reader, an image
lightbox — should be built on one shared shell (`Modal` in `07-react-tailwind-snippets.md`
§16) so a visitor never has to relearn how to close a popup partway through a site. A page with
four different modal implementations, each closing a different way, is a worse experience than
four modals that all look and behave identically.

Non-negotiable on every modal, no exceptions:

* **Escape** closes it.
* **Clicking outside the panel** (on the overlay) closes it. Clicking inside the panel never
  bubbles up and closes it accidentally.
* An explicit **× button** closes it, placed in the same corner every time.
* **Body scroll is locked** while it's open (`useBodyScrollLock`) and restored on close.
* **Consistent visual language:** the same overlay opacity/blur, the same corner radius, the
  same open/close transition timing across every modal on the site.
* A multi-step flow (a booking wizard) shows a **step-progress indicator** and a **"← Back"**
  control on every step after the first, and supports being **opened mid-flow** with a value
  already selected — e.g., a "Book" link on a specific service card should land the visitor
  past the service-selection step, not force them to re-pick what they already clicked on.
* A full-bleed image viewer (a lightbox) follows the same closing rules even though its shell
  looks different from a centered card — see `Lightbox` in `07-react-tailwind-snippets.md` §17.
