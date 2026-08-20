# Golden React Snippets & Tailwind Configuration

Exact, tested code for standalone React + Babel HTML files. Copy these verbatim rather than
improvising — they avoid the common failure modes of the CDN/Babel environment (stale closures,
unthrottled scroll handlers, re-firing observers, hydration-free ref timing).

All components assume `const { useState, useEffect, useRef, useCallback } = React;`.

---

## 1. Tailwind CDN Configuration

Place **before** any `text/babel` script. The alpha-aware color functions are what make
`bg-accent/10`, `text-ink/70`, and `border-line/60` work with OKLCH custom properties.

```html
<script src="https://cdn.tailwindcss.com"></script>
<script>
  const withAlpha = (v) => ({ opacityValue }) =>
    opacityValue === undefined ? `oklch(var(${v}))` : `oklch(var(${v}) / ${opacityValue})`;

  tailwind.config = {
    theme: {
      extend: {
        colors: {
          ground:     withAlpha('--ground'),
          surface:    withAlpha('--surface'),
          ink:        withAlpha('--ink'),
          muted:      withAlpha('--muted'),
          line:       withAlpha('--line'),
          accent:     withAlpha('--accent'),
          'accent-ink': withAlpha('--accent-ink'),
        },
        fontFamily: {
          display: ['"Cormorant Garamond"', 'Georgia', 'serif'],
          sans:    ['"Work Sans"', 'system-ui', 'sans-serif'],
        },
        fontSize: {
          'display-xl': ['clamp(3rem, 8vw, 7rem)',        { lineHeight: '0.95', letterSpacing: '-0.03em' }],
          'display-lg': ['clamp(2.5rem, 6vw, 5rem)',      { lineHeight: '1.0',  letterSpacing: '-0.025em' }],
          'display-md': ['clamp(2rem, 4.5vw, 3.5rem)',    { lineHeight: '1.08', letterSpacing: '-0.02em' }],
          'display-sm': ['clamp(1.5rem, 3vw, 2.25rem)',   { lineHeight: '1.2',  letterSpacing: '-0.01em' }],
          'body-lg':    ['clamp(1.05rem, 1.4vw, 1.25rem)',{ lineHeight: '1.65' }],
          'overline':   ['0.75rem', { lineHeight: '1', letterSpacing: '0.18em' }],
        },
        transitionTimingFunction: {
          'out-expo':   'cubic-bezier(0.16, 1, 0.3, 1)',
          'out-quart':  'cubic-bezier(0.25, 1, 0.5, 1)',
          'in-out-soft':'cubic-bezier(0.65, 0, 0.35, 1)',
        },
        boxShadow: {
          'soft':  '0 8px 24px -12px oklch(22% 0.03 75 / 0.14)',
          'lift':  '0 24px 60px -20px oklch(22% 0.03 75 / 0.20)',
          'glow':  '0 0 48px -8px oklch(var(--accent) / 0.35)',
        },
        maxWidth: { 'measure': '65ch', 'headline': '18ch' },
      },
    },
  };
</script>
```

---

## 2. Native Scroll Hook (Performance Optimized)

Tracks scroll position for parallax. `ticking` prevents queuing multiple rAF callbacks per
frame — the original version of this hook queued one per scroll event and dropped frames.

```javascript
const useScrollY = () => {
  const [scrollY, setScrollY] = useState(0);
  useEffect(() => {
    let ticking = false;
    const handleScroll = () => {
      if (ticking) return;
      ticking = true;
      window.requestAnimationFrame(() => {
        setScrollY(window.scrollY);
        ticking = false;
      });
    };
    window.addEventListener('scroll', handleScroll, { passive: true });
    handleScroll();
    return () => window.removeEventListener('scroll', handleScroll);
  }, []);
  return scrollY;
};
```

## 3. Reduced Motion

Gate every JS-driven effect behind this.

```javascript
const usePrefersReducedMotion = () => {
  const [reduced, setReduced] = useState(false);
  useEffect(() => {
    const mq = window.matchMedia('(prefers-reduced-motion: reduce)');
    setReduced(mq.matches);
    const onChange = (e) => setReduced(e.matches);
    mq.addEventListener('change', onChange);
    return () => mq.removeEventListener('change', onChange);
  }, []);
  return reduced;
};
```

## 4. Scroll Reveal — Hook + Component

Fires **once** and unobserves. `rootMargin` bottom inset triggers slightly before the element
is fully visible, so content is already settling as it enters.

```javascript
const useInView = (options = {}) => {
  const ref = useRef(null);
  const [inView, setInView] = useState(false);
  useEffect(() => {
    const el = ref.current;
    if (!el) return;
    if (!('IntersectionObserver' in window)) { setInView(true); return; }
    const observer = new IntersectionObserver(([entry]) => {
      if (entry.isIntersecting) { setInView(true); observer.unobserve(el); }
    }, { threshold: 0.15, rootMargin: '0px 0px -10% 0px', ...options });
    observer.observe(el);
    return () => observer.disconnect();
  }, []);
  return [ref, inView];
};

// Wrap anything. `delay` in ms drives the stagger.
const Reveal = ({ children, delay = 0, y = 24, className = '' }) => {
  const [ref, inView] = useInView();
  const reduced = usePrefersReducedMotion();
  const show = inView || reduced;
  return (
    <div
      ref={ref}
      className={`transition-[opacity,transform] duration-700 ease-out-expo ${className}`}
      style={{
        opacity: show ? 1 : 0,
        transform: show ? 'none' : `translate3d(0, ${y}px, 0)`,
        transitionDelay: reduced ? '0ms' : `${delay}ms`,
      }}
    >
      {children}
    </div>
  );
};
```

**Stagger usage** — cap the multiplier so late items don't lag:

```jsx
{items.map((item, i) => (
  <Reveal key={item.id} delay={Math.min(i, 5) * 80}>
    <Card {...item} />
  </Reveal>
))}
```

## 5. Sticky Glass Nav

Transparent over the hero, glass after scroll. Transitions background/blur/border only —
never height, which would reflow the page.

```jsx
const Nav = ({ links }) => {
  const scrollY = useScrollY();
  const solid = scrollY > 40;
  const [open, setOpen] = useState(false);
  return (
    <header
      className={`fixed inset-x-0 top-0 z-50 transition-[background-color,backdrop-filter,border-color]
                  duration-300 ease-out-quart border-b
                  ${solid ? 'bg-ground/80 backdrop-blur-md border-line' : 'bg-transparent border-transparent'}`}
    >
      <nav className="mx-auto flex max-w-6xl items-center justify-between px-6 py-4">
        <a href="#" className="font-display text-xl tracking-tight text-ink">Brand</a>
        <ul className="hidden items-center gap-8 md:flex">
          {links.map((l) => (
            <li key={l.href}>
              <a href={l.href}
                 className="group relative text-sm text-ink/80 transition-colors hover:text-ink">
                {l.label}
                <span className="absolute -bottom-1 left-0 h-px w-0 bg-accent
                                 transition-[width] duration-300 ease-out-expo group-hover:w-full" />
              </a>
            </li>
          ))}
        </ul>
        <button
          onClick={() => setOpen((v) => !v)}
          aria-label={open ? 'Close menu' : 'Open menu'}
          aria-expanded={open}
          className="grid h-11 w-11 place-items-center md:hidden"
        >
          <span className="sr-only">Menu</span>
          <svg viewBox="0 0 24 24" className="h-5 w-5" fill="none"
               stroke="currentColor" strokeWidth="1.5">
            <path d={open ? 'M6 6l12 12M18 6L6 18' : 'M3 7h18M3 17h18'} strokeLinecap="round" />
          </svg>
        </button>
      </nav>
      {open && (
        <ul className="border-t border-line bg-ground px-6 py-4 md:hidden">
          {links.map((l) => (
            <li key={l.href}>
              <a href={l.href} onClick={() => setOpen(false)}
                 className="block py-3 text-ink">{l.label}</a>
            </li>
          ))}
        </ul>
      )}
    </header>
  );
};
```

