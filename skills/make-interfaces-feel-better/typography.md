# Typography

Typography rendering details that make interfaces feel better.

## Size Floors: Small Text Is Not a Default

Generated interfaces reach for small type by reflex: `text-xs` helper text, `11px` badges, `10px` captions, a whole sidebar at `12px`, because small text looks tidy in a mockup. On a real screen at arm's length it is hard to read, and it tells the user the words did not matter. Start every text role at a readable size and go below it only for a reason you can name.

| Role | Floor (CSS px at 100% zoom) |
| --- | --- |
| Long-form body text | `16px` |
| UI text: control labels, menu items, list rows, setting names and their descriptions, dialog and error text | `14px` |
| Captions and secondary metadata | `13px` |
| Anything the user must read to act | never below `12px`; a `12px` label is already the exception |
| Inputs on mobile | `16px` (iOS Safari zooms the page below it) |

- **Below `18px`, keep weight `400` or heavier.** Thin weights are display-only; at text sizes they vanish.
- **Small and muted compound.** A `12px` caption in low-contrast grey is worse than either alone. If the text is secondary, lower the contrast or the size, not both.
- **Fit problems are not solved by shrinking.** A label that does not fit gets rewritten shorter, wraps, or gets more room. Dropping it to `10px` hides the problem and creates a worse one.
- **Density is a product decision, not a per-component one.** A dense professional tool may sit one step lower across the board. Decide that once, in the type scale, and do not let one panel quietly drop below the rest.
- **The exceptions are narrow and named:** a label under an icon in a compact rail, a keyboard-shortcut hint, a unit suffix beside a large number. Even there, stay at `11px` or above and keep the text non-essential.
- **Qt:** the Windows default UI font (Segoe UI `9pt`, `12px` at 100% scale) is the floor for the narrow exceptions above, not the size for body text. Use about `11pt` for body and control text and `10pt` for dense secondary text, which lines up with the web table. One desktop app raised every QSS size and every `setPointSizeF` constant by `1pt` (body `10pt` to `11pt`, a dense rack `9pt` to `10pt`) after complaints from users on 27-inch monitors; start there instead of arriving there. Set sizes in points, and keep high-DPI scaling on: with it off, sizes come out in physical pixels and every label shrinks on a 4K screen.

To catch it in review, search the styles for the small end of the scale (`text-xs`, `text-[10px]`, `text-[11px]`, `font-size` under `13px`, `setPointSize` below the default) and check what each one is used for. Captions and hints may stay; labels, descriptions, errors and anything with a button in it go up.

## Text Wrapping

### text-wrap: balance

Distributes text evenly across lines, preventing orphaned words on headings and short text blocks. **Only works on blocks of 6 lines or fewer** (Chromium) or 10 lines or fewer (Firefox) — the balancing algorithm is computationally expensive, so browsers limit it to short text.

```css
/* Good — even line lengths on short text */
h1, h2, h3 {
  text-wrap: balance;
}
```

```css
/* Bad — default wrapping leaves orphans */
h1 {
  /* no text-wrap rule → "Read our
     blog" instead of balanced lines */
}
```

```css
/* Bad — balance on long paragraphs (silently ignored, wastes intent) */
.article-body p {
  text-wrap: balance;
}
```

**Tailwind:** `text-balance`

### text-wrap: pretty

Prevents orphaned words (a single word dangling on the last line) by adjusting line breaks throughout the paragraph. Unlike `balance`, it doesn't try to equalize line lengths — it just ensures the last line isn't embarrassingly short. Works on text of any length with no line-count limit.

This should be your **default for short-to-medium text** — paragraphs, descriptions, captions, list items, card text. For very long text (10+ lines), skip both `pretty` and `balance` — the browser's default wrapping is fine and you avoid unnecessary layout cost.

```css
/* Good — descriptions, captions, short paragraphs */
p, li, figcaption, blockquote {
  text-wrap: pretty;
}
```

```tsx
// Tailwind
<p className="text-pretty">
  A short paragraph that won't leave an orphan on the last line.
</p>
```

**Tailwind:** `text-pretty`

### When to Use Which

| Scenario | Use |
| --- | --- |
| Headings, titles where even distribution matters | `text-wrap: balance` |
| Short-to-medium text — paragraphs, descriptions, captions, UI text | `text-wrap: pretty` |
| Long text (10+ lines), code blocks, pre-formatted text | Neither — leave default |

## Font Smoothing (macOS)

On macOS, text renders heavier than intended by default. Apply antialiased smoothing to the root layout so all text renders crisper and thinner.

```css
/* CSS */
html {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```

```tsx
// Tailwind — apply to root layout
<html className="antialiased">
```

### Good vs. Bad

