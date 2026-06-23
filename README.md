# skills

Personal collection of [Claude](https://www.anthropic.com/claude-code) skills.
Each top-level directory is one skill: a `SKILL.md` (instructions + trigger
description) plus any `references/`, `scripts/`, or `assets/` it needs.

## Skills

| Skill | What it does |
|-------|--------------|
| [`quoteforge-cards`](quoteforge-cards/) | Turn text into polished images and multi-slide decks (PNG) with [QuoteForge](https://github.com/lordvins226/quoteforge) — quote cards, carousels, slides, blog/OG headers, article covers, banners. Installs the CLI if needed, authors the JSON, renders, and shows the result. |

## Installing

Claude Code discovers skills under `~/.claude/skills/`. Symlink a skill there so
edits in this repo are picked up live:

```bash
ln -s "$PWD/quoteforge-cards" ~/.claude/skills/quoteforge-cards
```

Verify the link resolves:

```bash
readlink ~/.claude/skills/quoteforge-cards
test -e ~/.claude/skills/quoteforge-cards/SKILL.md && echo ok
```

## Layout

```
<skill-name>/
├── SKILL.md          # required — frontmatter (name, description) + instructions
├── references/       # docs loaded on demand
├── assets/           # files used in output
└── evals/            # test prompts + assets for evaluating the skill
```

`*-workspace/` directories (eval run artifacts) are git-ignored.
