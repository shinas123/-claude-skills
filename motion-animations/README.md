# motion-animations

A Claude Code skill for building production animations with the [Motion](https://motion.dev) library (formerly Framer Motion).

Covers all three Motion entry points:

- **`motion/react`** — React / Next.js components
- **`motion-v`** — Vue / Nuxt
- **`motion`** vanilla `animate()` — plain HTML / WordPress / no-build

## What this skill knows

- Scroll-driven animations (`useScroll`, `useTransform`, `useMotionValueEvent`)
- Viewport reveals (`whileInView` with `once: true`)
- Variants & staggered list orchestration
- Layout animations & shared-element transitions (`layoutId`)
- Gestures — hover, tap, focus, drag
- `AnimatePresence` for mount/unmount animations
- `MotionConfig` for site-wide transition defaults
- SVG path drawing, viewBox panning, attribute animation
- Reduced-motion accessibility
- Server Components compatibility via `motion/react-client`
- Performance rules — compositor-only transforms over layout props

## Install

Drop the folder into your Claude Code skills directory:

```bash
# macOS / Linux
git clone https://github.com/shinas123/motion-animations.git ~/.claude/skills/motion-animations

# Windows (PowerShell)
git clone https://github.com/shinas123/motion-animations.git $env:USERPROFILE\.claude\skills\motion-animations
```

Claude Code will pick it up on next launch.

## Use

In any Claude Code session:

```
/motion-animations
```

Or just mention Motion, scroll animations, `useScroll`, `whileInView`, `AnimatePresence`, etc. — the skill auto-activates based on its description triggers.

## Structure

```
motion-animations/
├── SKILL.md              # Frontmatter + procedure + validation + pitfalls
└── references/
    └── EXAMPLES.md       # 13 copy-paste production examples
```

`SKILL.md` is the main entry. `references/EXAMPLES.md` is loaded on demand and contains:

1. Page-level scroll progress bar
2. Auto-hide Navbar (scroll direction)
3. Site-wide `MotionConfig`
4. Staggered Projects grid (variants)
5. Per-case-study scroll progress
6. Hero blur on scroll
7. `AnimatePresence` modal
8. SVG path draw-on
9. Horizontal pin-pan gallery
10. Shared layout tab underline
11. Reduced-motion respect
12. Vanilla JS via CDN
13. Vue (`motion-v`) equivalents

## License

MIT. See [LICENSE](./LICENSE).

## Author

Built by [Shinas](https://github.com/shinas123) — AI Engineer at Wall Street Jr Investments, Dubai.

Contributions and issues welcome.
