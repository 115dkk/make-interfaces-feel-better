# Typography

Typography rendering details that make interfaces feel better.

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
