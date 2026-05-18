---
name: motion-animations
description: >
  Build production animations with the Motion library (formerly Framer Motion) —
  React (motion/react), Vue (motion-v), or vanilla JS (animate() from motion).
  Use when a React/Next.js/Vue project needs scroll-driven animations, viewport
  reveals, layout transitions, drag gestures, shared-element transitions, SVG
  path drawing, or staggered list animations. Activate when the user mentions
  Motion, Framer Motion, "scroll progress bar", "fade in on scroll",
  "AnimatePresence", "useScroll", "whileInView", or asks to polish a site with
  animations.
license: MIT
allowed-tools: Read Write Edit Bash Grep Glob
metadata:
  author: Shinas
  version: "1.0"
  domain: frontend
  complexity: intermediate
  language: TypeScript
  tags: motion, framer-motion, animation, react, scroll, frontend
---

# Build Motion Animations

Ship production-quality animations using the Motion library. Covers `motion/react` (React), `motion-v` (Vue), and the vanilla `animate()` function from `motion`. All three share the same package on npm (`motion`) except Vue (`motion-v`).

## When to Use

- A React/Next.js project needs scroll-driven animations, viewport reveals, drag gestures, or layout transitions
- The user wants a "scroll progress bar", "fade in on scroll", staggered list reveals, or shared-element transitions
- An existing site needs to be polished with animation — hero, navbar, section reveals, case-study pages
- The user mentions Motion, Framer Motion, `useScroll`, `whileInView`, `AnimatePresence`, `MotionConfig`
- A SVG needs `pathLength` draw-on, viewBox panning, or animated attributes
- Vanilla JS / WordPress / no-build environments need imperative `animate()` calls

## Inputs

- **Required**: target framework (React / Vue / vanilla) and the file(s) to add animations to
- **Required**: what should animate, and on what trigger (mount, scroll position, viewport entry, hover, drag)
- **Optional**: existing transition/ease preferences (default: cubic-bezier `[0.22, 1, 0.36, 1]`)
- **Optional**: reduced-motion behaviour (default: respect `useReducedMotion()`)

## Procedure

### Step 1: Confirm Motion is installed

For React/Next.js or vanilla JS:

```bash
npm ls motion
```

For Vue/Nuxt:

```bash
npm ls motion-v
```

**Expected:** package present at v11+. If missing: `npm install motion` (or `motion-v` for Vue).

**On failure:** Confirm `package.json` has the dep before editing component files — adding `motion/react` imports without the package will break the build.

### Step 2: Pick the right entry point for the framework

| Framework | Import | Notes |
|---|---|---|
| React (client component) | `import { motion } from "motion/react"` | Needs `"use client"` in Next.js App Router |
| React (server component) | `import * as motion from "motion/react-client"` | JSX-only; hooks still require client |
| Vue | `import { motion } from "motion-v"` | Use `:animate="{ … }"` (Vue prop binding) |
| Vanilla JS / HTML | `import { animate, scroll, stagger } from "motion"` | Imperative `animate(el, {…}, {…})` |

### Step 3: Apply the right pattern for the trigger

**One-shot mount animation:**
```tsx
<motion.div initial={{ opacity: 0, y: 20 }} animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.5 }} />
```

**Viewport reveal (most section animations):**
```tsx
<motion.div
  initial={{ opacity: 0, y: 30 }}
  whileInView={{ opacity: 1, y: 0 }}
  viewport={{ once: true, amount: 0.3 }}
  transition={{ duration: 0.6 }}
/>
```
Always pass `once: true` for content reveals — without it, animations re-fire on every viewport entry and feel glitchy.

**Page scroll progress bar:**
```tsx
"use client"
import { motion, useScroll, useSpring } from "motion/react"

export function ScrollProgress() {
  const { scrollYProgress } = useScroll()
  const scaleX = useSpring(scrollYProgress, { stiffness: 90, damping: 28, mass: 0.3 })
  return <motion.div style={{ scaleX, transformOrigin: "0%" }}
    className="fixed top-0 inset-x-0 h-[2px] bg-gradient-to-r from-blue-500 to-pink-500 z-50" />
}
```
Wrap `scrollYProgress` in `useSpring` — raw value is jittery on trackpads.

**Element-scoped scroll progress (per case study / per section):**
```tsx
const ref = useRef(null)
const { scrollYProgress } = useScroll({
  target: ref,
  offset: ["start end", "end start"]   // enters bottom → leaves top
})
```

**Scroll direction (auto-hide navbar):**
```tsx
const { scrollY } = useScroll()
const [hidden, setHidden] = useState(false)
useMotionValueEvent(scrollY, "change", (latest) => {
  const previous = scrollY.getPrevious() ?? 0
  setHidden(latest > previous && latest > 100)
})
return <motion.nav animate={{ y: hidden ? -100 : 0 }} transition={{ duration: 0.3 }} />
```

