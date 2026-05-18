# Motion Animations — Worked Examples

Production-ready snippets. Copy, adjust the className/Tailwind to match the host project, drop in.

## 1. Page-level scroll progress bar

`components/ScrollProgress.tsx`

```tsx
"use client"
import { motion, useScroll, useSpring } from "motion/react"

export default function ScrollProgress() {
  const { scrollYProgress } = useScroll()
  const scaleX = useSpring(scrollYProgress, {
    stiffness: 90,
    damping: 28,
    mass: 0.3,
  })

  return (
    <motion.div
      style={{ scaleX }}
      aria-hidden
      className="fixed top-0 inset-x-0 h-[2px] origin-left z-[60] bg-gradient-to-r from-blue-500 via-violet-500 to-pink-500"
    />
  )
}
```

Mount once in the root layout. The spring smooths trackpad jitter; without it the bar twitches.

## 2. Auto-hide Navbar (scroll direction)

```tsx
"use client"
import { motion, useScroll, useMotionValueEvent } from "motion/react"
import { useState } from "react"

export default function Navbar() {
  const { scrollY } = useScroll()
  const [hidden, setHidden] = useState(false)

  useMotionValueEvent(scrollY, "change", (latest) => {
    const previous = scrollY.getPrevious() ?? 0
    if (latest > previous && latest > 100) setHidden(true)
    else setHidden(false)
  })

  return (
    <motion.nav
      animate={{ y: hidden ? -100 : 0 }}
      transition={{ duration: 0.3, ease: "easeInOut" }}
      className="fixed top-0 inset-x-0 z-50 backdrop-blur bg-black/40 border-b border-white/10"
    >
      {/* nav contents */}
    </motion.nav>
  )
}
```

The `latest > 100` check prevents the bar hiding while still near the top, where it would feel jumpy.

## 3. Site-wide MotionConfig

`app/layout.tsx`

```tsx
import { MotionConfig } from "motion/react"

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <MotionConfig transition={{ duration: 0.4, ease: [0.22, 1, 0.36, 1] }}>
          {children}
        </MotionConfig>
      </body>
    </html>
  )
}
```

Note: `MotionConfig` requires a client boundary. Either mark `layout.tsx` `"use client"` or extract a `<MotionRoot>{children}</MotionRoot>` client component.

## 4. Staggered Projects grid (variants)

```tsx
"use client"
import { motion } from "motion/react"

const grid = {
  hidden: {},
  visible: { transition: { staggerChildren: 0.08, delayChildren: 0.1 } },
}
const card = {
  hidden: { opacity: 0, y: 24 },
  visible: { opacity: 1, y: 0, transition: { duration: 0.5 } },
}

export default function Projects({ projects }) {
  return (
    <motion.ul
      variants={grid}
      initial="hidden"
      whileInView="visible"
      viewport={{ once: true, amount: 0.2 }}
      className="grid gap-6 md:grid-cols-2"
    >
      {projects.map((p) => (
        <motion.li key={p.id} variants={card}>
          {/* card markup */}
        </motion.li>
      ))}
    </motion.ul>
  )
}
```

## 5. Per-case-study scroll progress (element-scoped)

```tsx
"use client"
import { motion, useScroll, useSpring } from "motion/react"
import { useRef } from "react"

export default function CaseStudyProgress() {
  const ref = useRef<HTMLElement>(null)
  const { scrollYProgress } = useScroll({
    target: ref,
    offset: ["start start", "end end"],
  })
  const scaleX = useSpring(scrollYProgress, { stiffness: 100, damping: 30 })

  return (
    <>
      <motion.div
        style={{ scaleX }}
        className="fixed top-[64px] inset-x-0 h-[2px] origin-left z-40 bg-amber-400"
      />
      <article ref={ref}>{/* case study content */}</article>
    </>
  )
}
```

`offset: ["start start", "end end"]` measures from "top of article meets top of viewport" to "bottom of article meets bottom of viewport".

## 6. Hero blur on scroll

```tsx
"use client"
import { motion, useScroll, useTransform } from "motion/react"

export default function Hero() {
  const { scrollYProgress } = useScroll()
  const filter = useTransform(scrollYProgress, [0, 0.2], ["blur(0px)", "blur(8px)"])
  const opacity = useTransform(scrollYProgress, [0, 0.2], [1, 0])

  return (
    <motion.section style={{ filter, opacity }} className="h-screen">
      {/* hero content */}
    </motion.section>
  )
}
```

Hero blurs and fades over the first 20% of page scroll, then is gone.

## 7. AnimatePresence — modal

