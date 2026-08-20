# Conversion Rate Optimization (CRO) & Copywriting

When generating text for landing pages, **NEVER use "Lorem Ipsum."** Always write realistic,
industry-specific copy using the frameworks below. Placeholder copy is the fastest way to make
a beautiful layout look unfinished — and it makes the design impossible to evaluate, because
real copy has real lengths.

---

## 1. Standard High-Converting Section Order

Unless explicitly told otherwise, structure landing pages in this flow:

1. **Hero** — the big promise. What it is, who it's for, and the primary CTA.
2. **Social Proof** — "trusted by" logos or authority badges, immediately below the fold.
3. **Problem/Solution (PAS)** — name the pain, then introduce the service as the resolution.
4. **Features/Benefits** — Rule of 3 or Rule of 6. Focus on *outcomes*, not features.
5. **Process** — 3–4 numbered steps. Reduces perceived risk by making the experience concrete.
6. **Testimonials** — 2–3 highly specific quotes with name, title, and photo.
7. **Objection Handling / FAQ** — answer the 4–6 reasons someone would say no.
8. **Final CTA** — restate the offer with a lead capture form.
9. **Footer** — copyright, links, social, contact.

**Vary the visual treatment across these.** If sections 4, 5, and 6 are all three-card grids,
the page reads as a template regardless of how good the styling is. See
`08-anti-patterns.md` §2.

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
