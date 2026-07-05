# Qt (Widgets & QML)

How to apply this skill's principles in Qt desktop applications. The other
reference files are written in CSS/web terms; this file maps every principle
onto Qt and adds the Qt-specific rules that have no web equivalent.

Applies when the project uses Qt Widgets (C++/`.ui` files/`.pro`/`.vcxproj`
with Qt modules) or Qt Quick (QML). Detect it before reaching for any CSS
advice — none of the CSS snippets in the other files can be pasted into QSS.

## The Prime Rule: QSS Is a Paint Bucket, Not a Design Tool

Qt Style Sheets look like CSS but implement a small, frozen subset of CSS 2.1.
Treating QSS as "CSS for Qt" is the single most common way Qt interfaces end
up broken or ugly. The technology hierarchy is:

1. **QML (Qt Quick)** — if the project uses it or can use it for a given
   surface, prefer it. Property bindings, `Behavior`, states/transitions and
   `MultiEffect` give web-grade motion and depth. The principles in
   [animations.md](animations.md) apply nearly 1:1.
2. **QPainter custom painting** (`paintEvent` overrides, `QStyledItemDelegate`,
   `QGraphicsScene`) — the default for anything the skill calls *chrome*:
   shadows, layered depth, rounded panels, knobs/sliders, hover and press
   feedback, focus rings, staged animations, cross-fading icons.
3. **QSS** — colors only. One app-wide sheet that sets palette roles, fonts,
   and flat styling of stock controls. Nothing else.

### What QSS may do (the "color coat")

- Background/text/border colors and simple gradients (`qlineargradient`)
- Font family and size on widget classes
- Flat `border`, `border-radius`, `padding` on **stock** controls
  (buttons, line edits, combo boxes, scroll bars, tabs, tooltips)
- Selection colors, disabled/hover **color** changes (not motion)

### What QSS must never be used for

- **Shadows or depth** — there is no `box-shadow`. Paint depth yourself
  (see below) or use a 1px lighter top edge for a "glass" look.
- **Motion** — there are no `transition` or `animation` properties at all.
  Every hover/press/enter/exit animation in a Qt app is code, not style.
- **Layout** — no flexbox, no grid, no `calc()`, no `transform`. Layout
  belongs to `QLayout`/QML positioners.
- **Sub-control drawing tricks** — e.g. the classic CSS border-triangle hack
  for combo-box arrows (`image: none` on a 0×0 box plus colored borders)
  silently collapsed into a flat dash on Qt 6.10. QSS sub-control rendering
  is version-fragile; ship real SVG assets (`image: url(:/icons/chevron.svg)`)
  for arrows, checks, and indicators instead of drawing them with borders.
- **Per-widget inline styling** — `widget->setStyleSheet(...)` scattered
  through code. A widget-level sheet swaps in `QStyleSheetStyle` for that
  widget and its children, breaking native-style rendering in subtle ways,
  and repeated calls trigger expensive re-polish passes. Centralize into one
  token-substituted app sheet applied once with `qApp->setStyleSheet(...)`.

## Design Tokens: QSS Has No Variables

QSS has no custom properties, no `var()`, no `calc()`. Keep one token struct
in code as the single source of truth, and substitute placeholders into the
QSS sheet at load time. Custom-painting widgets read the same struct, so
painted chrome and QSS-coated stock widgets can never drift apart.

```cpp
struct ThemeTokens {
    QString background = "#12121e";
    QString surface    = "#1a1a28";
    QString text       = "#e8e8f4";
    QString mutedText  = "#8888a8";
    QString border     = "#2a2a3c";
    QString accent     = "#3B82F6";
    int borderRadius   = 10;
    int rowHeight      = 40;
};

// theme.qss uses placeholders:  QWidget { background: @BG@; color: @TEXT@; }
QString sheet = loadSheet(":/themes/dark.qss");
sheet.replace("@BG@", tokens.background).replace("@TEXT@", tokens.text) /* … */;
qApp->setStyleSheet(sheet);   // once, at theme switch — never per widget
```

Dynamic state should not round-trip through QSS. Property-selector restyling
(`setProperty("selected", true)` + `unpolish()`/`polish()`) forces a style
recomputation per toggle; a custom-painted widget just reads its own state in
`paintEvent` and calls `update()`.

## Principle Mapping

