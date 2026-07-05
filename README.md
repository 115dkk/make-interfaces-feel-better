# Make Interfaces Feel Better

An [Agent Skill](https://docs.anthropic.com/en/docs/claude-code/skills) based on the article [Details that make interfaces feel better](https://jakub.kr/writing/details-that-make-interfaces-feel-better).

This skill teaches AI coding assistants (Claude Code, Codex, etc.) the small design engineering details that compound into a great interface.

This fork extends the original (which is web/CSS-focused) with:

- **Qt guidance** (`qt.md`) — how each principle maps onto Qt Widgets and QML, why QSS must stay a color-only layer, and how to build chrome with QPainter and design tokens instead
- **Color discipline** (`color.md`) — palette restraint, color tokenization, and dark mode background rules

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

## Installation

```bash
npx skills add jakubkrehel/make-interfaces-feel-better
```

## Usage

Once installed, Claude will automatically apply these principles when building UI components, reviewing frontend code, or implementing animations.

You can also invoke it manually:

```
/make-interfaces-feel-better
```

## License

MIT — see [LICENSE](LICENSE).
