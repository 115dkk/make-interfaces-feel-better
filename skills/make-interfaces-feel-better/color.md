# Color Discipline

Palette restraint rules. Toolkit-agnostic — they apply equally to CSS, QSS
token structs, and QML themes.

Adapted from a Korean designer's write-up on why AI-generated frontends feel
off: <https://m.dcinside.com/board/thesingularity/1291953>. Parts are adapted
from `better-colors` in [jakubkrehel/skills](https://github.com/jakubkrehel/skills)
(MIT).

## Fewer Hues, More Derivation

The most common reason an interface feels amateurish is too many colors
carrying meaning. Semantic colors already consume most of the budget: success
green, warning yellow, danger red, plus background and text. That is five
meanings before any branding. Every additional hue dilutes the rest — the Von
Restorff effect in reverse: when everything is highlighted, nothing is.

**Rule:** one dominant color family (usually a neutral) plus **one** accent.
Everything else is derived from those by changing lightness, chroma, or
alpha — never by introducing a new hue.

- Derive in a perceptual space: OKLCH on the web, or a color library
  (`culori`, `colorjs.io`, `chroma.js`) that does the math perceptually and
  emits the project's notation. HSL lightness is not perceived lightness, so a
  ramp stepped in HSL bunches at one end and drifts in hue. Keep the hue fixed
  and move lightness and chroma; hover is a lighter or darker step of the same
  hue, not a new color.
- Qt's `QColor` has no OKLCH. Compute the ramp offline with a color library
  and store the resulting hex values in the token struct.
- Never pick a ramp step by eye or estimate a value you could compute.
- If a screen feels busy or cheap, the first fix to try is removing colors,
  not adjusting them. Squint at the layout and count the hues competing for
  attention; more than 2 non-semantic hues is a red flag.

## One Color, One Meaning

A hue means one thing across the whole interface, and anything within about
15° of it counts as the same hue. The rule runs both ways: the color must not
appear where its meaning does not apply, and must not be missing where it
does.

- If the accent means interactive, accent on static text tells users to click
  something that does nothing, and an interactive element left neutral hides
  that it can be clicked. This is the color side of "the label and its styling
  agree" in [write.md](write.md).
- Semantic colors (success/warning/danger) are reserved words. Never use red,
  green, or yellow decoratively — they must keep their meaning.
- A status hue must not collide with the accent. If the brand is red, move
  danger toward a deeper crimson and check a destructive and a primary button
  side by side.
- **Rise and fall follow the locale.** Korean market screens show a rise in
  red and a fall in blue; Western ones show a rise in green and a fall in red.
  Give rise and fall their own per-locale tokens, never `success` and
  `danger`. A rising price on a Korean finance screen is the one place red
  does not mean danger.

## Fill One Action per View

When a filled color marks the primary action, exactly one action on the view
gets it and its peers stay neutral. Put the color on the background, not the
label: a filled button reads as primary from across the room, and
accent-colored text on a neutral button reads as a link.

- A selected tab or a checked segment may use the accent on its glyph and
  label. That is state, not emphasis.
- Several colored backgrounds are fine when they encode different states or
  categories rather than competing as peers.
- An existing hierarchy that already shows emphasis another way stays; do not
  recolor controls only to fit this rule.

## Tokenize and Restrict

Name colors by role, not by value, and make the roles the only way to color
anything: `background`, `surface`, `card`, `text`, `mutedText`, `border`,
`accent`, `success`, `warning`, `danger`. Raw hex values scattered through
components (or QSS sheets) are how palettes decay. A small closed set of
tokens is an enforcement mechanism, not just organization — it makes "add a
seventh meaningful color" impossible by construction.

- **Use a token only in its role.** Never borrow a token because its value
  looks right today. A separator token used as caption text works until
  borders get lighter, and then the text fades with them. If a role has no
  token, add the token. Separator and border are separate roles even while
  they share a value.
- **`accent` is the brand; `primary` means the most prominent of its group.**
  `--color-primary` for the brand beside `--color-text-primary` for body text
  makes every `primary` ambiguous until someone opens its definition.

## Never Pure Black, Never Pure White

Raw `#000000` backgrounds and `#FFFFFF` surfaces read as harsh and cheap, and
full-black dark modes are genuinely uncomfortable on desktop monitors (the
AMOLED power argument doesn't apply there).

- Dark backgrounds: a near-black neutral gray is fine. A slight blue cast
  (e.g. `#0c0c16`, `#12121e`) feels cool and sleek, a slight warm cast softer;
  the cast is a style choice, not a fix.
- Light backgrounds: an off-white (`#faf9f7`-ish when the neutrals are warm)
  instead of `#FFFFFF`.
- **Pick one temperature for the neutrals (cool, warm or none) and hold it
  across the whole ramp.** A warm gray border on a cool gray background is
  visible even when neither color is nameable on its own.
- Pure RGB primaries (`#FF0000`, `#00FF00`, `#0000FF`) are the fastest way to
  make a UI look dated. Desaturate and shift them.
- The one exception, by design: the 1px **image outline** from
  [surfaces.md](surfaces.md) must stay pure black/white at 10% alpha —
  tinted outlines read as dirt on the image edge.

## Dark Mode Is Not the Light Palette Reversed

Reversing the light palette is where a dark theme starts, not where it ends.
After the swap, three things almost always need tuning:

- Lower the accent's chroma a step or two. A color that looks confident on
  white glows like neon on near-black.
- Widen the spacing at the dark end. Steps that read as two pale surfaces merge
  into one as dark surfaces.
- Remeasure every pair (see Measure Contrast). Contrast is not symmetric, so a
  pair that passes in light can fail in dark.

Switch themes through one mechanism. On the web that is `prefers-color-scheme`
alone, a `.dark` class, or `light-dark()`, never a mix; in Qt it is the single
styling path in [qt.md](qt.md).

## Measure Contrast, Don't Estimate It

Contrast has an exact answer, so compute it.

- Never report a contrast value you did not measure.
- Measure the foreground against the background it actually renders on,
  usually the nearest ancestor that paints one: the card, not the page. A
  translucent surface or text over an image has no single background; measure
  the worst region it can sit over.
- Fix a pair that falls short by moving lightness, holding hue and chroma,
  then remeasure. Hue barely moves contrast, so changing it only turns a
  contrast fix into a palette change.
- A mid-lightness background caps what any text on it can reach. Body text
  needs a background near one end of the ramp.

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
- [ ] Ramps are derived in a perceptual space (OKLCH or a color library), not
  by stepping HSL lightness
- [ ] The accent appears only where its meaning applies, and everything with
  that meaning carries it
- [ ] Success/warning/danger colors are never used decoratively, and no status
  hue collides with the accent
- [ ] Rise and fall use per-locale tokens (Korea: rise red, fall blue), never
  `success` and `danger`
- [ ] One filled primary action per view; peers are neutral
- [ ] Every color in code goes through a named role token, used only in its
  role
- [ ] No `#000000` background, no `#FFFFFF` surface, no RGB primaries
- [ ] Neutrals hold one temperature (cool, warm or none) across the ramp
- [ ] Dark theme tuned after reversal: lower accent chroma, wider dark-end
  spacing, every pair remeasured
- [ ] Every contrast value reported was measured against the background the
  element renders on
- [ ] Accent derives from the product's brand identity where one exists
