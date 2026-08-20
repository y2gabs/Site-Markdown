# Color Systems

The old version of this framework was deliberately "color-neutral." That was the single
biggest cause of generic output: with no guidance, generation defaults to Tailwind's stock
`blue-500`/`gray-900` or a purple→indigo gradient. This file removes that failure mode.

---

## 1. Build Palettes in OKLCH, Not Hex

OKLCH is perceptually uniform: equal lightness numbers *look* equally light across hues.
This is why OKLCH ramps look expensive and HSL ramps look muddy.

Syntax: `oklch(L C H)` where **L** = 0–100% lightness, **C** = 0–0.37 chroma (saturation),
**H** = 0–360 hue angle.

**Define the palette as raw channel triplets in `:root`,** so opacity modifiers compose:

```css
:root {
  --ground:  98%  0.006  85;   /* page background */
  --surface: 100% 0      0;    /* cards, raised panels */
  --ink:     22%  0.014  75;   /* primary text — never pure black */
  --muted:   52%  0.012  75;   /* secondary text */
  --line:    90%  0.008  75;   /* hairline borders */
  --accent:  56%  0.132  38;   /* the ONE accent */
  --accent-ink: 98% 0.01 38;   /* text that sits on accent */
}
```

Consumed in the Tailwind CDN config via an alpha-aware function (see
`templates/starter.html`), which makes `bg-accent/10` and `border-line/60` work correctly.

### The Neutral Rule
**Never use pure `#000` or a pure-gray neutral ramp.** Push a tiny amount of chroma
(`0.004`–`0.016`) into every neutral, sharing the hue of your accent. This is the most
reliable single trick for making a page look designed rather than defaulted. Warm accent →
warm neutrals. Cool accent → cool neutrals.

### The Chroma Ceiling
Large areas need low chroma; small areas can take high chroma.

| Surface area | Max chroma |
|---|---|
| Page background | `< 0.02` |
| Section blocks, large cards | `< 0.06` |
| Buttons, badges, small fills | `< 0.16` |
| Text accents, icons, 1px rules | `< 0.22` |

Violating this is what makes a page feel loud and cheap.

---

## 2. The 60 / 30 / 10 Allocation

- **60%** — ground (the dominant neutral)
- **30%** — surface + ink (structure and text)
- **10%** — accent

The accent should feel *scarce*. If more than roughly one element per viewport is accent-
colored, the accent has stopped meaning anything. Accent belongs on: the primary CTA, an
active nav state, a small overline rule, one number in a stat block. Not on every heading.

---

## 3. Single-Accent Discipline

**Default to exactly one accent hue.** A second hue must earn its place — usually only as a
semantic signal (success/error) or in an intentionally editorial two-tone system.

Where a palette needs more range, move along **L and C at a fixed H**, not to a new hue:

```
--accent-950: 22% 0.06 38    /* deep — dark section grounds */
--accent-600: 56% 0.13 38    /* base — buttons */
--accent-200: 88% 0.05 38    /* wash — tinted card backgrounds */
--accent-050: 97% 0.015 38   /* whisper — hover fills, alternating bands */
```

---

## 4. Gradients

- Blend **analogous** hues only: keep hues within ~40° of each other.
- Vary **lightness more than hue** — a gradient from `oklch(58% .14 38)` to
  `oklch(44% .11 22)` reads as rich light falling across a surface. A gradient from purple to
  cyan reads as a 2015 startup template.
- Prefer gradients as **atmosphere** (a soft radial bloom behind a hero) over gradients as
  **fills** (a gradient button, a gradient headline). Gradient text is almost always a
  downgrade from a well-set solid headline.
- **Mesh/bloom recipe:** two or three large, heavily blurred radial gradients at low opacity,
  absolutely positioned, `pointer-events-none`, behind content.

```html
<div class="pointer-events-none absolute inset-0 overflow-hidden">
  <div class="absolute -top-40 left-1/4 h-[36rem] w-[36rem] rounded-full
              bg-accent/20 blur-[120px]"></div>
  <div class="absolute -bottom-52 right-1/4 h-[30rem] w-[30rem] rounded-full
              bg-accent/10 blur-[140px]"></div>
</div>
```

---

## 5. Dark Sections as Punctuation

A full-dark site is a deliberate choice for a few industries (automotive, tech, nightlife).
For everything else, the more sophisticated move is a **predominantly light page with one or
two dark sections** used as rhythm — typically the testimonial band, the stats band, or the
final CTA. The contrast shift does more for perceived quality than any single effect.

In dark blocks: raise accent lightness ~8–12% so it stays vivid against the dark ground, drop
borders to low-opacity white (`border-white/10`), and replace shadows with faint luminous
glows.

---

## 6. Industry Palette Starting Points

Hue angles to start from, then tune. Neutrals should borrow the accent's hue.

Scoped to health & wellness for now (see `01-design-system.md`) — the full 44-industry table
lives in that commit's history; restore via `git revert` rather than re-deriving it.

| Industry | Accent H | Character |
|---|---|---|
| Beauty / Personal Care | 30–50 | Clay, bone, burnished copper |
| Healthcare | 180–200 | Calm teal, soft sky |
| Dental / Orthodontics | 170–200 | Fresh mint or sky, high-key bright |
| Senior Living / Elder Care | 30–50 or 150–170 | Warm neutral, soft confident accent |
| Sports / Fitness | 15–35 or 90 | High-energy orange or volt green |
| Yoga / Studio Fitness | 100–140 or 20–40 | Soft sage or clay, low chroma |

---

## 7. Verify Before Shipping

- Body text vs. its background: **≥ 4.5:1**. Muted text: **≥ 4.5:1** (not 3:1 — muted text is
  still body text).
- Large display text (≥ 24px bold / 30px regular): ≥ 3:1.
- Accent-on-ground and ink-on-accent both checked — a mid-lightness accent often fails with
  white button text. If `L` is between 55% and 70%, test both black and white labels.
- Never signal state by hue alone: pair color with an icon, weight, or label.