```tsx
"use client"
import { AnimatePresence, motion } from "motion/react"

export function Modal({ open, onClose, children }) {
  return (
    <AnimatePresence mode="wait">
      {open && (
        <motion.div
          key="overlay"
          initial={{ opacity: 0 }}
          animate={{ opacity: 1 }}
          exit={{ opacity: 0 }}
          onClick={onClose}
          className="fixed inset-0 z-50 bg-black/60 backdrop-blur-sm grid place-items-center"
        >
          <motion.div
            initial={{ scale: 0.95, opacity: 0 }}
            animate={{ scale: 1, opacity: 1 }}
            exit={{ scale: 0.95, opacity: 0 }}
            onClick={(e) => e.stopPropagation()}
            className="rounded-xl bg-zinc-900 p-6 max-w-lg"
          >
            {children}
          </motion.div>
        </motion.div>
      )}
    </AnimatePresence>
  )
}
```

## 8. SVG path draw-on

```tsx
"use client"
import { motion } from "motion/react"

export function SignatureLine() {
  return (
    <motion.svg viewBox="0 0 200 40" className="w-48">
      <motion.path
        d="M 5 30 Q 50 5 100 25 T 195 20"
        fill="none"
        stroke="currentColor"
        strokeWidth={2}
        initial={{ pathLength: 0 }}
        whileInView={{ pathLength: 1 }}
        viewport={{ once: true }}
        transition={{ duration: 1.5, ease: "easeInOut" }}
      />
    </motion.svg>
  )
}
```

## 9. Horizontal pin-pan gallery

```tsx
"use client"
import { motion, useScroll, useTransform } from "motion/react"
import { useRef } from "react"

export default function PinPanGallery({ items }) {
  const ref = useRef<HTMLElement>(null)
  const { scrollYProgress } = useScroll({
    target: ref,
    offset: ["start start", "end end"],
  })
  const x = useTransform(scrollYProgress, [0, 1], ["0%", "-75%"])

  return (
    <section ref={ref} className="relative h-[300vh]">
      <div className="sticky top-0 h-screen overflow-hidden flex items-center">
        <motion.div style={{ x }} className="flex gap-6 pl-[10vw]">
          {items.map((item) => (
            <div key={item.id} className="shrink-0 w-[70vw] md:w-[40vw]">
              {/* card */}
            </div>
          ))}
        </motion.div>
      </div>
    </section>
  )
}
```

Wrapper is `300vh` tall → 300% of viewport scroll = 100% of the horizontal pan. Adjust ratio by changing wrapper height vs the `-75%` end.

## 10. Shared layout (tab underline)

```tsx
"use client"
import { motion } from "motion/react"
import { useState } from "react"

const tabs = ["About", "Work", "Notes"]

export default function Tabs() {
  const [active, setActive] = useState("Work")
  return (
    <nav className="flex gap-6">
      {tabs.map((tab) => (
        <button
          key={tab}
          onClick={() => setActive(tab)}
          className="relative pb-1 text-sm"
        >
          {tab}
          {active === tab && (
            <motion.span
              layoutId="tab-underline"
              transition={{ type: "spring", stiffness: 350, damping: 30 }}
              className="absolute inset-x-0 -bottom-0.5 h-0.5 bg-white"
            />
          )}
        </button>
      ))}
    </nav>
  )
}
```

`layoutId` glues the underline across renders — clicking a new tab slides it instead of cutting.

## 11. Reduced motion respect

```tsx
"use client"
import { motion, useReducedMotion } from "motion/react"

export function GentleReveal({ children }) {
  const reduce = useReducedMotion()
  return (
    <motion.div
      initial={reduce ? { opacity: 1 } : { opacity: 0, y: 20 }}
      whileInView={reduce ? { opacity: 1 } : { opacity: 1, y: 0 }}
      viewport={{ once: true }}
      transition={{ duration: 0.5 }}
    >
      {children}
    </motion.div>
  )
}
```

When the OS has "reduce motion" enabled, the element renders in its final state with no animation. Required for accessibility.

## 12. Vanilla JS — animate() for non-React sites

```html
<script type="module">
  import { animate, stagger, scroll } from "https://cdn.jsdelivr.net/npm/motion@11.13.5/+esm"

  // Stagger reveal on page load
  animate("li", { y: [20, 0], opacity: [0, 1] }, { delay: stagger(0.08) })

  // Scroll-driven rotation on a hero element
  scroll(animate(".hero-logo", { rotate: 360 }))
</script>
```

Use for WordPress, plain HTML pages, or any no-build context. Pin the version (`@11.13.5`) — never `@latest` in production.

## 13. Vue — motion-v equivalents

```vue
<script setup>
import { motion, useScroll } from 'motion-v'

const { scrollYProgress } = useScroll()
</script>

<template>
  <!-- Mount reveal -->
  <motion.div :initial="{ opacity: 0 }" :animate="{ opacity: 1 }" />

  <!-- Hover gesture -->
  <motion.button :whileHover="{ scale: 1.05 }" :whilePress="{ scale: 0.95 }" />

  <!-- Scroll progress -->
  <motion.div :style="{ scaleX: scrollYProgress, transformOrigin: '0%' }"
              class="fixed top-0 inset-x-0 h-0.5 bg-blue-500" />
</template>
```

Vue uses `:prop="value"` binding for animation values; otherwise the API mirrors React.
