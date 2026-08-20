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

Disabled below 768px and under reduced-motion. `translate3d` promotes to its own layer.

```jsx
const ParallaxHero = ({ src, alt, children }) => {
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
    <section className="relative isolate flex min-h-[92vh] items-center overflow-hidden">
      <img
        src={src} alt={alt}
        className="absolute inset-0 -z-10 h-[125%] w-full object-cover"
        style={{ transform: `translate3d(0, ${offset}px, 0)`, willChange: 'transform' }}
      />
      {/* Required scrim — never rely on the photo for contrast. */}
      <div className="absolute inset-0 -z-10 bg-gradient-to-b
                      from-ink/70 via-ink/50 to-ink/75" />
      <div className="mx-auto w-full max-w-6xl px-6">{children}</div>
    </section>
  );
};
```

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

## 16. Mount

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
