# QuoteForge content schema reference

Everything needed to author valid card and deck JSON. The CLI validates with Zod, so
field names and enums must match exactly. Run `quoteforge validate <file>` to confirm.

## Contents
- [Card](#card)
- [Deck](#deck)
- [Block types](#block-types)
- [Part styles](#part-styles)
- [Sizes](#sizes)
- [Themes](#themes)
- [Templates](#templates)

## Card

A single image. No `type`, or `"type": "card"`.

```json
{
  "type": "card",
  "template": "quote",
  "theme": "dark-teal",
  "size": "twitter",
  "meta": { "title": "optional", "tags": ["optional"] },
  "blocks": [ /* one or more blocks, see below */ ]
}
```

`template`, `theme`, `size`, and a non-empty `blocks` array are required. `meta` is
optional.

## Deck

Multiple slides, each rendered to its own numbered PNG (plus a ZIP). Requires
`"type": "deck"`.

```json
{
  "type": "deck",
  "meta": { "title": "Optional deck title" },
  "defaults": { "template": "quote", "theme": "dark-teal", "size": "instagram-sq", "showCounter": true },
  "slides": [
    { "id": "s1", "blocks": [ /* ... */ ] },
    { "id": "s2", "theme": "noir-crimson", "blocks": [ /* ... */ ] }
  ]
}
```

- `defaults` (required): `template`, `theme`, `size`; optional `showCounter`, `counter`.
- Each slide needs `id` and a non-empty `blocks`. A slide may override `template`,
  `theme`, `size`, `showCounter`, `counter`, and set an optional `label`.
- **Use a square size** for decks (carousels) so slides are visually consistent; the
  CLI warns on non-square deck sizes.
- `counter` config: `{ "format": "{current} / {total}", "position": "bottom-right", "style": "pill" }`.
  `position` ∈ `bottom-right | bottom-left | bottom-center | top-right`; `style` ∈
  `pill | plain | dots`.

## Block types

Blocks stack vertically in order. Note the shapes are **not** uniform — some use
`parts`, some `content`, some `items`.

### headline — large display text
```json
{ "type": "headline", "parts": [
  { "text": "Ship ", "style": "normal" },
  { "text": "small", "style": "accent" },
  { "text": ".", "style": "normal" }
] }
```

### blockquote — bordered pull quote
```json
{ "type": "blockquote", "parts": [{ "text": "Short quote.", "style": "italic" }] }
```
Capped at 28px by base CSS — for a hero-size quote, use a `headline` instead.

### text — plain paragraph
```json
{ "type": "text", "content": "A plain body paragraph." }
```

### bullet-list — labeled rows with an accent dot
```json
{ "type": "bullet-list", "items": [
  { "label": "01", "text": "First point" },
  { "label": "02", "text": "Second point" }
] }
```

### callout — boxed highlights (same shape as bullet-list)
```json
{ "type": "callout", "items": [{ "label": "Tip", "text": "Keep labels short." }] }
```

### divider — horizontal rule
```json
{ "type": "divider" }
```

### spacer — explicit vertical gap
```json
{ "type": "spacer", "size": "md" }
```
`size` ∈ `sm | md | lg`.

### image — inline image
```json
{ "type": "image", "src": "./photo.svg", "alt": "Sunset", "width": "md", "align": "center" }
```
- `src` — remote URL (`https://…`), local file path (resolved relative to the content
  file; `png/jpg/jpeg/webp/gif/svg`), or a `data:` URI. Local paths work via the CLI
  only.
- `alt` — optional.
- `width` ∈ `sm | md | lg | full` (33 / 50 / 75 / 100% of the content column).
  Default `full`.
- `align` ∈ `left | center | right`. Default `center`.

Any block may carry an optional `"id"` string.

## Part styles

`headline` and `blockquote` hold `parts`, each `{ "text": "...", "style": "..." }`.
`style` ∈ `normal | bold | italic | accent | accent-italic | mono | muted`.
`accent` paints the theme's accent color — use it to make one phrase pop.

## Sizes

| name | px | ratio | use |
|---|---|---|---|
| `twitter` | 1200×675 | 16:9 | Twitter/X post |
| `twitter-square` | 1080×1080 | 1:1 | Twitter/X square |
| `linkedin` | 1200×627 | 1.91:1 | LinkedIn post |
| `linkedin-square` | 1080×1080 | 1:1 | LinkedIn square |
| `instagram-sq` | 1080×1080 | 1:1 | Instagram square |
| `instagram-port` | 1080×1350 | 4:5 | Instagram portrait |
| `instagram-land` | 1080×566 | 1.91:1 | Instagram landscape |
| `facebook-post` | 1200×630 | 1.91:1 | Facebook link post |
| `facebook-square` | 1080×1080 | 1:1 | Facebook square (good for carousels) |
| `facebook-cover` | 1640×624 | 2.63:1 | Facebook page cover |
| `facebook-event` | 1920×1080 | 16:9 | Facebook event cover |
| `facebook-group-cover` | 1640×856 | 1.91:1 | Facebook group cover |
| `threads-sq` | 1080×1080 | 1:1 | Threads square |
| `threads-port` | 1080×1350 | 4:5 | Threads portrait |
| `threads-land` | 1080×566 | 1.91:1 | Threads landscape |
| `story` | 1080×1920 | 9:16 | IG/FB/Twitter Story |
| `custom` | free | — | custom dimensions |

## Themes

`brand-midnight`, `brutal-white`, `dark-orange`, `dark-teal`, `kyoto`,
`light-minimal`, `mono-slate`, `noir-crimson`, `oceanic`, `paper-cream`,
`sunset-rose`, `terminal-green`.

Run `quoteforge themes list` to see swatches. Pick by mood: dark/tech →
`dark-teal`/`terminal-green`/`brand-midnight`; bold/loud → `noir-crimson`/
`dark-orange`; light/editorial → `paper-cream`/`light-minimal`/`kyoto`.

## Templates

| template | layout | best for |
|---|---|---|
| `quote` | centered statement | a single quote or statement |
| `manifesto` | dense, multiple blocks | layered text, several blocks |
| `list` | bulleted | tips, steps, enumerations |
| `minimal` | sparse | one short line, lots of space |