## 6. Parallax Hero

Disabled below 768px and under reduced-motion. `translate3d` promotes to its own layer. This
hero scrolls away normally (its background image just parallaxes internally as it does) — for a
hero that stays pinned to the viewport while page content slides up and over it instead, use the
Fixed + Slide-Over technique in `04-motion.md` §9, which composes with the same floating-card
content below.

Content lives in a **floating card**, not directly on the photograph — this is what lets the
mobile and desktop treatments diverge without the copy ever touching the busy image behind it.

**Mobile and desktop deliberately position and align the card differently** — this is a
dual-layout section per `06-ui-ux-accessibility.md` §8, done with responsive variants inside
one card rather than two separate DOM trees, since only alignment and vertical anchor change:

| | Mobile | Desktop |
|---|---|---|
| Card width | `w-full` | `w-[55%]` |
| Vertical anchor | Upper third: `items-start pt-[20vh]` | Centered, slight lift: `items-center -translate-y-8` |
| Text/button alignment | Centered: `text-center`, buttons `justify-center` | Left: `text-left`, buttons `justify-start` |

```jsx
const ParallaxHero = ({ src, alt, eyebrow, children }) => {
  const scrollY = useScrollY();
  const reduced = usePrefersReducedMotion();
  const [enabled, setEnabled] = useState(false);
  useEffect(() => {
    const mq = window.matchMedia('(min-width: 768px)');
    setEnabled(mq.matches);
    const on = (e) => setEnabled(e.matches);
    mq.addEventListener('change', on);
    return () => mq.removeEventListener('change', on);
  }, []);
  const offset = enabled && !reduced ? scrollY * 0.28 : 0;

  return (
    <section className="relative isolate flex h-screen min-h-[640px] w-full overflow-hidden">
      <img
        src={src} alt={alt}
        className="absolute inset-0 -z-10 h-[125%] w-full object-cover"
        style={{ transform: `translate3d(0, ${offset}px, 0)`, willChange: 'transform' }}
      />
      {/* Required scrim — never rely on the photo for contrast. */}
      <div className="absolute inset-0 -z-10 bg-gradient-to-r from-ink/70 via-ink/40 to-ink/20" />

      <div className="relative mx-auto flex h-full w-full max-w-6xl items-start justify-center
                      px-4 pt-[20vh] text-center sm:px-6 md:items-center md:justify-start
                      md:-translate-y-8 md:pt-0 md:text-left lg:px-8">
        <div className="w-full rounded-3xl border border-white/15 bg-ink/75 p-6 shadow-lift
                        backdrop-blur-xl sm:p-8 md:w-[55%] md:p-12">
          {eyebrow && (
            <p className="mb-4 inline-flex items-center gap-2 rounded-full border
                          border-accent/30 bg-accent/20 px-3.5 py-1.5 text-overline uppercase
                          text-white">
              {eyebrow}
            </p>
          )}
          {children}
        </div>
      </div>
    </section>
  );
};
```

Buttons inside `children` should use `flex flex-row gap-3` **on both breakpoints** — sized to
fit without wrapping — rather than stacking on mobile (`flex-col sm:flex-row`). A two-button row
reads as more confident and matches the rest of the card's now-centered composition; the row
just gets `justify-center` on mobile and `justify-start` on desktop via the parent's alignment.

To guarantee that row never wraps, give each button label a one-word mobile fallback (see
`05-copywriting-cro.md` §4):

```jsx
<Button href="#book" className="group">
  <span className="sm:hidden">Book</span>
  <span className="hidden sm:inline">Reserve Your First Session</span>
  <Arrow />
</Button>
<Button href="#services" variant="onDark">
  <span className="sm:hidden">Services</span>
  <span className="hidden sm:inline">See how a session works</span>
</Button>
```

### Pinned Variant — `PinnedHero`

The Fixed + Slide-Over technique from `04-motion.md` §9, complete: the whole hero is
`position: fixed` rather than scrolling away in normal flow, and its floating card eases out
continuously as the visitor scrolls down and back in as they scroll back up — because it's a
live function of `scrollY`, never a one-time reveal. Requires the page's `<main>` (or whatever
wraps everything after the hero) to start at `mt-[100vh]` so content slides up over it with no
dead scroll gap; see the full recipe in §9.

```jsx
const PinnedHero = ({ src, alt, eyebrow, children }) => {
  const scrollY = useScrollY();
  const reduced = usePrefersReducedMotion();
  const [wide, setWide] = useState(false);
  useEffect(() => {
    const mq = window.matchMedia('(min-width: 768px)');
    setWide(mq.matches);
    const on = (e) => setWide(e.matches);
    mq.addEventListener('change', on);
    return () => mq.removeEventListener('change', on);
  }, []);
  const parallax = wide && !reduced ? scrollY * 0.28 : 0;

  // Derived from scrollY on every render — no extra state, no second scroll listener.
  // smoothstep gives the fade an actual eased shape (slow-fast-slow) instead of a raw linear
  // ratio, and being symmetric it eases out one direction and in the other for free — see
  // 04-motion.md §9 for why a plain `1 - scrollY / 600` reads as mechanical, not eased.
  const smoothstep = (t) => t * t * (3 - 2 * t);
  const fadeT = Math.min(1, Math.max(0, scrollY / 600));
  const scaleT = Math.min(1, Math.max(0, scrollY / 2000));
  const opacity = reduced ? 1 : 1 - smoothstep(fadeT);
  const scale = reduced ? 1 : 1 - 0.1 * smoothstep(scaleT);

  return (
    <section className="fixed inset-0 z-0 h-screen w-full overflow-hidden pointer-events-none">
      <img
        src={src} alt={alt}
        className="absolute inset-0 -z-10 h-[125%] w-full object-cover"
        style={{ transform: `translate3d(0, ${parallax}px, 0)`, willChange: 'transform' }}
      />
      <div className="absolute inset-0 -z-10 bg-gradient-to-r from-ink/70 via-ink/40 to-ink/20" />

      <div
        className="relative mx-auto flex h-full w-full max-w-6xl items-start justify-center
                  px-4 pt-[20vh] text-center sm:px-6 md:items-center md:justify-start
                  md:-translate-y-8 md:pt-0 md:text-left lg:px-8"
        style={{ opacity, transform: `scale(${scale})`, transformOrigin: wide ? 'left center' : 'center' }}
      >
        <div className="w-full rounded-3xl border border-white/15 bg-ink/75 p-6 shadow-lift
                        backdrop-blur-xl pointer-events-auto sm:p-8 md:w-[55%] md:p-12">
          {eyebrow && (
            <p className="mb-4 inline-flex items-center gap-2 rounded-full border
                          border-accent/30 bg-accent/20 px-3.5 py-1.5 text-overline uppercase
                          text-white">
              {eyebrow}
            </p>
          )}
          {children}
        </div>
      </div>
    </section>
  );
};
```

Two things easy to get wrong:

- **`pointer-events-none` on the section, `pointer-events-auto` back on the card.** The pinned
  hero sits in front of everything as a fixed layer; without the section-level `none`, it would
  intercept clicks on content that's visually scrolled above it. The card re-enables events so
  its own buttons stay clickable.
