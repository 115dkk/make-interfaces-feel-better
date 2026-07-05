# Color Discipline

Palette restraint rules. Toolkit-agnostic — they apply equally to CSS, QSS
token structs, and QML themes.

Adapted from a Korean designer's write-up on why AI-generated frontends feel
off: <https://m.dcinside.com/board/thesingularity/1291953>

## Fewer Hues, More Derivation

The most common reason an interface feels amateurish is too many colors
carrying meaning. Semantic colors already consume most of the budget: success
green, warning yellow, danger red, plus background and text. That is five
meanings before any branding. Every additional hue dilutes the rest — the Von
Restorff effect in reverse: when everything is highlighted, nothing is.

**Rule:** one dominant color family (usually a tinted neutral) plus **one**
accent. Everything else is derived from those by changing lightness,
saturation, or alpha — never by introducing a new hue.

- Think in HSL (or OKLCH on the web): keep H fixed, derive states by moving
  L and S. Hover = slightly lighter/darker of the same hue, not a new color.
- If a screen feels busy or cheap, the first fix to try is removing colors,
  not adjusting them. Squint at the layout and count the hues competing for
  attention; more than 2 non-semantic hues is a red flag.
- Semantic colors (success/warning/danger) are reserved words. Never use red,
  green, or yellow decoratively — they must keep their meaning.

## Tokenize and Restrict

Name colors by role, not by value, and make the roles the only way to color
anything: `background`, `surface`, `card`, `text`, `mutedText`, `border`,
`accent`, `success`, `warning`, `danger`. Raw hex values scattered through
components (or QSS sheets) are how palettes decay. A small closed set of
tokens is an enforcement mechanism, not just organization — it makes "add a
seventh meaningful color" impossible by construction.

## Never Pure Black, Never Pure White

Raw `#000000` backgrounds and `#FFFFFF` surfaces read as harsh and cheap, and
full-black dark modes are genuinely uncomfortable on desktop monitors (the
AMOLED power argument doesn't apply there).

- Dark backgrounds: mix a slight blue cast (e.g. `#0c0c16`, `#12121e`) for a
  cool/sleek feel, or a slight warm/red cast for a softer one.
- Light backgrounds: a touch of warmth (`#faf9f7`-ish) instead of `#FFFFFF`.
- Pure RGB primaries (`#FF0000`, `#00FF00`, `#0000FF`) are the fastest way to
  make a UI look dated. Desaturate and shift them.
- The one exception, by design: the 1px **image outline** from
  [surfaces.md](surfaces.md) must stay pure black/white at 10% alpha —
  tinted outlines read as dirt on the image edge.

## Brand First

If the project has (or needs) an identity — icon, logo, symbol — derive the
accent and neutrals from it, ideally before styling screens. A theme grown
from the brand doubles as the frontend principle; a palette bolted on later
always looks bolted on.

## Picking a Palette

When starting from nothing:

- [Adobe Color](https://color.adobe.com/create/color-wheel) — harmony rules,
  palette extraction from images
- [tweakcn](https://tweakcn.com/editor/theme) — shadcn/ui theme editor (web
  projects)
- [Coolors](https://coolors.co/generate) — quick palette generation

## Checklist

- [ ] One accent hue; all other non-semantic colors are derived neutrals
- [ ] Success/warning/danger colors are never used decoratively
- [ ] Every color in code goes through a named role token
- [ ] No `#000000` background, no `#FFFFFF` surface, no RGB primaries
- [ ] Dark mode background is a tinted dark, not gray and not pure black
- [ ] Accent derives from the product's brand identity where one exists
