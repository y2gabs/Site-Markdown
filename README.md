# Site-Markdown

A design framework for generating contemporary, high-converting websites. Feed these files to
an AI coding agent as context; it produces sites that look designed rather than generated.

## Usage

Load the files as context and state the brief:

> Using the Site-Markdown framework, build a landing page for a boutique physiotherapy clinic
> in Halifax. Follow `00-BUILD-PROTOCOL.md`.

The agent should follow the protocol's phases in order and start from `templates/starter.html`.

## Files

| File | Purpose |
|---|---|
| `00-BUILD-PROTOCOL.md` | **Start here.** Phased operating procedure and hard rules |
| `01-design-system.md` | Layout archetypes, imagery/icon direction, industry directions (scoped to health & wellness for now) |
| `02-color-system.md` | OKLCH palette construction, allocation, per-industry hues |
| `03-typography.md` | Font pairings, fluid scale, setting rules |
| `04-motion.md` | Easing, duration, scroll reveal, reduced motion |
| `05-copywriting-cro.md` | Section order, copy formulas, microcopy, voice |
| `06-ui-ux-accessibility.md` | 8pt grid, touch targets, contrast, semantics |
| `07-react-tailwind-snippets.md` | Tailwind config + tested React hooks and components |
| `08-anti-patterns.md` | **Read last.** Generated-site tells and the final audit |
| `templates/starter.html` | Working single-file reference implementation |

Numbered for reading order. `00` and `08` bracket the process: one sets the system, the other
audits it.

## Design Philosophy

Six ideas do most of the work:

1. **Commit to a system before writing markup.** One accent hue, two type families, one radius
   language — decided up front. Improvised styling is what makes generated sites look generic.
2. **Neutrals are never gray.** Every neutral carries a trace of the accent's hue. This single
   detail separates designed pages from defaulted ones.
3. **Compose from named layout patterns, don't improvise structure.** The Layout Archetype
   library in `01-design-system.md` (bento grids, masonry, before/after sliders, timelines…)
   means every section has a reason to look the way it does, and no two sections repeat the
   same shape.
4. **Photography is art-directed, not sourced.** One consistent color grade and aspect-ratio
   language across every image on the page — see Imagery & Art Direction in `01`.
5. **Asymmetry and varied rhythm.** Centered-everything with uniform section padding is the
   clearest tell of machine-generated design.
6. **The last pass is subtraction.** Nearly every page improves by removing a decorative
   element and adding whitespace.

## Reference Implementation

`templates/starter.html` is a complete single-file React + Tailwind page demonstrating the
full system: OKLCH palette bridge, fluid type scale, parallax hero with scrim, scroll reveals
with stagger, seamless marquee, count-up stats on a dark punctuation band, accordion, and form.

Verified in Chromium: single `<h1>`, correct heading order, all images with `alt`, all buttons
labelled, all inputs associated with `<label>`, working accordion and form state, no React
errors. The layout also holds when images fail to load.

> **Note:** the template uses the Tailwind Play CDN and in-browser Babel, which are development
> tools — correct for a single-file deliverable, but compile both for production. Its
> `picsum.photos` images are placeholders to be replaced with real photography.

## Extending

- **New industry:** add an entry to `01-design-system.md` with all six fields (Vibe, Palette,
  Type, Radius, Signature, Motion) and a hue row in `02-color-system.md` §6.
- **New component:** add to `07-react-tailwind-snippets.md` using the existing tokens
  (`ease-out-expo`, `shadow-lift`, the color names) so it composes with everything else.
- **Brand constraints:** override the `:root` triplets in `templates/starter.html` and the
  `fontFamily` entries. Everything downstream inherits.