- **The opacity/scale transform lives on the wrapper, not inline on `style` mixed with
  Tailwind's `transition-*` classes.** This is a per-frame scroll-driven value — giving it a
  CSS `transition` would fight the constant updates and read as laggy or stepped, not eased.
  Let the value itself be the animation; reserve `transition-*` classes for state changes like
  hover. **Do not add `transition-all` "to smooth it out"** — that instinct is exactly backward
  here; the smoothing comes from `smoothstep`, not from CSS.
- **Copy the mobile/desktop class list on the inner wrapper exactly** —
  `items-start justify-center px-4 pt-[20vh] text-center` for mobile,
  `md:items-center md:justify-start md:-translate-y-8 md:pt-0 md:text-left` for desktop. This
  is the one part of the component most often simplified away when hand-written from memory,
  and dropping it silently reverts the hero to desktop-only alignment on every breakpoint —
  headline and buttons left-aligned and vertically centered on a phone, which is the exact
  regression this pattern exists to prevent (see `06-ui-ux-accessibility.md` §8's Hero row).

## 7. Count-Up Stat

```jsx
const CountUp = ({ end, suffix = '', duration = 1600 }) => {
  const [ref, inView] = useInView({ threshold: 0.4 });
  const reduced = usePrefersReducedMotion();
  const [value, setValue] = useState(0);

  useEffect(() => {
    if (!inView) return;
    if (reduced) { setValue(end); return; }
    let raf, start;
    const step = (t) => {
      if (start === undefined) start = t;
      const p = Math.min((t - start) / duration, 1);
      setValue(Math.round(end * (1 - Math.pow(1 - p, 3)))); // easeOutCubic
      if (p < 1) raf = requestAnimationFrame(step);
    };
    raf = requestAnimationFrame(step);
    return () => cancelAnimationFrame(raf);
  }, [inView, end, duration, reduced]);

  return (
    <span ref={ref} className="tabular-nums">{value.toLocaleString()}{suffix}</span>
  );
};
```

## 8. Seamless Marquee

Track duplicated and translated `-50%`, so the loop has no visible seam.

```jsx
const Marquee = ({ items, speed = 40 }) => (
  <div className="group relative overflow-hidden py-6
                  [mask-image:linear-gradient(to_right,transparent,black_12%,black_88%,transparent)]">
    <div className="flex w-max gap-16 group-hover:[animation-play-state:paused]"
         style={{ animation: `marquee ${speed}s linear infinite` }}>
      {[...items, ...items].map((item, i) => (
        <span key={i} className="shrink-0 text-sm uppercase tracking-[0.18em] text-muted"
              aria-hidden={i >= items.length}>
          {item}
        </span>
      ))}
    </div>
  </div>
);
```

```css
@keyframes marquee { from { transform: translateX(0); } to { transform: translateX(-50%); } }
```

## 9. Premium Image Card

The image scales inside a fixed frame; the card lifts. This pairing is the highest-value
hover in the system.

```jsx
const ImageCard = ({ src, alt, overline, title, body }) => (
  <a href="#" className="group block">
    <div className="overflow-hidden rounded-2xl bg-surface shadow-soft
                    transition-[transform,box-shadow] duration-500 ease-out-expo
                    group-hover:-translate-y-1 group-hover:shadow-lift">
      <div className="aspect-[4/3] overflow-hidden">
        <img src={src} alt={alt}
             className="h-full w-full object-cover transition-transform duration-700
                        ease-out-expo group-hover:scale-[1.04]" />
      </div>
      <div className="p-6">
        <p className="text-overline uppercase text-accent">{overline}</p>
        <h3 className="mt-3 font-display text-display-sm text-ink">{title}</h3>
        <p className="mt-2 max-w-measure text-muted">{body}</p>
      </div>
    </div>
  </a>
);
```

## 10. Accessible Accordion

```jsx
const Accordion = ({ items }) => {
  const [openIdx, setOpenIdx] = useState(0);
  return (
    <div className="divide-y divide-line border-y border-line">
      {items.map((item, i) => {
        const open = openIdx === i;
        return (
          <div key={i}>
            <button
              onClick={() => setOpenIdx(open ? -1 : i)}
              aria-expanded={open}
              className="flex w-full items-center justify-between gap-6 py-6 text-left
                         min-h-[48px] focus-visible:outline-none focus-visible:ring-2
                         focus-visible:ring-accent focus-visible:ring-offset-4"
            >
              <span className="font-display text-display-sm text-ink">{item.q}</span>
              <span className={`shrink-0 transition-transform duration-300 ease-out-quart
                                ${open ? 'rotate-45' : ''}`} aria-hidden="true">
                <svg viewBox="0 0 24 24" className="h-5 w-5" fill="none"
                     stroke="currentColor" strokeWidth="1.5">
                  <path d="M12 5v14M5 12h14" strokeLinecap="round" />
                </svg>
              </span>
            </button>
            <div className="grid transition-[grid-template-rows] duration-400 ease-in-out-soft"
                 style={{ gridTemplateRows: open ? '1fr' : '0fr' }}>
              <div className="overflow-hidden">
                <p className="max-w-measure pb-6 text-muted">{item.a}</p>
              </div>
            </div>
          </div>
        );
      })}
    </div>
  );
};
```

The `grid-template-rows: 0fr → 1fr` technique animates to auto height without measuring the
content — it is the correct modern solution and avoids `max-height` guessing.

## 11. Button Primitives

```jsx
const Button = ({ as: Tag = 'a', variant = 'primary', className = '', children, ...rest }) => {
  const base = `inline-flex items-center justify-center gap-2 min-h-[48px] px-8 py-3
                text-sm font-medium rounded-full transition-[transform,box-shadow,background-color]
                duration-300 ease-out-expo focus-visible:outline-none focus-visible:ring-2
                focus-visible:ring-accent focus-visible:ring-offset-2 focus-visible:ring-offset-ground`;
  const variants = {
    primary: 'bg-accent text-accent-ink shadow-soft hover:-translate-y-0.5 hover:shadow-lift',
    ghost:   'border border-line text-ink hover:border-ink/40 hover:-translate-y-0.5',
    onDark:  'bg-surface text-ink hover:-translate-y-0.5 hover:shadow-lift',
  };
  return <Tag className={`${base} ${variants[variant]} ${className}`} {...rest}>{children}</Tag>;
};
```

## 12. Bento Grid

Asymmetric grid — one tile spans two columns/rows, the rest are equal. Backs the **Bento
Grid** layout archetype in `01-design-system.md`. Adjust the `span` values per tile via the
`className` passed in each item's data, not by editing the grid itself.

```jsx
const BentoGrid = ({ children }) => (
  <div className="grid auto-rows-[minmax(14rem,auto)] grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-4">
    {children}
  </div>
);

const BentoCard = ({ span = '', title, body, icon, className = '' }) => (
  <div className={`group flex flex-col justify-between rounded-2xl bg-surface p-8 shadow-soft
                   transition-[transform,box-shadow] duration-500 ease-out-expo
                   hover:-translate-y-1 hover:shadow-lift ${span} ${className}`}>
    <div>
      {icon && <div className="h-8 w-8 text-accent">{icon}</div>}
      <h3 className="mt-4 font-display text-display-sm text-ink">{title}</h3>
      <p className="mt-2 max-w-measure text-muted">{body}</p>
    </div>
  </div>
);

// Usage — first tile spans 2 columns and 2 rows:
// <BentoGrid>
//   <BentoCard span="sm:col-span-2 lg:row-span-2" title="…" body="…" />
//   <BentoCard title="…" body="…" />
//   <BentoCard title="…" body="…" />
//   <BentoCard span="sm:col-span-2" title="…" body="…" />
// </BentoGrid>
```

## 13. Before/After Slider

Draggable comparison. Backs the **Before/After Slider** archetype (trades, beauty, fitness,
renovation, dental). Pointer events handle both mouse and touch; the range input underneath
keeps it keyboard-accessible.

```jsx
const BeforeAfterSlider = ({ before, after, beforeAlt = 'Before', afterAlt = 'After' }) => {
  const [pos, setPos] = useState(50);
  const ref = useRef(null);

  const updateFromClientX = (clientX) => {
    const rect = ref.current.getBoundingClientRect();
    const pct = ((clientX - rect.left) / rect.width) * 100;
    setPos(Math.min(100, Math.max(0, pct)));
  };

  return (
    <div ref={ref}
      className="relative aspect-[4/3] w-full select-none overflow-hidden rounded-2xl shadow-lift"
      onPointerDown={(e) => { e.currentTarget.setPointerCapture(e.pointerId); updateFromClientX(e.clientX); }}
      onPointerMove={(e) => { if (e.buttons === 1) updateFromClientX(e.clientX); }}
    >
      <img src={after} alt={afterAlt} className="absolute inset-0 h-full w-full object-cover" draggable={false} />
      <div className="absolute inset-0 h-full overflow-hidden" style={{ width: `${pos}%` }}>
        <img src={before} alt={beforeAlt}
             className="h-full w-full max-w-none object-cover"
             style={{ width: ref.current ? ref.current.offsetWidth : '100%' }}
             draggable={false} />
      </div>
      <div className="absolute inset-y-0 w-0.5 bg-white/90" style={{ left: `${pos}%` }}>
        <div className="absolute left-1/2 top-1/2 grid h-11 w-11 -translate-x-1/2 -translate-y-1/2
                        place-items-center rounded-full bg-white shadow-lift">
          <svg viewBox="0 0 24 24" className="h-4 w-4" fill="none" stroke="currentColor" strokeWidth="2">
            <path d="M8 7l-4 5 4 5M16 7l4 5-4 5" strokeLinecap="round" strokeLinejoin="round" />
          </svg>
        </div>
      </div>
      <input type="range" min="0" max="100" value={pos}
             onChange={(e) => setPos(Number(e.target.value))}
             aria-label="Comparison slider"
             className="absolute inset-x-0 bottom-3 mx-auto w-1/2 accent-white opacity-0 focus-visible:opacity-100" />
    </div>
  );
};
```

## 14. Pricing / Comparison Table

Three tiers, the middle visually elevated. Backs the **Comparison/Pricing Table** archetype.

```jsx
const PricingTable = ({ tiers }) => (
  <div className="grid gap-8 md:grid-cols-3 md:items-end">
    {tiers.map((t) => (
      <div key={t.name}
        className={`rounded-2xl p-8 transition-[transform,box-shadow] duration-500 ease-out-expo
                    ${t.featured
                      ? 'bg-ink text-ground shadow-lift md:-translate-y-4 md:py-12'
                      : 'bg-surface text-ink shadow-soft hover:-translate-y-1 hover:shadow-lift'}`}>
        {t.featured && (
          <p className="text-overline uppercase text-accent">Most Popular</p>
        )}
        <h3 className={`mt-3 font-display text-display-sm ${t.featured ? '' : ''}`}>{t.name}</h3>
        <p className="mt-4 flex items-baseline gap-1">
          <span className="font-display text-display-md tabular-nums">{t.price}</span>
          <span className={t.featured ? 'text-ground/60' : 'text-muted'}>/{t.period}</span>
        </p>
        <ul className="mt-8 space-y-3">
          {t.features.map((f) => (
            <li key={f} className="flex items-start gap-3 text-sm">
              <svg viewBox="0 0 24 24" className="mt-0.5 h-4 w-4 shrink-0 text-accent"
                   fill="none" stroke="currentColor" strokeWidth="2">
                <path d="M5 13l4 4L19 7" strokeLinecap="round" strokeLinejoin="round" />
              </svg>
              <span className={t.featured ? 'text-ground/80' : 'text-muted'}>{f}</span>
            </li>
          ))}
        </ul>
        <Button href={t.href || '#'} variant={t.featured ? 'onDark' : 'ghost'} className="mt-8 w-full">
          {t.cta || 'Choose Plan'}
        </Button>
      </div>
    ))}
  </div>
);
```

## 15. Timeline

Vertical chronological narrative. Backs the **Timeline** archetype (origin stories,
milestones, class/session schedules).

```jsx
const Timeline = ({ items }) => (
  <ol className="relative border-l border-line pl-8">
    {items.map((item, i) => (
      <Reveal as="li" key={item.year} delay={Math.min(i, 5) * 90} className="relative pb-12 last:pb-0">
        <span className="absolute -left-[calc(2rem+5px)] top-1.5 h-2.5 w-2.5 rounded-full bg-accent
                         ring-4 ring-ground" aria-hidden="true" />
        <p className="text-overline uppercase text-accent">{item.year}</p>
        <h3 className="mt-2 font-display text-display-sm">{item.title}</h3>
        <p className="mt-2 max-w-measure text-muted">{item.body}</p>
      </Reveal>
    ))}
  </ol>
);
```

`Reveal` as defined in §4 renders a `<div>`; pass `as="li"` there (swap the hardcoded `<div>`
for `React.createElement(as, …)`, or duplicate the component as `RevealLi` for a list context)
so the timeline stays valid `<ol><li>` markup.

## 16. Modal Primitive

Every dialog on a site — a booking flow, a team-member profile, an article reader, a
newsletter prompt — should share **one** shell so they read as one system rather than four
unrelated popups. Build every centered dialog on this, not a bespoke overlay each time.

```javascript
const useBodyScrollLock = (locked) => {
  useEffect(() => {
    if (!locked) return;
    const prev = document.body.style.overflow;
    document.body.style.overflow = 'hidden';
    return () => { document.body.style.overflow = prev; };
  }, [locked]);
};
```

```jsx
const Modal = ({ open, onClose, children, labelledBy, className = '' }) => {
  useBodyScrollLock(open);
  useEffect(() => {
    if (!open) return;
    const onKey = (e) => { if (e.key === 'Escape') onClose(); };
    window.addEventListener('keydown', onKey);
    return () => window.removeEventListener('keydown', onKey);
  }, [open, onClose]);

  if (!open) return null;
  return (
    <div className="fixed inset-0 z-[200] grid place-items-center p-4 sm:p-6" role="dialog"
         aria-modal="true" aria-labelledby={labelledBy}>
      {/* The handler goes on the overlay itself, not the outer dialog div. The overlay is a
          separate absolutely-positioned sibling that fully covers the outer div's box, so any
          click in the "empty" gutter area hits the overlay first — e.target on the outer div's
          own onMouseDown would never equal e.currentTarget, and outside-click would silently
          never fire. See 08-anti-patterns.md for this exact failure mode. */}
      <div className="absolute inset-0 bg-ink/70 backdrop-blur-sm" onMouseDown={onClose} />
      <div className={`relative w-full max-w-lg animate-[modal-in_.3s_cubic-bezier(0.16,1,0.3,1)]
                       rounded-2xl bg-surface p-6 shadow-lift sm:p-8 ${className}`}>
        <button onClick={onClose} aria-label="Close"
                className="absolute right-4 top-4 grid h-11 w-11 place-items-center rounded-full
                           text-muted transition-colors hover:bg-ground hover:text-ink
                           focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-accent">
          <svg viewBox="0 0 24 24" className="h-5 w-5" fill="none" stroke="currentColor" strokeWidth="1.5">
            <path d="M6 6l12 12M18 6L6 18" strokeLinecap="round" />
          </svg>
        </button>
        {children}
      </div>
    </div>
  );
};
```

```css
@keyframes modal-in { from { opacity: 0; transform: translateY(12px) scale(.98); } to { opacity: 1; transform: none; } }
```

Non-negotiable on every dialog built from this shell: closes on **Escape**, closes on **click
outside** the panel (the `onMouseDown` guard above — clicking the panel itself never bubbles to
the overlay), closes on the **×** button, and **locks body scroll** while open. A "Team member"
modal, a "Read more" article modal, and a booking modal are all just different `children` passed
into this same `<Modal>` — same overlay darkness, same radius, same close-button placement.

## 17. Lightbox

A full-bleed image viewer is a different shape than `Modal` (no card, no padding) but shares
its close/Escape/scroll-lock discipline, plus arrow-key navigation.

**The counter must track position in the full image array, not a paginated window of it.** If
a gallery only renders 9 images at a time (see §18), the Lightbox still needs to navigate and
count across the *entire* array — open image 9 and press "next" and it should advance to image
10 on the next page, not dead-end.

```jsx
const Lightbox = ({ images, index, onClose, onNavigate }) => {
  const open = index !== null;
  useBodyScrollLock(open);
  useEffect(() => {
    if (!open) return;
    const onKey = (e) => {
      if (e.key === 'Escape') onClose();
      if (e.key === 'ArrowRight') onNavigate((index + 1) % images.length);
      if (e.key === 'ArrowLeft') onNavigate((index - 1 + images.length) % images.length);
    };
    window.addEventListener('keydown', onKey);
    return () => window.removeEventListener('keydown', onKey);
  }, [open, index, images.length, onClose, onNavigate]);

  if (!open) return null;
  return (
    <div className="fixed inset-0 z-[200] grid place-items-center bg-ink/90 p-4"
         onMouseDown={(e) => { if (e.target === e.currentTarget) onClose(); }}>
      <button onClick={onClose} aria-label="Close"
              className="absolute right-4 top-4 grid h-11 w-11 place-items-center rounded-full text-white/80 hover:text-white">
        <svg viewBox="0 0 24 24" className="h-6 w-6" fill="none" stroke="currentColor" strokeWidth="1.5">
          <path d="M6 6l12 12M18 6L6 18" strokeLinecap="round" />
        </svg>
      </button>
      <button onClick={() => onNavigate((index - 1 + images.length) % images.length)} aria-label="Previous image"
              className="absolute left-2 grid h-12 w-12 place-items-center rounded-full text-white/80 hover:text-white sm:left-6">
        <svg viewBox="0 0 24 24" className="h-6 w-6" fill="none" stroke="currentColor" strokeWidth="1.5">
          <path d="M15 6l-6 6 6 6" strokeLinecap="round" strokeLinejoin="round" />
        </svg>
      </button>
      <img src={images[index].src} alt={images[index].alt}
           className="max-h-[85vh] max-w-[90vw] rounded-sm object-contain shadow-lift" />
      <button onClick={() => onNavigate((index + 1) % images.length)} aria-label="Next image"
              className="absolute right-2 grid h-12 w-12 place-items-center rounded-full text-white/80 hover:text-white sm:right-6">
        <svg viewBox="0 0 24 24" className="h-6 w-6" fill="none" stroke="currentColor" strokeWidth="1.5">
          <path d="M9 6l6 6-6 6" strokeLinecap="round" strokeLinejoin="round" />
        </svg>
      </button>
      <p className="absolute bottom-6 text-sm tabular-nums text-white/70">{index + 1} / {images.length}</p>
    </div>
  );
};
```

## 18. Paginated Gallery — Data-Driven, Not Assumed

Derive pagination controls from the array's actual length, never from how many images the demo
happens to ship with. Seeding the gallery with exactly 9 placeholder images must not be the
*reason* the arrows are hidden — the `images.length > PAGE_SIZE` check is what hides them, and
it needs to keep working correctly the day a real client hands over 40 photos.

**`grid-cols-3` stays fixed at every breakpoint — never drop to fewer columns on mobile.**
The instinct to "fix" a cramped mobile gallery by going to 2 or 1 columns actually makes it
worse: it turns a fixed, predictable 3×3 block into a long scroll, and breaks the "9 images
visible at once" premise the pagination arrows depend on. Instead, let the grid's own fluid
sizing shrink each tile (a `grid-cols-3` cell is always exactly one third of its container,
whatever that container's width is) and tighten the *gap* on small screens — that's what keeps
all 9 thumbnails on screen without introducing horizontal scroll.

```jsx
const PAGE_SIZE = 9;

const Gallery = ({ images }) => {
  const [page, setPage] = useState(0);
  const [lightboxIndex, setLightboxIndex] = useState(null);
  const pageCount = Math.ceil(images.length / PAGE_SIZE);
  const visible = images.slice(page * PAGE_SIZE, page * PAGE_SIZE + PAGE_SIZE);

  return (
    <div>
      <div className="grid grid-cols-3 gap-1.5 sm:gap-4">
        {visible.map((img, i) => {
          const globalIndex = page * PAGE_SIZE + i; // Lightbox always addresses the full array.
          return (
            <button key={img.src} onClick={() => setLightboxIndex(globalIndex)}
                    className="group aspect-square overflow-hidden rounded-sm">
              <img src={img.src} alt={img.alt}
                   className="h-full w-full object-cover transition-transform duration-700
                              ease-out-expo group-hover:scale-[1.04]" />
            </button>
          );
        })}
      </div>
      {images.length > PAGE_SIZE && (
        <div className="mt-6 flex justify-center gap-4">
          <button onClick={() => setPage((p) => Math.max(0, p - 1))} disabled={page === 0}
                  aria-label="Previous page" className="disabled:opacity-30">‹</button>
          <button onClick={() => setPage((p) => Math.min(pageCount - 1, p + 1))} disabled={page === pageCount - 1}
                  aria-label="Next page" className="disabled:opacity-30">›</button>
        </div>
      )}
      <Lightbox images={images} index={lightboxIndex} onClose={() => setLightboxIndex(null)} onNavigate={setLightboxIndex} />
    </div>
  );
};
```

## 19. Mobile Auto-Advance Slider

For content that's a grid on desktop but needs to be one-at-a-time on mobile (testimonials,
news/announcements). Swaps which item is **shown**, rather than physically scrolling a track —
that distinction is what avoids the visible "snap back to the start" reverse-scroll that a
naive looping scroll-track produces. Pauses briefly after the visitor interacts.

```javascript
const useAutoAdvance = (length, { interval = 5000, pauseFor = 4000 } = {}) => {
  const [index, setIndex] = useState(0);
  const pausedUntil = useRef(0);
  useEffect(() => {
    const id = setInterval(() => {
      if (Date.now() < pausedUntil.current) return;
      setIndex((i) => (i + 1) % length);
    }, interval);
    return () => clearInterval(id);
  }, [length, interval]);
  const pause = () => { pausedUntil.current = Date.now() + pauseFor; };
  return [index, (i) => { setIndex(i); pause(); }, pause];
};
```

```jsx
const MobileSlider = ({ items, renderItem }) => {
  const [index, goTo, pause] = useAutoAdvance(items.length);
  return (
    <div onTouchStart={pause}>
      {renderItem(items[index], index)}
      <div className="mt-4 flex justify-center gap-2">
        {items.map((_, i) => (
          <button key={i} onClick={() => goTo(i)} aria-label={`Go to slide ${i + 1}`}
                  className={`h-1.5 rounded-full transition-all duration-300
                             ${i === index ? 'w-6 bg-accent' : 'w-1.5 bg-line'}`} />
        ))}
      </div>
    </div>
  );
};
```

## 20. Multi-Step Wizard (Booking / Quote Flows)

A `Stepper` progress indicator plus a `Modal`-hosted flow. Show which step the visitor is on,
mark completed steps, and give every step after the first a "← Back" control. Support landing
on a later step with a value pre-filled — e.g. a "Book" link on a specific service card should
open the wizard with that service already selected, not force the visitor to re-pick it.

```jsx
const Stepper = ({ steps, current }) => (
  <ol className="mb-8 flex items-center gap-2">
    {steps.map((label, i) => {
      const done = i < current, active = i === current;
      return (
        <li key={label} className="flex flex-1 items-center gap-2">
          <span className={`grid h-8 w-8 shrink-0 place-items-center rounded-full text-xs font-medium
                            transition-colors duration-300
                            ${done ? 'bg-accent text-accent-ink'
                                   : active ? 'border-2 border-accent text-accent'
                                            : 'border border-line text-muted'}`}>
            {done ? '✓' : i + 1}
          </span>
          <span className={`hidden text-xs sm:block ${active ? 'text-ink' : 'text-muted'}`}>{label}</span>
          {i < steps.length - 1 && <span className="h-px flex-1 bg-line" aria-hidden="true" />}
        </li>
      );
    })}
  </ol>
);

// Scaffold — fill each step's body in; the shell (stepper, back control, pre-seeding,
// reset-on-reopen) is what's easy to get subtly wrong and worth copying exactly.
const BookingWizard = ({ open, onClose, initialService = null }) => {
  const steps = ['Service', 'Date', 'Time', 'Details', 'Confirm'];
  const [step, setStep] = useState(initialService ? 1 : 0);
  const [selection, setSelection] = useState({ service: initialService });

  useEffect(() => {
    if (open) { setStep(initialService ? 1 : 0); setSelection({ service: initialService }); }
  }, [open, initialService]);

  const next = () => setStep((s) => Math.min(steps.length - 1, s + 1));
  const back = () => setStep((s) => Math.max(0, s - 1));

  return (
    <Modal open={open} onClose={onClose} labelledBy="booking-title" className="max-w-xl">
      <h2 id="booking-title" className="font-display text-display-sm">Book Your Visit</h2>
      <Stepper steps={steps} current={step} />
      {step > 0 && (
        <button onClick={back} className="mb-4 text-sm text-muted hover:text-ink">← Back</button>
      )}
      {/* Render the field(s) for `steps[step]` here, writing into `selection` via setSelection,
          then a primary Button calling next() — or, on the final step, a static confirmation
          with a fabricated reference number if this is a front-end-only demo. */}
    </Modal>
  );
};
```

## 21. Location Map (Leaflet + Free Geocoding)

A real interactive map needs no paid API: **CartoDB Positron** tiles for the greyscale style
and **Nominatim** (OpenStreetMap's free geocoder) to turn a street address into coordinates on
load. Requires Leaflet's CSS and JS loaded via CDN `<script>`/`<link>` in `<head>` — Leaflet is
not React-aware, so it's driven imperatively inside a `useEffect`.

```jsx
const LocationMap = ({ address, businessName }) => {
  const mapRef = useRef(null);
  const containerRef = useRef(null);

  useEffect(() => {
    if (!window.L || mapRef.current) return; // guards against double-init in strict mode
    const map = window.L.map(containerRef.current, { scrollWheelZoom: false }).setView([0, 0], 2);
    window.L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
      attribution: '© OpenStreetMap contributors © CARTO', maxZoom: 19,
    }).addTo(map);
    mapRef.current = map;

    fetch(`https://nominatim.openstreetmap.org/search?format=json&limit=1&q=${encodeURIComponent(address)}`)
      .then((r) => r.json())
      .then(([result]) => {
        if (!result) throw new Error('No geocoding result');
        const { lat, lon } = result;
        map.setView([lat, lon], 15);
        window.L.marker([lat, lon]).addTo(map)
          .bindPopup(`<strong>${businessName}</strong><br>${address}`).openPopup();
      })
      .catch(() => map.setView([39.5, -98.35], 4)); // graceful fallback, never a broken blank map
  }, [address, businessName]);

  return <div ref={containerRef} className="h-[400px] w-full rounded-sm" />;
};
```

Nominatim's usage policy caps requests at roughly one per second and asks for an identifying
`User-Agent` — fine for a single page load, but don't fire it in a loop (e.g., once per item in
a multi-location list) without a delay between calls.

## 22. Compact Tile

The mobile counterpart to a photo/icon grid (Services, Team) per `06-ui-ux-accessibility.md`
§8 — a single-column list of dense rows instead of a multi-column grid of cards. Media sits on
the left, text on the right, everything vertically centered in one line or two.

```jsx
const CompactTile = ({ media, title, meta, action, onClick, gap = 'gap-4' }) => (
  <div onClick={onClick}
       className={`flex items-center ${gap} rounded-2xl bg-surface p-4 shadow-soft
                  ${onClick ? 'cursor-pointer transition-shadow duration-300 hover:shadow-lift' : ''}`}>
    {media}
    <div className="min-w-0 flex-1">
      <h3 className="truncate font-display text-base text-ink">{title}</h3>
      {meta && <p className="mt-0.5 truncate text-sm text-muted">{meta}</p>}
    </div>
    {action}
  </div>
);
```

Two usages, deliberately sized differently — an icon tile is denser than a photo tile, which
needs more breathing room to read as a person rather than a thumbnail:

```jsx
// Services — small icon media, a compact "Book" button as the action, tighter list spacing.
<ul className="space-y-2 md:hidden">
  {services.map((s) => (
    <li key={s.id}>
      <CompactTile
        gap="gap-3"
        media={<div className="grid h-10 w-10 shrink-0 place-items-center rounded-xl bg-accent/10 text-accent">{s.icon}</div>}
        title={s.title}
        meta={`${s.duration} · ${s.price}`}
        action={<Button href="#book" onClick={() => onSelectService(s.id)} className="!min-h-0 !px-4 !py-2 text-xs">Book</Button>}
      />
    </li>
  ))}
