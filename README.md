# Make Interfaces Feel Better

An [Agent Skill](https://docs.anthropic.com/en/docs/claude-code/skills) based on the article [Details that make interfaces feel better](https://jakub.kr/writing/details-that-make-interfaces-feel-better).

This skill teaches AI coding assistants (Claude Code, Codex, etc.) the small design engineering details that compound into a great interface.

This fork extends the original (which is web/CSS-focused) with the items below. The original has since moved to [jakubkrehel/skills](https://github.com/jakubkrehel/skills), where it was split into separate `better-*` skills; this fork keeps one skill and takes content from there where it fits (the writing rules and the type size floors so far).

- **Qt guidance** (`qt.md`) — how each principle maps onto Qt Widgets and QML, why QSS must stay a color-only layer, and how to build chrome with QPainter and design tokens instead
- **Color discipline** (`color.md`) — palette restraint, color tokenization, and dark mode background rules
- **Interruptions and consent** (`interruptions.md`) — full-screen takeovers, security ceremonies, escape routes, countdown wording, warning habituation, and showing a machine-readable code without teaching people to trust one
- **Icon discipline** (`icons.md`) — stroke weight matched to text, states through `currentColor`, outline versus fill, sizing, RTL flipping
- **Product copy** (`write.md`): name things by what the user gets, keep internals off the screen, cut filler and marketing voice, turn raw errors into instructions, disclose what the app does on the user's behalf, ship every string in every locale, keep Korean UI in 합니다체, and write READMEs for users and developers rather than as advertisements
- **Size floors** (`typography.md`): small text is an exception you justify, with web and Qt floors taken from real complaints rather than mockups
- **Field lessons** from shipping a multi-skin Qt desktop app — QSS selector traps (specificity ties, `Q_OBJECT` class matching), size-hint propagation and scroll-area clipping, item views with custom-painted rows, theme switching that goes stale, offscreen pixel verification (golden galleries, perceptibility measurement), status-glyph legibility, and variable-font weights under QSS

## What it covers

- Text wrapping (`text-wrap: balance` / `pretty`)
- Concentric border radius for nested elements
- Contextual icon animations with opacity, scale, and blur
- Font smoothing on macOS
- Tabular numbers for dynamic values
- Interruptible animations (CSS transitions vs keyframes)
- Enter animations with split and stagger
- Subtle exit animations
- Optical vs geometric alignment
- Shadows instead of borders
- Image outlines for depth
- Palette restraint and color tokens (fork addition)
- Qt Widgets/QML adaptation of all principles (fork addition)
- Readable text size floors (fork addition)
- Product copy and error messages (fork addition)

## Installation

```bash
npx skills add 115dkk/make-interfaces-feel-better
```

## Usage

Once installed, Claude will automatically apply these principles when building UI components, reviewing frontend code, or implementing animations.

You can also invoke it manually:

```
/make-interfaces-feel-better
```

## License

MIT — see [LICENSE](LICENSE).
