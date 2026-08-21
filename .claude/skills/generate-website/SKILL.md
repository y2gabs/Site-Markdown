---
name: generate-website
description: Generate a complete, single-file index.html website using the Site-Markdown design framework (00-BUILD-PROTOCOL.md through 08-anti-patterns.md, templates/starter.html) plus the Gemini Gem persona in references/gemini-gem-instructions.md. Use this whenever the user wants a landing page, marketing site, or business website built from this repo — phrases like "build a site for...", "generate a website for [business]", "make me a landing page", or any brief describing an industry, brand, or business that should become a real page. Also use it when the user pastes or describes a brief and says to follow the Site-Markdown framework or the build protocol. Produces one self-contained HTML file, not a scaffolded project.
---

# Generate Website

This repo is a design framework, not application code: its numbered files (`00`–`08`) are a
phased operating procedure, and `references/gemini-gem-instructions.md` is the persona/technical
contract the framework was originally written for (a "Gemini Gem" system prompt). This skill's
job is to combine both with a user's brief and produce one real, finished `index.html`.

Do not skip straight to writing markup. The framework's central failure mode — and the reason
it exists — is an agent inventing colors, fonts, and section structure ad hoc instead of
committing to a system first. Follow the phases below in order, the same way the protocol
insists on.

## Step 1 — Load the operating procedure

Read, in this order:

1. `00-BUILD-PROTOCOL.md` — the phased procedure (Phase 0 Intake through Phase 7 Completeness
   Gate + Subtraction Pass) and the Hard Rules list at the end. This is the primary spec for
   *what to do and in what order*.
2. `references/gemini-gem-instructions.md` — the persona and technical contract for *how to
   write the code*: single-file React 18 + Tailwind (CDN, Babel Standalone) architecture, the
   14 mandatory sections and 4 mandatory modals in exact order, and the Babel/execution
   guardrails (no fragment shorthand, no icon-lookup objects, inline SVG only, no literal
   arrow/×/✓ glyphs in JSX, modal backdrop-click handler placement, image `onError` fallback).

These two files together are the spec. Everything below just tells you when to pull in the
rest of the framework.

## Step 2 — Get the brief

The user provides a prompt describing the site: industry, brand name, real copy points if they
have them, conversion goal, page count, brand colors/fonts if constrained. If any of Phase 0's
intake fields are missing, don't stop to ask — decide a sensible default per the Phase 0 table
in `00-BUILD-PROTOCOL.md` (e.g. invent plausible industry-specific copy and numbers rather than
using placeholders) and state the assumption briefly before building.

## Step 3 — Work the phases, pulling in reference files as each phase needs them

Follow `00-BUILD-PROTOCOL.md`'s own Reading Order table — don't front-load every numbered file
before starting; pull each in exactly when the phase calls for it:

| Phase | Pull in |
|---|---|
| 1 (layout archetypes + signature move) | `01-design-system.md` — find the brief's industry entry, or pick archetypes matching its Vibe if no entry fits |
| 2 (commit the system in writing) | `02-color-system.md` (OKLCH palette construction), `03-typography.md` (pairing + fluid scale) |
| 3 (copy first) | `05-copywriting-cro.md` (section order, copy formulas, voice) |
| 4 (build) | `04-motion.md` (easing/reveal vocabulary), `07-react-tailwind-snippets.md` (Tailwind config, hooks, components), `templates/starter.html` as the literal starting point — copy it, don't reimplement its primitives from memory |
| 5–6 (responsive + accessibility passes) | `06-ui-ux-accessibility.md` |
| 7 (completeness gate + final audit) | `08-anti-patterns.md`, read end to end, only at this final step |

Write out Phase 2's system block explicitly (Industry, Signature, Palette, Type, Radius,
Shadow, Motion, Section order) before touching markup — this becomes the `:root` block and
Tailwind config, mirroring `templates/starter.html`'s structure.

## Step 4 — Build the file

Start from `templates/starter.html` as the base (copy it, then modify) rather than writing a
new document from scratch — it already contains the OKLCH alpha bridge, fluid type scale,
hooks (`useScrollY`, `usePrefersReducedMotion`, `useInView`, `useDirectionalReveal`), primitives
(`Reveal`, `LineReveal`, `Button`, `Modal`, `DetailModal`, `CompactTile`), and a full section set
that already satisfies most of the Gemini gem's guardrails. Swap in the new palette, type,
copy, and section content; add or remove sections per the brief while keeping the 14-section /
4-modal structure from `references/gemini-gem-instructions.md` unless the user explicitly asks
for a shorter page.

Save the result as `index.html` (or the path the user names) via the Write tool. This is a
generated deliverable, not a chat transcript — the Hard Rule about "nothing outside the code
fence" in `00-BUILD-PROTOCOL.md` exists because canvas/export tools capture trailing chat prose
verbatim into the file; writing the file directly with the Write tool sidesteps that failure
mode entirely, but keep the file itself free of anything but the HTML document for the same
reason (no trailing comments about what you built, no note-to-self).

## Step 5 — Phase 7: completeness gate, then the anti-patterns audit

Before declaring the build done:

1. List every section and modal the Phase 1–2 commitment promised, then check the actual file
   against that list one item at a time — it's easy for one section (commonly FAQ, News, or the
   Location Map, since they land late and nothing else visibly breaks when they're missing) to
   get dropped silently.
2. Check for two sections quietly duplicating the same content (e.g. a "Visit Us" block
   restating an address already covered by Contact + Location Map).
3. Read `08-anti-patterns.md` end to end and audit the build against it.
4. Do a subtraction pass: remove a decorative element, cut anything that repeats another
   section's function, increase whitespace. Nearly every generated page improves by removing
   something at this step, not adding.

## Step 6 — Tell the user what you built

After writing the file, give a short summary: industry/brief assumptions made (if any), the
Phase 2 system block (palette/type/radius/signature), and where the file was saved. Don't
paste the HTML itself into the chat — the file is the deliverable.

## Notes

- This produces a **single landing page** by default (per Phase 0's default page count). If the
  user asks for multiple pages, treat each as its own pass through Steps 3–5, sharing the Phase
  2 system across all of them.
- The Tailwind Play CDN and in-browser Babel used in `templates/starter.html` are development
  conveniences appropriate for a single-file deliverable — mention to the user that a
  production deployment should compile both, per the README's note.
- `picsum.photos` / Unsplash images are placeholders. Say so explicitly rather than presenting
  them as final photography.