</ul>

// Team — larger photo media, no separate action button (the whole tile opens the profile modal).
<ul className="space-y-3 md:hidden">
  {team.map((member) => (
    <li key={member.id}>
      <CompactTile
        onClick={() => onSelectMember(member)}
        media={<img src={member.photo} alt={member.name} className="h-16 w-16 shrink-0 rounded-xl object-cover" />}
        title={member.name}
        meta={member.role}
      />
    </li>
  ))}
</ul>
```

## 23. Social Links

**Icon-only, never a text link list** ("Facebook · Instagram · LinkedIn" reads as a checklist,
not a design decision). One row of icon-only circular buttons, `aria-label` carrying the text
the icon can't — the same discipline as any other icon-only button per
`06-ui-ux-accessibility.md` §4 and the Iconography rules in `01-design-system.md`.

```jsx
const SocialLinks = ({ links, className = '' }) => (
  <ul className={`flex items-center gap-3 ${className}`}>
    {links.map((l) => (
      <li key={l.label}>
        <a href={l.href} aria-label={l.label} target="_blank" rel="noopener noreferrer"
           className="grid h-11 w-11 place-items-center rounded-full border border-line
                      text-muted transition-colors duration-300 hover:border-ink/30
                      hover:text-ink focus-visible:outline-none focus-visible:ring-2
                      focus-visible:ring-accent">
          {l.icon}
        </a>
      </li>
    ))}
  </ul>
);
```

Swap in real brand marks from each platform's press kit for production — the four below are
simplified generic renditions, placeholders for the pattern rather than exact logos:

```jsx
const XIcon = (p) => (
  <svg viewBox="0 0 24 24" {...p} fill="none" stroke="currentColor" strokeWidth="1.75">
    <path d="M5 5l14 14M19 5L5 19" strokeLinecap="round" />
  </svg>
);
const FacebookIcon = (p) => (
  <svg viewBox="0 0 24 24" {...p} fill="none" stroke="currentColor" strokeWidth="1.75">
    <path d="M14 9h2V6h-2c-1.66 0-3 1.34-3 3v2H9v3h2v6h3v-6h2.2l.8-3H14V9z" strokeLinejoin="round" />
  </svg>
);
const InstagramIcon = (p) => (
  <svg viewBox="0 0 24 24" {...p} fill="none" stroke="currentColor" strokeWidth="1.75">
    <rect x="4" y="4" width="16" height="16" rx="5" />
    <circle cx="12" cy="12" r="3.5" />
    <circle cx="16.5" cy="7.5" r="0.75" fill="currentColor" stroke="none" />
  </svg>
);
const LinkedInIcon = (p) => (
  <svg viewBox="0 0 24 24" {...p} fill="none" stroke="currentColor" strokeWidth="1.75">
    <rect x="4" y="4" width="16" height="16" rx="3" />
    <line x1="8" y1="10.5" x2="8" y2="16" strokeLinecap="round" />
    <circle cx="8" cy="7.5" r="0.75" fill="currentColor" stroke="none" />
    <path d="M12 16v-3.2c0-1.3.9-2.3 2.2-2.3s1.8 1 1.8 2.3V16" strokeLinecap="round" strokeLinejoin="round" />
  </svg>
);

