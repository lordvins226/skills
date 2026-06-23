---
name: quoteforge-cards
description: >-
  Turn text into polished images and multi-slide decks (PNG) with QuoteForge, a CLI
  that renders JSON content through themed templates. Use it whenever the user wants
  a clean text-forward graphic — a quote, statement, tip, stat, definition, title,
  or announcement — rendered as an image, NOT only for social media. Covers social
  posts and carousels (Twitter/X, LinkedIn, Instagram, Facebook, Threads, Stories)
  AND presentation/talk slides, blog or Open Graph header images, article cover
  images, banners (page / channel / event / group covers), README and docs hero
  visuals, newsletter and email graphics, release/announcement cards, and any
  "make this text into a shareable picture" or "render these points as slides"
  request — even if the user never says "social media" or names QuoteForge. Triggers
  on "make a quote card", "turn this into a graphic/image", "carousel deck", "slides
  from these bullet points", "header image for my post", "a cover image for my
  article", "a banner for my page/channel/event", "an image of this quote", "render
  these as PNGs", and similar. Also covers installing QuoteForge if it is not on the
  machine yet, and picking sizes, themes, and templates for the target medium.
---

# QuoteForge Cards

QuoteForge turns a small JSON file into a polished, text-forward image. You author
the content (a single **card** or a multi-slide **deck**), pick a size/theme, and
the CLI renders a PNG (or numbered PNGs + a ZIP for decks) using Puppeteer. The
output suits social posts and carousels, but equally presentation slides, blog /
Open Graph header images, README and docs visuals, and announcement graphics —
anywhere a clean image of some text is wanted.

Your job with this skill: get QuoteForge available, translate the user's request
into valid content JSON, render it, and show the result.

## Workflow

1. **Make sure `quoteforge` is available.** Run `quoteforge --version`. If that
   fails, install it (see "Installing" below). If you are inside the QuoteForge
   repo itself (a `package.json` whose name is `quoteforge`), use `bun quoteforge`
   instead of installing.
2. **Decide card vs deck.** One image → a **card**. Several related images the user
   will swipe through (a carousel, a thread, a step-by-step) → a **deck**. When
   unsure, a single card is the safer default; ask only if it genuinely matters.
3. **Author the JSON.** Write the content file (see "Authoring content" and the
   schema reference). Keep text tight — these are images, not documents. Choose a
   size, theme, and template that fit the platform and tone.
4. **Validate, then render.**
   - `quoteforge validate <file>` first — it catches schema mistakes fast, before
     you spend time launching a browser.
   - Card: `quoteforge generate <file> --output <path.png> --no-timestamp`
   - Deck: `quoteforge slides <file> --output <dir>`
5. **Show the result.** Read/display the PNG so the user (and you) can confirm it
   looks right. If something's off, fix the JSON and re-render — visual problems
   (overflow, alignment, contrast) only show up in the actual image.

`--output` for `generate` expects a **file path**, not a directory (it errors with
`EISDIR` otherwise). `slides --output` expects a directory.

## Installing

Try these in order; stop at the first that works for the environment.

- **Homebrew** (macOS / Linux): `brew install lordvins226/quoteforge/quoteforge`
- **Install script** (Linux / macOS): `curl -fsSL https://raw.githubusercontent.com/lordvins226/quoteforge/main/install.sh | sh`
  — installs to `~/.local/bin`; make sure that's on `PATH`.
- **From source** (when the repo is already checked out): `bun install` then run via
  `bun quoteforge <cmd>`.

QuoteForge renders with Chrome/Chromium. On first run it uses a system install
(Chrome, Chromium, Edge) if present, otherwise downloads a pinned Chrome for Testing
(~170 MB) to `~/.cache/quoteforge/chrome/`. Set `QUOTEFORGE_CHROME=/path/to/chrome`
to point at a specific binary. The first render may be slow because of this.

## Authoring content

Content auto-detects type: a root `"type": "deck"` → deck; otherwise → single card.

**Minimal card:**
```json
{
  "template": "quote",
  "theme": "dark-teal",
  "size": "twitter",
  "blocks": [
    { "type": "blockquote", "parts": [{ "text": "Make it work, then make it right.", "style": "normal" }] }
  ]
}
```

**Minimal deck** (each slide is its own image):
```json
{
  "type": "deck",
  "defaults": { "template": "quote", "theme": "dark-teal", "size": "instagram-sq" },
  "slides": [
    { "id": "s1", "blocks": [{ "type": "headline", "parts": [{ "text": "3 lessons", "style": "bold" }] }] },
    { "id": "s2", "blocks": [{ "type": "headline", "parts": [{ "text": "Lesson 1", "style": "accent" }] }] }
  ]
}
```

Blocks stack vertically in order. The full block catalog (headline, blockquote, text,
bullet-list, callout, divider, spacer, image), every size, the theme list, and the
templates are in **`references/content-schema.md`** — read it before authoring
anything non-trivial, so the JSON is right the first time.

## Picking size / theme / template

- **Size** drives the medium. Social: `twitter` (16:9), `linkedin`, `instagram-sq`
  (1:1), `instagram-port` (4:5), `story` (9:16), `facebook-square`. For a deck/
  carousel, prefer a **square** size so every slide is consistent — QuoteForge warns
  on non-square deck sizes. For non-social uses: a blog / Open Graph header or article
  cover is effectively `facebook-post` (1200×630) or `twitter` (1200×675); wide
  banners / page covers fit `facebook-cover` (1640×624), `facebook-group-cover`
  (1640×856), or `facebook-event` (1920×1080); presentation slides fit `twitter` /
  `facebook-event` (16:9); and `custom` takes arbitrary dimensions when nothing preset
  matches (e.g. an exact banner size a platform requires).
- **Theme** sets the palette and fonts (e.g. `dark-teal`, `noir-crimson`,
  `paper-cream`, `terminal-green`, `light-minimal`). Match it to the mood.
- **Template** sets the layout: `quote` (centered statement), `manifesto` (dense
  multi-block), `list` (bulleted), `minimal` (sparse). Default to `quote` for a
  single quote/statement.

When the user doesn't specify, choose sensible defaults, render, and let the image
itself confirm the choice — don't over-ask.

## Common commands

```bash
quoteforge generate card.json --output out.png --no-timestamp   # one card → PNG
quoteforge generate card.json --size linkedin --theme dark-orange
quoteforge slides deck.json --output ./out                      # deck → PNGs + ZIP
quoteforge slides deck.json --slide 2                           # render only slide 2
quoteforge validate card.json                                   # schema check
quoteforge themes list                                          # see themes + swatches
```

If the user wants to hand-tweak interactively rather than have you author JSON, point
them to `quoteforge studio` (a local WYSIWYG editor) or `quoteforge new` (an
interactive prompt-driven creator) — but for agent-driven generation, authoring JSON
and running `generate`/`slides` is the path.
