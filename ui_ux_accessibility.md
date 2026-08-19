# UI/UX & Accessibility Guidelines

Ensure all generated designs strictly adhere to these contemporary UI/UX and accessibility standards.

## 1. The 8-Point Spacing Grid
All padding, margin, sizing, and gaps MUST be multiples of 8px (which aligns perfectly with Tailwind's default spacing scale where 1 unit = 4px).
* **Micro-spacing:** `gap-2` (8px), `gap-4` (16px) for items inside a card.
* **Macro-spacing:** `py-16` (64px), `py-24` (96px) for spacing between major page sections.
* **Rule:** Never use random values like `py-11` or `mt-7`. Stick to the grid to maintain visual rhythm.

## 2. Touch Targets & Mobile First
* **Minimum Button Size:** Any clickable element (buttons, links, form inputs) MUST have a minimum height and width of 44px-48px. 
* **Implementation:** Always use at least `py-3 px-6` for buttons, or `min-h-[48px]`.
* **Stacking:** On mobile (`max-width: 768px`), flex/grid layouts MUST stack vertically (`flex-col` or `grid-cols-1`).

## 3. Contrast & Accessibility
* **WCAG Minimums:** Text must have a 4.5:1 contrast ratio against its background. 
* **Text over Images:** If placing white text over a background image, you MUST include a dark overlay. Example: `<div className="absolute inset-0 bg-black/70 mix-blend-multiply"></div>`. Never rely on the image itself to provide contrast.
* **Muted Text:** When using muted text (e.g., `text-gray-500`), ensure the background is pure white or very light gray. Do not use `text-gray-400` on a `bg-gray-100` background.

## 4. Visual Hierarchy & Glassmorphism
* **Depth:** Use varying degrees of blur and opacity to create depth, rather than relying solely on borders.
* **Sticky Elements:** When using a sticky navigation bar (`sticky top-0` or `fixed top-0`), apply a glass effect to ensure content scrolling underneath is legible but obscured: `bg-white/80 backdrop-blur-md border-b border-gray-200`.
* **Rounding:** Use consistent border-radius. Contemporary design heavily favors `rounded-2xl` or `rounded-3xl` for cards, and `rounded-full` for primary action buttons.