// <SocialLinks links={[
//   { label: 'X (Twitter)', href: 'https://x.com/…',         icon: <XIcon className="h-4 w-4" /> },
//   { label: 'Facebook',    href: 'https://facebook.com/…',  icon: <FacebookIcon className="h-4 w-4" /> },
//   { label: 'Instagram',   href: 'https://instagram.com/…', icon: <InstagramIcon className="h-4 w-4" /> },
//   { label: 'LinkedIn',    href: 'https://linkedin.com/…',  icon: <LinkedInIcon className="h-4 w-4" /> },
// ]} />
```

## 24. Directional Line Reveal (Section Headings)

The line-mask heading entrance from `04-motion.md` §11. Each line slides up from behind its own
clipped edge, staggered; the entry direction follows the reader's approach. Reveals **once** and
unobserves — direction picks the side it enters from, it never re-hides anything.

```javascript
// Approach direction comes from the observer entry's own boundingClientRect, which arrives
// free with the callback — no getBoundingClientRect() per frame, so no layout thrash.
const useDirectionalReveal = ({ threshold = 0.25, rootMargin = '0px 0px -10% 0px' } = {}) => {
  const ref = useRef(null);
  const [state, setState] = useState({ shown: false, from: 'below' });

  useEffect(() => {
    const el = ref.current;
    if (!el) return;
    if (!('IntersectionObserver' in window)) { setState({ shown: true, from: 'below' }); return; }

    const obs = new IntersectionObserver((entries) => {
      const entry = entries[entries.length - 1];
      const from = entry.boundingClientRect.top < 0 ? 'above' : 'below';
      if (entry.intersectionRatio >= threshold) {
        setState({ shown: true, from });
        obs.unobserve(el);                      // once. never again.
      } else {
        // Still hidden: keep the start position in sync with where it's being approached from.
        setState((s) => (s.shown || s.from === from ? s : { shown: false, from }));
      }
    }, { threshold: [0, threshold], rootMargin });

    obs.observe(el);
    return () => obs.disconnect();
  }, [threshold, rootMargin]);

  return [ref, state.shown, state.from];
};
```

```jsx
const LineReveal = ({
  lines, as: Tag = 'h2', className = '', delay = 0, stagger = 100, duration = 900,
}) => {
  const [ref, shown, from] = useDirectionalReveal();
  const reduced = usePrefersReducedMotion();
  const visible = shown || reduced;
  const hiddenY = from === 'above' ? '-100%' : '100%';

  return (
    <Tag ref={ref} className={className}>
      {lines.map((line, i) => (
        // Mask: pb/-mb pair keeps the clip below the descender line without shifting layout.
        <span key={i} className="block overflow-hidden pb-[0.12em] -mb-[0.12em]">
          <span
            className="block will-change-transform"
            style={{
              transform: visible ? 'translate3d(0,0,0)' : `translate3d(0, ${hiddenY}, 0)`,
              // No transition while hidden, so re-seating the start position is invisible.
              transition: visible && !reduced
                ? `transform ${duration}ms cubic-bezier(0.16, 1, 0.3, 1) ${delay + i * stagger}ms`
                : 'none',
            }}
          >
            {line}
          </span>
        </span>
      ))}
    </Tag>
  );
};
```

Each entry in `lines` may be a string or JSX, so the display-italic emphasis word from
`03-typography.md` §5 still works:

```jsx
<Overline>02 — Sessions</Overline>
<LineReveal
  className="mt-6 font-display text-display-md"
  delay={120}
  lines={['Three ways', <React.Fragment>into <em className="italic">stillness</em></React.Fragment>]}
