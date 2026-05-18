# claude-skills

Custom skills for [Claude Code](https://claude.com/claude-code) — domain knowledge and procedures Claude reaches for automatically when the work matches.

## Skills in this repo

| Skill | What it does |
|---|---|
| [`motion-animations`](./motion-animations) | Build production animations with the Motion library (React / Vue / vanilla). Scroll-driven, viewport reveals, variants, layout transitions, gestures, SVG, accessibility. |

More skills will land here as I build them.

## Install

Clone the whole repo into your Claude Code skills directory, or symlink individual skills.

**Whole repo** (gets every skill in here, including future ones):

```bash
# macOS / Linux
git clone https://github.com/shinas123/-claude-skills.git ~/.claude/skills/shinas

# Windows (PowerShell)
git clone https://github.com/shinas123/-claude-skills.git $env:USERPROFILE\.claude\skills\shinas
```

That puts each skill at `~/.claude/skills/shinas/<skill-name>/`. Claude Code discovers them on next launch.

**One skill only:**

```bash
# Clone the repo somewhere, then copy or symlink the skill you want
git clone https://github.com/shinas123/-claude-skills.git /tmp/skills
cp -r /tmp/skills/motion-animations ~/.claude/skills/
```

## Use

In any Claude Code session, invoke a skill directly:

```
/motion-animations
```

Or mention a trigger from the skill's description (e.g. for `motion-animations`: "Motion", "scroll progress bar", "useScroll", "whileInView", "AnimatePresence", "polish the site with animations") and Claude will auto-activate it.

## Skill structure

Each skill is a folder containing at minimum a `SKILL.md`:

```
<skill-name>/
├── SKILL.md              # Frontmatter + procedure + validation + pitfalls
├── LICENSE
├── README.md             # Public-facing description
└── references/           # On-demand reference material
    └── EXAMPLES.md
```

`SKILL.md` frontmatter format (Claude Code reads `description` during skill discovery):

```yaml
---
name: skill-name
description: >
  One paragraph describing what the skill does and when Claude should
  activate it. Includes trigger keywords inline.
license: MIT
allowed-tools: Read Write Edit Bash Grep Glob
metadata:
  author: Your Name
  version: "1.0"
  domain: frontend
  complexity: intermediate
  language: TypeScript
  tags: tag1, tag2, tag3
---
```

## Contributing

Issues and PRs welcome. If you've got a Motion pattern, a tooling skill, or a recipe that's saved you time, drop it in.

## License

MIT — see individual skills' `LICENSE` files.

## Author

[Shinas](https://github.com/shinas123) — AI Engineer at Wall Street Jr Investments, Dubai.
