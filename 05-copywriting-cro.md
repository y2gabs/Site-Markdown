# Conversion Rate Optimization (CRO) & Copywriting

When generating text for landing pages, **NEVER use "Lorem Ipsum."** Always write realistic,
industry-specific copy using the frameworks below. Placeholder copy is the fastest way to make
a beautiful layout look unfinished — and it makes the design impossible to evaluate, because
real copy has real lengths.

---

## 1. Standard Section Order — Full Local-Business Build

For the health & wellness build this framework is currently scoped to (`01-design-system.md`)
— a local business site with booking, staff, a gallery, and ongoing content — this is **the**
section order, not a suggestion to vary from. It has exactly 14 sections, numbered 0–13, and
none of them are optional or interchangeable: **Section 9 (News), Section 11 (FAQ), and
Section 12 (Location Map) are required**, not nice-to-haves, even though they land late in the list
where they're easiest to quietly drop. See the Phase 7 completeness gate in
`00-BUILD-PROTOCOL.md` for how to verify none of the 14 went missing.

| # | Section | Where the detail lives |
|---|---|---|
| 0 | Navigation | `07-react-tailwind-snippets.md` §5, `06-ui-ux-accessibility.md` §8 |
| 1 | Hero | `04-motion.md` §9 (Fixed + Slide-Over), `07` §6 (`PinnedHero`) |
| 2 | About | `06` §8's About row (mobile: text centered, image hidden entirely); social links via `07` §23 (`SocialLinks`) — icon-only, never a text list. Stat pair is 5-star reviews + years in operation, not generic percentages |
| 3 | Social Proof | `04-motion.md` §10 (marquee seam math — `w-max`, not a fixed `%` width) |
| 4 | Services | `06` §8's feature-grid row; mobile tiles via `07` §22 (`CompactTile`); tap opens §3 below's Service Detail |
| 5 | Team | Same `CompactTile` pattern, more breathing room than Services gets (`06` §8); tap opens the Team Member popup, §3 below |
| 6 | Gallery | `07` §18 (data-driven pagination, never a hardcoded count) + §17 (`Lightbox`, array-aware across pages) |
| 7 | Testimonials | `07` §19 (`MobileSlider`/`useAutoAdvance` — state-index, no visible reverse-scroll) |
| 8 | Chat Agent | `07` §25 (`ChatWidget` — mobile-safe centered overlay, demo-only, no real backend) |
| 9 | News | Same mobile-slider technique as Testimonials; tap opens the News Reader popup, §3 below. **Required** |
| 10 | Contact Info | `05` §9 (links, never a form) + `06` §8 (desktop rows vs. mobile icon-circle row); every day of the week listed, never collapsed |
| 11 | FAQ | Accordion per `07` §10; first 5 questions shown, a "More questions" button reveals the rest at once (no incremental paging). **Required** |
| 12 | Location Map | `07` §21 (Leaflet + Nominatim, CartoDB Positron greyscale, no API key). **Required.** Visual map only — §10's directions link already covers the address, so this section never restates it as text |
| 13 | Footer | `07` §23 (`SocialLinks`, icon-only) |

**Four modals are equally required, not optional extras** — they're global overlays a build can
still silently drop the same way a section can, so the Phase 7 gate checks these too:

| Modal | Opens from | Built on |
|---|---|---|
| Booking Wizard | Nav CTA, Hero CTA, each Service's "Book" | `07` §20 (`BookingWizard`) — pre-seeds the service when opened from a card |
| Image Lightbox | Any Gallery photo | `07` §17 — counter/navigation address the full array, not just the visible page |
| Team Member popup | Any Team card (whole card is the trigger) | `07` §26 (`DetailModal`) |
| News Reader popup | Any News card | `07` §26 (`DetailModal`) |

**A lighter alternative** exists for a simpler single-page brief that doesn't need booking,
staff, or a gallery — Hero → Social Proof → Problem/Solution (PAS) → Features/Benefits →
Process → Testimonials → FAQ → Final CTA → Footer. Use the 14-section structure above by
default for this framework's current scope; fall back to the shorter list only when explicitly
asked for something simpler.

**Vary the visual treatment across whichever list is in use.** If Services, Team, and
Testimonials are all identical three-card grids, the page reads as a template regardless of how
good the styling is. See `08-anti-patterns.md` §2.

---

## 2. Copywriting Formulas

### PAS (Problem, Agitation, Solution)
For introductory paragraphs and "About" sections.
* **Problem:** State the specific pain. *"Chronic back pain ruining your workday?"*
* **Agitation:** Make it real. *"You've tried stretching, pills, and ergonomic chairs, but that
  sharp knot between your shoulder blades won't let up."*
* **Solution:** Introduce the offer. *"Our targeted deep-tissue therapy dissolves chronic
  tension in a single session."*

### AIDA (Attention, Interest, Desire, Action)
For overall page flow.
* **Attention:** Bold hero headline, under 8 words.
* **Interest:** Subheadline explaining the mechanism.
* **Desire:** Testimonials and imagery.
* **Action:** High-contrast buttons.

### Before / After / Bridge
For hero subheads and feature copy. Where they are now → where they could be → how you get
them there. Compresses well into two sentences.

---

## 3. Headline Construction

The hero H1 is the highest-leverage text on the page.

- **Under 8 words.** 20–36 characters reads best at display size (`max-w-[16ch]`).
- Make a **specific promise**, not a category description. "Sleep through the night by Friday"
  beats "Premium Sound Therapy Services."