/>
```

Pair it with the `Overline` above the heading (a plain `<Reveal>` is right there — an overline
is one short line and doesn't need masking), and give the heading a delay slightly longer than
the overline's so the eye travels downward through the group.

**Reach for `<Reveal>` (§4), not this, for body copy, cards, and grids.** The line mask is a
heading treatment; applying it to paragraphs produces a wall of independently sliding lines that
reads as noise. One or two masked headings per viewport is the ceiling.

## 25. Chat Widget

A floating support/demo-assistant button, bottom-right on desktop — but a fixed-width panel
anchored to `right-6` overflows off the left edge of a narrow phone the moment its width
exceeds the visible space to that edge (a 384px-wide panel anchored 24px from the right edge of
a 375px-wide viewport needs 33px more room than the screen has). **On mobile the panel becomes
a centered, viewport-clamped overlay instead of an edge-anchored fixed-width one** — the same
category of fix as the Contact block's dual layout in `06-ui-ux-accessibility.md` §8.

```jsx
const ChatWidget = ({ title = 'Assistant', initialMessage, getReply }) => {
  const [open, setOpen] = useState(false);
  const [messages, setMessages] = useState([{ sender: 'agent', text: initialMessage }]);
  const [input, setInput] = useState('');

  const send = (e) => {
    e.preventDefault();
    const text = input.trim();
    if (!text) return;
    setMessages((m) => [...m, { sender: 'user', text }]);
    setInput('');
    setTimeout(() => setMessages((m) => [...m, { sender: 'agent', text: getReply(text) }]), 700);
  };

  return (
    <div className="fixed bottom-6 right-6 z-[90] sm:right-6">
      {!open && (
        <button onClick={() => setOpen(true)} aria-label={'Open ' + title}
                className="grid h-14 w-14 place-items-center rounded-full bg-accent
                           text-accent-ink shadow-lift transition-transform duration-300
                           hover:scale-105">
          <svg viewBox="0 0 24 24" className="h-6 w-6" fill="none" stroke="currentColor" strokeWidth="1.75">
            <path d="M21 12c0 4.418-4.03 8-9 8a9.86 9.86 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z" strokeLinecap="round" strokeLinejoin="round" />
          </svg>
        </button>
      )}

      {open && (
        // Mobile: `inset-x-4` alone centers AND clamps the width (left:16px, right:16px with
        // no explicit width computes the width as viewport − both insets) — no separate
        // width/transform needed, and none should be added alongside it. Desktop cancels the
        // insets and reverts to a fixed-width panel anchored bottom-right.
        <div className="fixed inset-x-4 bottom-6 h-[70vh] max-h-[480px]
                        sm:inset-x-auto sm:left-auto sm:right-6 sm:bottom-6 sm:h-[450px] sm:w-96
                        flex flex-col overflow-hidden rounded-3xl border border-line bg-surface
                        shadow-lift">
          <div className="flex items-center justify-between bg-accent p-4 text-accent-ink">
            <div className="flex items-center gap-3">
              <span className="h-2.5 w-2.5 rounded-full bg-emerald-400" aria-hidden="true" />
              <h4 className="font-display text-sm font-semibold">{title}</h4>
            </div>
            <button onClick={() => setOpen(false)} aria-label="Close chat"
                    className="grid h-8 w-8 place-items-center rounded-full text-accent-ink/80 hover:text-accent-ink">
              <svg viewBox="0 0 24 24" className="h-4 w-4" fill="none" stroke="currentColor" strokeWidth="1.75">
                <path d="M6 6l12 12M18 6L6 18" strokeLinecap="round" />
              </svg>
            </button>
          </div>

          <div className="flex-1 space-y-3 overflow-y-auto bg-ground/50 p-4">
            {messages.map((m, i) => (
              <div key={i} className={'flex ' + (m.sender === 'user' ? 'justify-end' : 'justify-start')}>
                <div className={'max-w-[80%] rounded-2xl p-3 text-xs leading-relaxed ' +
                  (m.sender === 'user' ? 'bg-accent text-accent-ink' : 'border border-line bg-surface text-ink')}>
                  {m.text}
                </div>
              </div>
            ))}
          </div>

          <form onSubmit={send} className="flex gap-2 border-t border-line bg-surface p-3">
            <input value={input} onChange={(e) => setInput(e.target.value)}
                   placeholder="Type a message…"
                   className="min-h-[44px] flex-1 rounded-xl bg-ground px-3 text-xs
                              focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-accent" />
            <button type="submit" aria-label="Send"
                    className="grid h-11 w-11 shrink-0 place-items-center rounded-xl
                               bg-accent text-accent-ink">
              <svg viewBox="0 0 24 24" className="h-4 w-4" fill="none" stroke="currentColor" strokeWidth="1.75">
                <path d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8" strokeLinecap="round" strokeLinejoin="round" />
              </svg>
            </button>
          </form>
        </div>
      )}
    </div>
  );
};
```

The panel is its own independently `fixed` element on every breakpoint, not nested inside the
toggle button's positioning — `fixed inset-x-4 bottom-6` centers and clamps it to the viewport
on mobile with no separate width or transform needed, and `sm:inset-x-auto sm:left-auto
sm:right-6` cancels those insets at `sm` and up so it reverts to a normal fixed-width panel
anchored to the same corner the button sits in. Resist adding a width or a centering transform
alongside `inset-x-4` — setting `left` and `right` on a fixed element with no explicit width
already computes that width as the space between them, and adding a second sizing mechanism on
top is exactly the contradictory-CSS mistake that produces a panel with an ambiguous, cascade-
order-dependent width instead of a reliably viewport-clamped one. This is a **front-end demo
only** — `getReply` should return canned, keyword-matched responses (per
`05-copywriting-cro.md`'s no-fabricated-backend principle), never a real API call from a static
single-file build.

## 26. Detail Modal

One content shape shared by every "tap for more" popup — a service, a team member, a news
article. This is what makes a Team Member popup and a Service popup genuinely the **same**
modal layout rather than just visually similar ones built twice: both render through this one
component, on top of `Modal` (§16).

```jsx
const DetailModal = ({ open, onClose, image, eyebrow, title, body, meta, actionLabel, onAction }) => (
  <Modal open={open} onClose={onClose} labelledBy="detail-modal-title" className="p-0">
    {image && (
      <img src={image} alt="" className="h-56 w-full rounded-t-2xl object-cover sm:h-64" />
    )}
    <div className="p-6 sm:p-8">
      {eyebrow && <p className="text-overline uppercase text-accent">{eyebrow}</p>}
      <h3 id="detail-modal-title" className="mt-3 font-display text-display-sm text-ink">{title}</h3>
      {meta && <p className="mt-2 text-sm tabular-nums text-muted">{meta}</p>}
      <p className="mt-4 text-muted text-pretty">{body}</p>
      {actionLabel && (
        <Button as="button" onClick={onAction} className="mt-8 w-full">{actionLabel}</Button>
      )}
    </div>
  </Modal>
);
```

Maps directly onto the three "tap for detail" popups a typical build needs:

| Use | `eyebrow` | `title` | `meta` | `actionLabel` |
|---|---|---|---|---|
| Service detail | category/tag | service name | duration · price | "Book This Service" |
| Team member | role/title | name | availability | "Book with {first name}" |
| News article | date | headline | — | "Book Now" or omit |

A team member or news popup needing a longer body than fits on the compact card (per
`06-ui-ux-accessibility.md` §8's Services/Team row) is exactly what `body` is for — pass the
fuller bio or article excerpt here, not the short card copy.

## 27. Mount

React 18 root API. Mounting the wrong way (`ReactDOM.render`) is the most common silent
failure in this environment.

```jsx
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

