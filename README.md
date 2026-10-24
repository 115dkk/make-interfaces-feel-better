# Make Interfaces Feel Better

An [Agent Skill](https://docs.anthropic.com/en/docs/claude-code/skills) based on the article [Details that make interfaces feel better](https://jakub.kr/writing/details-that-make-interfaces-feel-better).

This skill teaches AI coding assistants (Claude Code, Codex, etc.) the small design engineering details that compound into a great interface.

This fork extends the original (which is web/CSS-focused) with the items below. The original has since moved to [jakubkrehel/skills](https://github.com/jakubkrehel/skills), where it was split into separate `better-*` skills; this fork keeps one skill and takes content from there where it fits (the writing rules and the type size floors so far).

- **Qt guidance** (`qt.md`) — how each principle maps onto Qt Widgets and QML, why QSS must stay a color-only layer, and how to build chrome with QPainter and design tokens instead
- **Color discipline** (`color.md`) — palette restraint, color tokenization, and dark mode background rules
- **Interruptions and consent** (`interruptions.md`) — full-screen takeovers, security ceremonies, escape routes, countdown wording, warning habituation, and showing a machine-readable code without teaching people to trust one
- **Icon discipline** (`icons.md`) — stroke weight matched to text, states through `currentColor`, outline versus fill, sizing, RTL flipping
- **Product copy** (`write.md`): name things by what the user gets, keep internals off the screen, cut filler, give the product no persona (it does not chat, comfort, sell itself or write prose), turn raw errors into instructions, disclose what the app does on the user's behalf, ship every string in every locale, use a middle dot only to join two items and never to list, keep Korean UI in 합니다체, and write READMEs for users and developers rather than as advertisements
- **Game writing** (`game-write.md`): build the protagonist's situation before the feature, let the machine speak like a machine and the characters like people, keep real outages out of the fiction, and put clues in the world instead of numbered hint ladders
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

As a Claude Code plugin, which also installs the copy check hook described below:

```
/plugin marketplace add 115dkk/make-interfaces-feel-better
/plugin install make-interfaces-feel-better@115dkk-screen
```

As a plain skill for Claude Code, Codex and other agents (no hook):

```bash
npx skills add 115dkk/make-interfaces-feel-better
```

## Copy check hook

The plugin registers a `PreToolUse` hook on `Write`, `Edit` and `MultiEdit`. Before the edit lands, the hook takes the lines it adds that contain Hangul, together with the other lines it adds within three lines of them, and asks a separate model, Claude Sonnet 5.5 at medium effort through your own `claude` CLI, whether any interface copy or README prose among them clearly breaks a rule in [`write.md`](skills/make-interfaces-feel-better/write.md).

- A breach denies the edit. The reason quotes each offending expression, names the rule and gives a corrected wording, and Claude rewrites the text before writing it.
- The nearby lines let the judge see code next to the copy, such as `{error.message}` rendered on the line below a Korean label.
- Anything else goes through unchanged. Edits that add no line with Hangul, Markdown files other than READMEs, tests, `.github`, and files under `~/.claude` and `~/.codex` are passed without calling the model.
- If the judge cannot run (no `claude` on `PATH`, a timeout, an error), the edit goes through and Claude is told it was not checked.

Each judged edit costs one model call, about 5 to 10 seconds, on your Claude plan. The hook needs `python3` and `claude` on `PATH`. Copy in other languages is not judged yet.

## Usage

Once installed, Claude will automatically apply these principles when building UI components, reviewing frontend code, or implementing animations.

You can also invoke it manually:

```
/make-interfaces-feel-better
```

## License

MIT — see [LICENSE](LICENSE).