**Staggered list with variants:**
```tsx
const list = { hidden: {}, visible: { transition: { staggerChildren: 0.08 } } }
const item = { hidden: { opacity: 0, y: 20 }, visible: { opacity: 1, y: 0 } }

<motion.ul variants={list} initial="hidden" whileInView="visible" viewport={{ once: true }}>
  {items.map(i => <motion.li key={i.id} variants={item}>{i.name}</motion.li>)}
</motion.ul>
```

**Shared-element transition (e.g. nav highlight, card → detail):**
```tsx
{tabs.map(tab => (
  <button onClick={() => setActive(tab.id)}>
    {tab.label}
    {active === tab.id && <motion.div layoutId="underline" className="..." />}
  </button>
))}
```

**Exit animation:**
```tsx
<AnimatePresence mode="wait">
  {open && <motion.div key="modal"
    initial={{ opacity: 0, scale: 0.95 }}
    animate={{ opacity: 1, scale: 1 }}
    exit={{ opacity: 0, scale: 0.95 }} />}
</AnimatePresence>
```

**Site-wide ease (set once in layout):**
```tsx
<MotionConfig transition={{ duration: 0.4, ease: [0.22, 1, 0.36, 1] }}>
  {children}
</MotionConfig>
```

**SVG path draw-on:**
```tsx
<motion.path d={d} initial={{ pathLength: 0 }}
  whileInView={{ pathLength: 1 }} viewport={{ once: true }}
  transition={{ duration: 1.5, ease: "easeInOut" }} />
```

**Horizontal sticky pin-pan (gallery section):**
```tsx
const ref = useRef(null)
const { scrollYProgress } = useScroll({ target: ref, offset: ["start start", "end end"] })
const x = useTransform(scrollYProgress, [0, 1], ["0%", "-75%"])

<section ref={ref} className="relative h-[300vh]">
  <div className="sticky top-0 h-screen overflow-hidden flex items-center">
    <motion.div style={{ x }} className="flex gap-5">{cards}</motion.div>
  </div>
</section>
```

### Step 4: Honour reduced-motion

```tsx
const prefersReduced = useReducedMotion()
<motion.div animate={prefersReduced ? {} : { y: 0, opacity: 1 }} />
```

### Step 5: Validate

Run `npm run dev` and confirm in the browser:
- Animation triggers on the intended event
- No layout shift / FOUC on first paint (use `initial` to set the pre-animation state)
- No console errors about hydration mismatch (often caused by missing `"use client"`)
- Reduced-motion OS setting is respected

**Expected:** Animation runs smoothly at 60fps. Inspect via Chrome DevTools → Performance tab → look for missed frames.

**On failure:**
- Janky animation → check whether you're animating layout-triggering props (`width`, `height`, `top`) instead of transforms
- Hydration mismatch → add `"use client"` to the file, or use `motion/react-client` for SSR-friendly JSX
- Animation fires too often → add `viewport={{ once: true }}`

## Validation

- [ ] `npm ls motion` (or `motion-v`) returns a v11+ version
- [ ] No `framer-motion` imports remain (deprecated package)
- [ ] All scroll/viewport hooks live in `"use client"` files (Next.js App Router)
- [ ] `initial` props match the pre-animation visual state (no FOUC on mount)
- [ ] Repeating animations use `viewport={{ once: true }}` unless intentionally re-firing
- [ ] Animation honours `useReducedMotion()` where appropriate
- [ ] Transforms used over layout props for performance

## Common Pitfalls

- **`framer-motion` vs `motion`**: v11 renamed the package. Old imports `from "framer-motion"` still work but are legacy. New code uses `motion/react`.
- **Hooks in Server Components**: `useScroll`, `useTransform`, `useMotionValue` need `"use client"`. `motion/react-client` only fixes JSX, not hooks.
- **Animating `width`/`height`**: triggers layout/paint. Prefer `scaleX`/`scaleY` with `transform-origin`.
- **`whileInView` without `once: true`**: animation re-fires every scroll, feels glitchy.
- **Missing `initial`**: element starts in its final state and "animates" invisibly. Always set `initial` to the pre-state.
- **`@latest` CDN imports** in production: pin a version (`motion@11.13.5/+esm`) to avoid silent supply-chain changes.
- **Motion AI Kit install scripts** (`curl … | bash`, npx-from-URL): supply-chain risk — install only via official documented channels and pin versions.
- **`useSpring` skipped on `scrollYProgress`**: raw scroll values are noisy on trackpads. Always smooth with `useSpring` for progress bars.

## Examples

See `references/EXAMPLES.md` for: full ScrollProgress component, auto-hide Navbar, Hero starfield with scroll-driven blur, staggered Projects grid, per-case-study scroll progress, and pin-pan gallery.

## Related Skills

- `frontend-design` — broader UI design system context; Motion is the animation layer on top
- `claude-code-video-toolkit--frontend-design` — same domain
- `nextjs-developer` — App Router + Server Components context for `motion/react-client` decisions
- `react-expert` — hook patterns underpinning Motion's hook API
- `vue-expert` / `vue-expert-js` — when using `motion-v` instead

<!-- Keep under 500 lines. Examples live in references/EXAMPLES.md -->