---

## Environment Gotchas

- Script tag must be `<script type="text/babel">`, and Babel Standalone must load **after**
  React/ReactDOM.
- The Tailwind CDN and Babel Standalone are development tools. They are correct for a
  single-file deliverable; note in the handoff that a production build should compile both.
- `tailwind.config` must be assigned **before** any element using custom classes renders.
- Arbitrary values containing spaces need underscores: `shadow-[0_24px_60px_-20px_...]`.
- Do not use optional chaining inside JSX attribute strings in very old Babel builds — use
  explicit guards if a parse error appears.
- Preconnect to `fonts.gstatic.com` with `crossorigin` or the font swap will visibly flash.
- **Avoid an icon lookup object** (`const icons = { clock: <circle .../><polyline .../>, ... }`)
  — a plain object's values must each already be one valid JSX expression, so any icon needing
  more than one element (a clock's face *and* hands, a mail envelope's outline *and* flap)
  breaks with "Adjacent JSX elements must be wrapped in an enclosing tag" the moment someone
  adds one. Every icon in this framework (§23, `Arrow` in the reference template, etc.) is
  instead its own small component returning a complete `<svg>` — components can hold as many
  sibling elements as they need inside their own root, so this failure class can't occur. If a
  lookup-object *is* used anyway, wrap any multi-element entry in `<g>…</g>` or `<>…</>`.