```css
/* Good — applied once at the root */
html {
  -webkit-font-smoothing: antialiased;
}

/* Bad — applied per-element, inconsistent */
.heading {
  -webkit-font-smoothing: antialiased;
}
.body {
  /* no smoothing → heavier than heading */
}
```

**Note:** This only affects macOS rendering. Other platforms ignore these properties, so it's safe to apply universally.

## Font Family Scope

This skill does not require a specific font family. Do not introduce a paid or proprietary typeface just to satisfy the polish checklist.

Use the product's existing type system unless the task explicitly asks for a type change. If the design calls for a system-native macOS feel, use the system font stack. If the design calls for a commercial face such as Helvetica Now, treat it as an optional brand decision and keep a practical fallback stack.

```css
/* System-native macOS/iOS feel */
html {
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
}
```

```css
/* Commercial brand face with safe fallbacks */
html {
  font-family: "Helvetica Now", "Helvetica Neue", Arial, sans-serif;
}
```

**Rule:** font smoothing, text wrapping, and tabular numbers are rendering details. They do not override the project's chosen font family.

## Tabular Numbers

When numbers update dynamically (counters, prices, timers, table columns), use tabular-nums to make all digits equal width. This prevents layout shift as values change.

```css
/* CSS */
.counter {
  font-variant-numeric: tabular-nums;
}
```

```tsx
// Tailwind
<span className="tabular-nums">{count}</span>
```

### When to Use

| Use tabular-nums | Don't use tabular-nums |
| --- | --- |
| Counters and timers | Static display numbers |
| Prices that update | Decorative large numbers |
| Table columns with numbers | Phone numbers, zip codes |
| Animated number transitions | Version numbers (v2.1.0) |
| Scoreboards, dashboards | |

### Caveat

Some fonts (like Inter) change the visual appearance of numerals with this property — specifically, the digit `1` becomes wider and centered. This is expected behavior and usually desirable for alignment, but verify it looks right in your specific font.

```css
/* With Inter font:
   Default:  1234  → proportional, "1" is narrow
   Tabular:  1234  → all digits equal width, "1" centered */
```

## Variable Fonts Need Static Instances (Qt)

Qt cannot select a variable font's `wght` axis from QSS `font-weight`, and widget-level font weights only partially map onto it. Bundle a variable-only family and every weight in the app renders at the default instance — usually thin, everywhere, with nothing obviously "erroring".

- **Ship static cuts** (Regular / Medium / SemiBold / Bold) instead of the variable file when the UI is Qt Widgets + QSS.
- On the web this is a non-issue (`font-weight` and `font-variation-settings` both work); the trap is Qt-specific but fatal to typography when hit.
- Symptom to recognize: an entire app whose headings and body text all look the same thin weight despite `font-weight` rules that look correct.

## CJK Fallback Metrics

When the primary font is Latin-only (DM Mono, Inter, JetBrains Mono, …) and the UI renders Korean/Japanese/Chinese text, line layout is computed from the **primary** font's ascent/descent while the CJK glyphs come from a fallback font with taller vertical metrics. The glyphs are bigger than the line box that was reserved for them.

This bites hardest in Qt: fixed-height rows, labels sized from `QFontMetrics::height()` of the Latin font, and elided text all clip Hangul vertically — a label that should read `녹스` renders with the bottom of the last glyph cut off, looking like `녹ㅅ`. On the web the same mismatch shows up as clipped ascenders/descenders inside tight `line-height` + `overflow: hidden` containers.

### Rules

- **Never ship a Latin-only family alone** when the UI can contain CJK text — and any UI with user-supplied strings (file names, device names, presets) can. Declare the fallback chain explicitly everywhere a family is set:

```cpp
// Qt — code
QFont font;
font.setFamilies({ "DM Mono", "Pretendard", "Noto Sans KR", "Malgun Gothic" });
```

```css
/* QSS / CSS — every font-family list, not just the root one */
font-family: "DM Mono", "Pretendard", "Noto Sans KR", "Malgun Gothic", monospace;
```

- **Don't size containers from the Latin font's metrics.** `QFontMetrics::height()` of the primary family under-measures CJK. Measure the actual string (`fm.boundingRect(text)`), or take the tallest metrics across the declared family chain, and add 1–2px slack in fixed-height rows.
- **Validate with a CJK sample whenever fonts change.** Any change to family, size, or a fixed `rowHeight` token gets checked against a Hangul string (e.g. `녹음 중 · 계량기 상태`), not just Latin text. Clipping only reproduces with fallback glyphs, so Latin-only screenshots prove nothing.
- **Mono readouts are the highest-risk spot.** Numeric/mono fonts chosen for [tabular numbers](#tabular-numbers) are almost always Latin-only; the moment a unit label or localized suffix joins the number, the fallback kicks in. Keep the CJK fallback in the mono chain too.
