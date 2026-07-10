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

### QSS traps inside the color coat

Even a colors-only sheet has sharp edges. Each of these has produced a
shipped bug:

- **Specificity ties are resolved by source order.** An attribute selector
  (`[active="true"]`) and a state pseudo-class (`:checked`) have the *same*
  specificity, so whichever rule sits later in the sheet wins the tie — a
  variant rule placed after the state rules silently turns the state off
  (e.g. a checked control losing its "on" lamp only when a variant property
  is set). For any state that must survive a variant, write the combined
  selector (`[active="true"]:checked`) so it wins on specificity instead of
  source order, and keep `:pressed`/`:checked` rules last within their tier.
- **Class selectors need the metaobject.** A custom `QWidget` subclass
  without `Q_OBJECT` reports its className as `QWidget`, so `MyWidget { … }`
  never matches it — and worse, a global `QWidget { background: … }` rule
  *does*, painting opaque rectangles behind custom-painted chrome. Add
  `Q_OBJECT` to every custom-painted class, prefer `objectName` selectors
  (`#DeviceChip`), and never ship a bare `QWidget { background: … }` rule.
- **Dynamic properties set after creation don't restyle descendants.** Qt
  caches which sheet rules apply per widget; toggling a property that
  descendant selectors depend on requires an unpolish/polish pass on the
  descendants too, or child labels keep the stale style forever. One more
  reason dynamic state belongs in paint code, not in property selectors.

## Form Before Color

When creating or reworking an element, decide its **form** first, and only
then coat it with color tokens. Form means everything QPainter (or QML)
owns: geometry, corner treatment, states (hover/pressed/selected/disabled),
depth strategy, and how it moves. Work it out as if in grayscale; the token
struct colors it afterwards.

The order matters because of a strong failure mode: starting from QSS. If
the first move is editing a stylesheet, the element's form silently inherits
whatever QSS can express — a flat box with a border — and no amount of color
tuning afterwards fixes that ceiling. Deciding form in paint code first means
QSS never constrains the design; it only tints it.

In design review this failure mode has a name: "a coat of paint". A new
composite control assembled from stock widgets plus a stylesheet fails
differentiated-design review predictably, and retrofitting form afterwards
means rebuilding the control. Composite bodies (plots, meters, routing
views, pickers) get a custom-painted widget from the first commit — never a
stock widget "for now".

Practically, for every new element ask, in order:

1. What shape and states does it have, and who paints them? (QPainter/QML —
   never QSS)
2. Which existing tokens color those states? (Add a token only if no role
   fits — see [color.md](color.md); never hardcode a hex in paint code.)
3. Is anything left for QSS at all? (Usually only stock sub-widgets embedded
   inside the element.)

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

CJK text has an extra Qt-specific trap — Latin-only primary fonts clip
Hangul/Han/Kana in fixed-height rows. See "CJK Fallback Metrics" in
[typography.md](typography.md).

## Custom Painting Rules

**Separate state from paint.** The widget owns input handling and state
(pressed, hovered via `underMouse()`/enter-leave events, value); painting is a
pure function of that state plus theme tokens. This keeps repaints cheap and
lets a theme switch restyle everything with one `update()` broadcast.

**HiDPI correctness.** Cache expensive backgrounds in a `QPixmap` keyed by
`(size, state, devicePixelRatioF())` and set `pixmap.setDevicePixelRatio()`.
For crisp 1px lines under antialiasing, align to half-pixel offsets
(`QRectF(x + 0.5, y + 0.5, …)`) or use a cosmetic pen. Two more traps that
are invisible at 100% scale and only bite on scaled monitors: (1)
`QIcon::pixmap()` returns a DPR-baked pixmap, so when compositing it into
another pixmap, center using `deviceIndependentSize()` — centering on
`width()` drifts at 200%. (2) Native child windows (embedded plugin views,
HWND hosts) speak **physical** pixels while Qt geometry is logical; convert
with `devicePixelRatioF()` at the boundary, in one helper, and test on a
scaled monitor.

**Animation driver.** Use `QVariantAnimation` updating a member (e.g.
`hoverProgress` 0→1) and calling `update()` per tick. Hover in: ~150 ms
ease-out; hover out and exits shorter and softer. All easing through
`QEasingCurve` (`OutCubic` ≈ the web `ease-out` used elsewhere in this skill).

**Effects budget.** At most one `QGraphicsEffect` per widget is possible, and
even one is usually too many — opacity effects conflict with child effects and
disable backing-store optimizations. Prefer painting alpha directly.

## Size Hints Don't Propagate Themselves

Qt layouts cache minimum sizes and size hints aggressively. Three failure
modes recur in any card/row UI:

- **Changing a child's height doesn't grow its ancestors.** After
  `setFixedHeight()` on nested content, cached minimums in intermediate
  layouts stop the update partway up the chain — the row simply never grows
  (or never shrinks), and the content renders clipped. Explicitly call
  `layout()->invalidate()` on every layout from the changed widget up to the
  row container.