- **Never put a literal special-character glyph — `×`, `→`, `←`, `✓`, `•`, a curly quote — directly
  in JSX text content for an icon, a close button, or an arrow.** These are the single most
  fragile bytes in any copy/paste, clipboard, or AI-regeneration pipeline: the moment the file
  passes through a step that reads or re-saves it under a different encoding than it was written
  in, every one of these multi-byte characters silently becomes a `?` or `�` (the Unicode
  replacement character) — a "Next: Staff →" button renders as "Next: Staff ?", a close button
  renders as a bare "?". `<meta charset="UTF-8">` in `<head>` only declares how the *browser*
  should interpret the bytes it receives; it does nothing to protect the bytes themselves during
  whatever generated, stored, or transmitted the file before the browser saw it.

  The fix is structural, not a font or encoding fix: **use inline SVG for every icon-like glyph**
  (as this framework already does everywhere — `Arrow`, `Modal`'s close ×, every icon in §23).
  An SVG `path`'s `d` attribute is pure ASCII numbers and letters; there is no non-ASCII byte in
  it to mangle, so it survives any pipeline that would corrupt a literal `×` or `→`. If plain text
  is truly unavoidable, use the numeric HTML entity (`&#215;` for ×, `&#8594;` for →) rather than
  the raw character — entities are ASCII too. Never the raw glyph itself.
