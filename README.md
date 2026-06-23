# Skills

> **Note:** This repository is a personal collection of skills for Claude. For
> information about the Agent Skills standard, see [agentskills.io](http://agentskills.io).

# Skills

Skills are folders of instructions, scripts, and resources that Claude loads
dynamically to improve performance on specialized tasks. They teach Claude how
to accomplish particular objectives consistently — here, turning text into
rendered graphics with a CLI.

# Skill Sets

- [./quoteforge-cards](./quoteforge-cards): Turn text into polished images and multi-slide decks (PNG) with [QuoteForge](https://github.com/lordvins226/quoteforge) — quote cards, carousels, slides, blog/OG headers, article covers, and banners. Installs the CLI if needed, authors the JSON, renders, and shows the result.

# Try in Claude Code

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

# Creating a Basic Skill

Skills require minimal setup — just a folder containing a `SKILL.md` file with
YAML frontmatter and instructions:

```markdown
---
name: my-skill-name
description: A clear description of what this skill does and when to use it
---

# My Skill Name

[Add your instructions here that Claude will follow when this skill is active]

## Examples
- Example usage 1
- Example usage 2
```

The frontmatter requires only two fields:
- `name` - A unique identifier for your skill (lowercase, hyphens for spaces)
- `description` - A complete description of what the skill does and when to use it

Optional bundled resources live alongside `SKILL.md`: `references/` (docs loaded
on demand), `scripts/` (executable helpers), `assets/` (files used in output),
and `evals/` (test prompts). Eval run artifacts under `*-workspace/` are
git-ignored.