- **`QScrollArea` samples `heightForWidth` at pre-layout width** (often the
  minimum width of a single wrapped item) and caches it, so wrapping content
  gets a wildly wrong height — tall and narrow, or clipped short. Sync it
  yourself: install an event filter for `Resize`/`Show`/`LayoutRequest` and
  set the scroll area's height from `heightForWidth(viewport width)`,
  bounded to sane limits.
- **Never resize-to-hint from a resize path.** Calling `updateGeometry()` +
  `resize(sizeHint())` anywhere reachable from `resizeEvent` ping-pongs with
  a `widgetResizable` scroll area over the width until the stack overflows
  (0xC00000FD). Resize-to-hint only from user-action paths, and only when
  the hint can actually change.

## Item Views With Custom-Painted Rows

When a `QStyledItemDelegate` owns row painting entirely:

- The view still paints palette `Highlight` on the branch/indentation strip
  *outside* the delegate's rect — a stray color bar beside the painted
  selection. Set `setIndentation(0)` (the delegate owns left structure) and
  make the palette `Highlight` transparent.
- The base `editorEvent` toggles check state from any mouse release over
  *its own idea* of the checkbox rect — no press/release pairing, wrong
  rect. If you paint your own toggle, consume releases over your toggle rect
  unconditionally and never forward them to the base handler.
- Connect `itemChanged`-style signals **after** populating the model.
  Population emits a change per `setData` while rows are half-built (data
  roles still unset), and handlers dereference incomplete items.

## Theme Switching Re-runs Everything

A theme switch must flow through the same styling entry point as startup.
Anything applied only in the startup path — icon tinting, palettes,
window-chrome colors — silently goes stale on switch, and nobody notices
until a user toggles dark mode at runtime.

- Route startup and switch through one function; connect chrome widgets to a
  theme-changed signal instead of styling them from the startup path.
- Widgets that choose their chrome at construction time (per-theme
  renderers, painted decorations) must be **recreated** on switch —
  repainting is not enough.
- Paint code reads the app's own theme state, never the OS scheme; mixed
  sources produce widgets that disagree after a switch.
- Test switching in a loop. Repeated switches expose leaks: widgets orphaned
  instead of deleted grow the widget count every round, and each switch gets
  slower than the last. A simple widget census before/after N switches
  catches this.

## Prove Changes With Pixels

Qt has no snapshot-testing culture like the web, but offscreen rendering
makes one cheap, and eyeballing is not verification. These patterns catch
real regressions:

- **Golden gallery.** Add a hidden CLI mode that renders representative
  widget states to PNGs under `QT_QPA_PLATFORM=offscreen`. Byte-compare
  against a baseline to prove a refactor changed nothing — or that a change
  touched exactly the intended shots and no others.
- **Render baseline and candidate into the same absolute path.** Any scene
  that draws environment-derived text (file paths, temp directories) into
  the render produces false diffs when rendered from different directories.
- **Neutralize hover and focus before grabbing.** Offscreen platforms leave
  the cursor at (0,0) and activate the window, so the top-left widget grabs
  as hovered-and-focused in its "normal" shot. Send `QEvent::Leave` and
  `clearFocus()` before grabbing.
- **Painted is not visible.** A subtle state can pass code review and still
  be imperceptible — a hover fill at alpha 16/255 is ~3.5% brightness delta:
  painted, invisible. Prove visibility by pixel-diffing the state render
  against the normal render (changed-pixel count *and* max channel delta; as
  a rule of thumb, a max delta under ~30 is invisible on real panels). When
  it fails, retune the state — don't trust the spec number.
- **Capture live windows from inside the app.** On scaled monitors a
  DPI-unaware external capture (`PrintWindow`/`CopyFromScreen` from a stock
  script) returns a cropped top-left quadrant — which reads as "the control
  is missing" and sends the investigation the wrong way. Use
  `QWidget::grab()`/`QScreen::grabWindow()` from the app, or make the
  capturing process per-monitor DPI aware.

## Qt Review Checklist (in addition to the main checklist)

- [ ] Form before color: every new element's shape/states/motion were decided
      in paint code (QPainter/QML) first, then colored via tokens — the work
      did not start from a QSS edit
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
- [ ] No bare `QWidget { background: … }` rule; custom-painted classes have
      `Q_OBJECT` so selectors can actually target them
- [ ] States that must survive variant rules use combined selectors — no
      specificity ties left to source order
- [ ] Height changes invalidate the layout chain up to the row; nothing
      reachable from `resizeEvent` resizes to `sizeHint()`
- [ ] Theme switch re-runs the same styling path as startup and recreates
      construction-time chrome
- [ ] Physical↔logical pixel conversion (native child windows, DPR-baked
      pixmaps) happens at one boundary and was tested on a scaled monitor
- [ ] Visual changes are proven with offscreen golden renders (same output
      path, hover/focus neutralized); subtle states pixel-diffed for
      visibility, not just painted