- **Outcome over mechanism.** The subhead explains *how*; the headline states *what changes*.
- Avoid: "Welcome to…", "We are a…", "Your partner in…", "Elevate your…", "Unlock the power of…"
- One word of the headline may be set in the display face's italic for emphasis — far more
  sophisticated than a color change or gradient.

**Subhead:** one or two sentences, ≤ 28 words, naming the mechanism and the audience.

---

## 4. Microcopy Rules (Buttons)

* Never use "Submit," "Click Here," "Learn More," or a bare "Get Started."
* Use **value-driven, first-person or imperative** action words with an object:
  "Book Your Session," "Start My Free Trial," "Get My Custom Plan," "Reserve a Spot,"
  "See Available Times," "Claim Your Consultation."
* **Support the button with risk-reducing microcopy** directly beneath it, in small muted text:
  *"No card required · 2 minutes · Cancel anytime."* This single line reliably outperforms any
  button styling change.
* Secondary CTAs should be lower-commitment, not a second primary: "See how it works,"
  "View pricing."
* **A hero's two-button row needs a one-word label on mobile.** The Hero pairs a primary and
  secondary CTA in one `flex flex-row` (`04-motion.md`/`07-react-tailwind-snippets.md` §6) at
  every breakpoint, and a full value-driven label ("Reserve Your First Session") wraps or
  crowds its neighbor on a narrow screen. Keep the persuasive full label for `sm:` and up, and
  swap in a single, still-specific word below it — "Book," "Reserve," "Shop," "Call," "Visit" —
  never a generic one like "Click" or "Go":
  ```html
  <span className="sm:hidden">Book</span>
  <span className="hidden sm:inline">Reserve Your First Session</span>
  ```
  This rule is specific to the hero's button *row* — a single standalone button elsewhere on
  the page doesn't need the swap, since it isn't fighting a neighbor for width.

---

## 5. Testimonials That Read as Real

- **Specific and quantified.** "I slept through the night for the first time in two years"
  beats "Great service, highly recommend!"
- Always include **name, title/context, and photo**. "John D." with no attribution reads as
  fabricated.
- **Vary the length.** Three quotes of identical length is a tell. One long, two short.
- Lead with the objection the customer had before converting — that's what persuades the next
  reader.

## 6. Social Proof & Stats

- Prefer **oddly specific numbers**: "1,847 sessions delivered" is more credible than "1000+."
- Avoid the universal trio of 100% / 24/7 / 10+ Years on every site.
- If real numbers aren't available, use qualitative proof (named partners, credentials,
  certifications) rather than inventing implausible ones.

## 7. Forms

- **Ask for the minimum.** Every additional field measurably reduces completion. Name + email
  is usually enough; phone only if the business actually calls.
- Labels above inputs, always visible. Placeholder-only labels fail accessibility and usability.
- Inline validation on blur, not on submit.
- The submit button states the outcome: "Send My Request," not "Submit."
- Confirm success in place — never leave the user wondering whether it worked.
- **A form isn't the default contact mechanism** — for a local or service business, a direct
  `tel:`/`mailto:`/directions link (§9) usually converts better and needs no backend. Reach for
  a form only where the request genuinely needs structured intake a phone call or click can't
  give — a multi-step booking flow (§20 in `07-react-tailwind-snippets.md`) is not "a form," it's
  a wizard, and its steps should be selections and pickers, not a wall of text inputs.

---

## 8. Voice Calibration by Industry

| Industry | Voice | Avoid |
|---|---|---|
| Wellness / Spa | Calm, sensory, second person | Hype, urgency, exclamation marks |
| Legal / Financial | Precise, measured, credentialed | Slang, emoji, over-promising |
| Tech / SaaS | Direct, benefit-led, concrete | Buzzword stacking, "revolutionary" |
| Trades / Home Services | Plain, confident, practical | Corporate abstraction |
| Education / Childcare | Warm, encouraging, clear | Condescension, jargon |
| Luxury / Jewelry | Spare, restrained, understated | Discount language, urgency |
| Fitness | Energetic, imperative, motivating | Shame-based framing |
| Healthcare | Reassuring, clear, non-alarming | Diagnostic claims, fear appeals |

**Universal:** write in second person ("you"), keep sentences under ~20 words, and prefer
concrete nouns to abstract ones. Never make medical, legal, or financial claims that a real
business would need to substantiate.

---

## 9. Contact: Links, Not a Form

For a local or service business, the highest-converting contact section is often not a form at
all — it's three direct, one-tap actions: call, email, get directions.

* **`tel:` and `mailto:` links**, not routed through a "Send us a message" form that adds a step
  and a chance to be ignored.
* **Directions, not a static address.** Link the address to
  `https://www.google.com/maps/dir/?api=1&destination=<url-encoded address>` —
  deliberately **omit the `origin` parameter** so Google Maps defaults the starting point to
  the visitor's current location, rather than requiring them to type one in.
* **Every day of the week, not a collapsed range.** "Mon–Sat: 9am–8pm" hides that Wednesday is
  actually different, and a visitor checking on a Sunday has no way to tell if "Closed" is
  implied or just omitted. List all seven days explicitly, each with its own hours or "Closed."
* On mobile, these three links commonly collapse into a compact row of icon-only circular
  buttons — visually like a social-icon row. Keep the `aria-label` on each ("Call [number]",
  "Get directions to [address]") even when the text label is visually hidden; see
  `06-ui-ux-accessibility.md` §8.
* A **map is a visual aid, not a duplicate of this content.** If the page also has a location
  map section, it shouldn't repeat the address as its own separate block — one section states
  the address as a directions link, the other renders the map. Two sections independently
  claiming to be "the" contact/location section is a sign one of them shouldn't exist; see
  `08-anti-patterns.md` §7.