| Skill principle | Qt Widgets (QPainter) | QML |
| --- | --- | --- |
| Concentric border radius | Compute `outer = inner + padding` when painting nested rounded rects; QSS `border-radius` only on flat stock widgets | Same math on `Rectangle.radius` |
| Shadows over borders | Paint 2–3 stacked rounded rects with low-alpha `QColor`, or 1px lighter top edge; avoid `QGraphicsDropShadowEffect` (rasterizes the widget, slow, blurry at HiDPI) | `MultiEffect { shadowEnabled: true }` (Qt 6.5+), or a pre-rendered 9-patch shadow image |
| Interruptible animations | `QVariantAnimation`/`QPropertyAnimation`: on state change, `stop()` and restart **from the current value** toward the new target — never from 0 | `Behavior on x { NumberAnimation }` — interruptible by default |
| Split & stagger enters | One animation per chunk, offset start times (`QSequentialAnimationGroup` + `QPauseAnimation`, or staggered `QTimer` starts) | `SequentialAnimation` / per-item `PauseAnimation` |
| Subtle exits | Shorter duration than enter (150 ms vs 300 ms), small translate painted as an offset | `exit` transitions with small `y` delta |
| Icon cross-fade | Keep both pixmaps, paint with `painter.setOpacity()` + scale about the center; **drop the blur term** — real-time blur is not cheap in widgets | `opacity` + `scale` Behaviors; blur via `MultiEffect` only if already using it |
| Scale on press | Don't scale the widget (relayout jank). In `paintEvent`: `translate(center); scale(0.96); translate(-center)` while pressed, driven by an animation | `scale: pressed ? 0.96 : 1.0` + `Behavior on scale` |
| Tabular numbers | Qt 6.7+: `font.setFeature(QFont::Tag("tnum"), 1)`; otherwise use the theme's mono font for numeric readouts, or reserve width with `QFontMetrics::horizontalAdvance` of the widest value | `font.features: { "tnum": 1 }` |
| Font smoothing | `painter.setRenderHint(QPainter::Antialiasing)` and `QPainter::TextAntialiasing` in every custom `paintEvent`; set an app-default `QFont` with per-platform fallbacks | Default renderer handles it |
| Minimum hit area | Widget rect ≥ 40×40 even when the painted control is smaller — paint small inside a larger rect (the pseudo-element trick, inverted); override `sizeHint()` | `TapHandler` margins / larger `MouseArea` than the visual |
| Optical alignment | Nudge in paint code or per-side layout margins; icon-side padding = text-side − 2px still applies | Same, with `anchors` offsets |
| Image outlines | After drawing a pixmap, stroke a 1px rounded rect in `rgba(0,0,0,0.1)` / `rgba(255,255,255,0.1)` (pure black/white only, per [surfaces.md](surfaces.md)) | 1px `border` on the wrapping `Rectangle` with the same colors |

## Custom Painting Rules

**Separate state from paint.** The widget owns input handling and state
(pressed, hovered via `underMouse()`/enter-leave events, value); painting is a
pure function of that state plus theme tokens. This keeps repaints cheap and
lets a theme switch restyle everything with one `update()` broadcast.

**HiDPI correctness.** Cache expensive backgrounds in a `QPixmap` keyed by
`(size, state, devicePixelRatioF())` and set `pixmap.setDevicePixelRatio()`.
For crisp 1px lines under antialiasing, align to half-pixel offsets
(`QRectF(x + 0.5, y + 0.5, …)`) or use a cosmetic pen.

**Animation driver.** Use `QVariantAnimation` updating a member (e.g.
`hoverProgress` 0→1) and calling `update()` per tick. Hover in: ~150 ms
ease-out; hover out and exits shorter and softer. All easing through
`QEasingCurve` (`OutCubic` ≈ the web `ease-out` used elsewhere in this skill).

**Effects budget.** At most one `QGraphicsEffect` per widget is possible, and
even one is usually too many — opacity effects conflict with child effects and
disable backing-store optimizations. Prefer painting alpha directly.

## Qt Review Checklist (in addition to the main checklist)

- [ ] QSS contains only colors, fonts, and flat stock-control styling — no
      drawing tricks, no layout, no per-widget inline sheets
- [ ] One app-wide token-substituted sheet; theme values come from a single
      token struct shared with custom painters
- [ ] Indicators/arrows are SVG assets, not QSS border hacks
- [ ] All motion is `QVariantAnimation`/`QPropertyAnimation`/QML `Behavior`,
      interruptible (retargets from current value, never restarts)
- [ ] Custom `paintEvent`s set `Antialiasing` + `TextAntialiasing`
- [ ] No `QGraphicsDropShadowEffect` on frequently repainted widgets — depth
      is painted (stacked alpha rects or lighter top edge)
- [ ] Pixmap caches are keyed by device pixel ratio
- [ ] Dynamic state does not restyle via property selectors + repolish;
      stateful widgets custom-paint and `update()`
