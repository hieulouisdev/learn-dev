# Lesson 16: CSS Cheatsheet

> Module: CSS · Lesson 16 of 16
> Print this page or bookmark it — your one-page reference for every CSS property you've learned.

---

## 1. Adding CSS to HTML

```html
<!-- External (recommended) -->
<link rel="stylesheet" href="style.css">

<!-- Internal -->
<style>...</style>

<!-- Inline (avoid) -->
<p style="color: red;">
```

---

## 2. The CSS Rule Structure

```css
selector {
  property: value;
}
```

- **Selector**: which elements to style.
- **Property**: what aspect to change.
- **Value**: what to set it to.
- **Declaration**: one `property: value;` pair.
- Always end declarations with `;`.

---

## 3. Selectors

| Selector | Example | Meaning |
|----------|---------|---------|
| Type | `p` | All `<p>` |
| Class | `.card` | All `class="card"` |
| ID | `#main` | The one `id="main"` |
| Universal | `*` | All elements |
| Attribute | `[type="text"]` | All with `type="text"` |
| Descendant | `article p` | `<p>` inside `<article>` |
| Child | `article > p` | `<p>` directly inside `<article>` |
| Adjacent sibling | `h2 + p` | `<p>` immediately after `<h2>` |
| General sibling | `h2 ~ p` | All `<p>` after `<h2>` |
| Group | `h1, h2, h3` | Multiple selectors |

### Pseudo-classes

| Pseudo-class | Applies when |
|--------------|--------------|
| `:hover` | Mouse over |
| `:focus` | Has focus |
| `:active` | Being clicked |
| `:visited` | Link visited |
| `:link` | Link not visited |
| `:checked` | Checkbox/radio checked |
| `:disabled` / `:enabled` | Form state |
| `:first-child` | First in parent |
| `:last-child` | Last in parent |
| `:nth-child(n)` | Nth in parent (odd, even, formula) |
| `:not(selector)` | Doesn't match |
| `:empty` | No children |

### Pseudo-elements

| Pseudo-element | Targets |
|----------------|---------|
| `::before` | Insert content before |
| `::after` | Insert content after |
| `::first-letter` | First letter |
| `::first-line` | First line |
| `::selection` | Highlighted text |

---

## 4. Colors

| Format | Example |
|--------|---------|
| Named | `red`, `cornflowerblue` |
| Hex | `#ff0000`, `#fff` |
| RGB | `rgb(255, 0, 0)` |
| RGBA | `rgba(255, 0, 0, 0.5)` |
| HSL | `hsl(0, 100%, 50%)` |
| HSLA | `hsla(0, 100%, 50%, 0.5)` |
| Current color | `currentColor` |
| Transparent | `transparent` |

---

## 5. Text and Fonts

| Property | Purpose |
|----------|---------|
| `font-family` | Font stack |
| `font-size` | Size (rem preferred) |
| `font-weight` | 100-900, normal, bold |
| `font-style` | normal, italic, oblique |
| `font-variant` | small-caps |
| `line-height` | 1.5 for body text |
| `letter-spacing` | Space between letters |
| `word-spacing` | Space between words |
| `text-align` | left, right, center, justify |
| `text-decoration` | underline, line-through, none |
| `text-transform` | uppercase, lowercase, capitalize |
| `text-indent` | First line indent |
| `text-shadow` | `2px 2px 4px rgba(0,0,0,0.3)` |
| `white-space` | normal, nowrap, pre, pre-wrap |
| `color` | Text color |
| `direction` | ltr, rtl |

### Font Shorthand

```css
font: italic 700 16px/1.5 "Inter", sans-serif;
/* style weight size/line-height family */
```

### Web Fonts (Google Fonts)

```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
```

### Self-Hosted

```css
@font-face {
  font-family: "MyFont";
  src: url("MyFont-Regular.woff2") format("woff2");
  font-weight: 400;
  font-display: swap;
}
```

---

## 6. The Box Model

```
┌───────────────────┐
│      margin        │
│  ┌──────────────┐ │
│  │   border      │ │
│  │ ┌──────────┐ │ │
│  │ │ padding  │ │ │
│  │ │  ┌────┐  │ │ │
│  │ │  │CONT│  │ │ │
│  │ │  │ENT │  │ │ │
│  │ │  └────┘  │ │ │
│  │ └──────────┘ │ │
│  └──────────────┘ │
└───────────────────┘
```

### Always start with

```css
* { box-sizing: border-box; margin: 0; padding: 0; }
```

### Properties

| Property | Purpose |
|----------|---------|
| `width`, `height` | Content size |
| `min-width`, `max-width` | Constraints |
| `min-height`, `max-height` | Constraints |
| `padding` | Inner space (TRBL shorthand) |
| `margin` | Outer space (TRBL shorthand) |
| `border` | Line around element |
| `outline` | Line outside border (no space) |

### Shorthand Patterns

```css
margin: 20px;             /* all sides */
margin: 20px 40px;         /* TB 20, LR 40 */
margin: 20px 40px 30px;    /* T 20, LR 40, B 30 */
margin: 10px 20px 30px 40px; /* T R B L (clockwise) */
```

---

## 7. Borders

```css
border: 2px solid #ccc;          /* shorthand */
border-radius: 8px;               /* all corners */
border-radius: 50%;               /* circle (if square) */
border-radius: 9999px;            /* pill */
```

Border styles: `solid`, `dashed`, `dotted`, `double`, `groove`, `ridge`, `inset`, `outset`, `none`.

Per-side: `border-top`, `border-right`, `border-bottom`, `border-left`.

Per-corner: `border-top-left-radius`, etc.

---

## 8. Backgrounds

```css
background: #2c3e50 url("hero.jpg") no-repeat center / cover;
/* color image repeat attachment position / size */
```

| Property | Values |
|----------|--------|
| `background-color` | color |
| `background-image` | `url(...)` |
| `background-repeat` | repeat, repeat-x, repeat-y, no-repeat |
| `background-position` | center, top left, 50% 50%, 20px 30px |
| `background-size` | cover, contain, 100% 100%, 200px |
| `background-attachment` | scroll, fixed, local |
| `background-clip` | border-box, padding-box, content-box, text |
| `background-origin` | border-box, padding-box, content-box |

### Gradients

```css
background: linear-gradient(to right, red, blue);
background: linear-gradient(45deg, red, blue, green);
background: radial-gradient(circle, white, black);
background: conic-gradient(red 0deg 90deg, blue 90deg 180deg);
background: repeating-linear-gradient(45deg, #fff 0 10px, #eee 10px 20px);
```

---

## 9. Display

| Value | Behavior |
|-------|----------|
| `block` | New line, full width (default: div, p, h1-h6) |
| `inline` | Same line, content width (default: span, a) |
| `inline-block` | Same line, accepts width/height |
| `none` | Removed from layout |
| `flex` | Flex container |
| `inline-flex` | Inline flex container |
| `grid` | Grid container |
| `inline-grid` | Inline grid container |
| `contents` | Element's box disappears |
| `flow-root` | New block formatting context |
| `table`, `table-row`, `table-cell` | Behave like table |

`visibility: hidden` — invisible but takes space.
`opacity: 0` — transparent but interactive.

---

## 10. Position

| Value | Behavior |
|-------|----------|
| `static` | Default - normal flow |
| `relative` | Offset from normal position (space reserved) |
| `absolute` | Removed from flow, relative to nearest positioned ancestor |
| `fixed` | Removed from flow, relative to viewport |
| `sticky` | Starts in flow, becomes fixed when scrolled past |

### Properties

```css
position: relative;
top: 10px;
right: 20px;
bottom: 0;
left: 0;
inset: 0;              /* shorthand for all four = 0 */
z-index: 100;
```

`z-index` only works on positioned elements. Higher = on top.

---

## 11. Flexbox

```css
.container {
  display: flex;
  flex-direction: row | row-reverse | column | column-reverse;
  flex-wrap: nowrap | wrap | wrap-reverse;
  justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
  align-items: stretch | flex-start | flex-end | center | baseline;
  gap: 20px;
}

.item {
  flex: 1 1 200px;     /* grow shrink basis */
  flex-grow: 1;
  flex-shrink: 1;
  flex-basis: 200px;
  align-self: flex-start;
  order: 2;
}
```

### Centering Trick

```css
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}
```

### Sticky Footer

```css
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  margin: 0;
}
header, footer { flex-shrink: 0; }
main { flex: 1; }
```

---

## 12. Grid

```css
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-columns: repeat(3, 1fr);
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  grid-template-rows: 100px 1fr auto;
  grid-template-areas:
    "header header"
    "sidebar main"
    "footer footer";
  gap: 20px;
  justify-items: center;
  align-items: stretch;
}

.item {
  grid-column: 1 / 3;
  grid-row: 1;
  grid-column: span 2;
  grid-area: header;
}
```

---

## 13. Responsive Design

### Viewport Meta (in HTML)

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

### Relative Units

| Unit | Relative to |
|------|--------------|
| `rem` | Root font size (usually 16px) |
| `em` | Parent font size |
| `%` | Parent |
| `vw`, `vh` | 1% of viewport width/height |
| `vmin`, `vmax` | Smaller/larger viewport dim |
| `ch` | Width of "0" in current font |

### `clamp()`

```css
font-size: clamp(1rem, 2.5vw, 2rem);
```

### Media Queries

```css
@media (min-width: 768px) { /* tablet and up */ }
@media (max-width: 768px) { /* tablet and down */ }
@media (min-width: 768px) and (max-width: 1024px) { /* tablet */ }
@media (orientation: portrait) { ... }
@media (prefers-color-scheme: dark) { ... }
@media (prefers-reduced-motion: reduce) { ... }
```

### Standard Breakpoints

| Name | Width |
|------|-------|
| sm | 640px |
| md | 768px |
| lg | 1024px |
| xl | 1280px |
| 2xl | 1536px |

### Mobile-First Example

```css
/* Mobile styles (default) */
.container { padding: 0 10px; }

/* Tablet+ */
@media (min-width: 768px) {
  .container { padding: 0 20px; }
}

/* Desktop+ */
@media (min-width: 1024px) {
  .container { max-width: 1200px; margin: 0 auto; }
}
```

---

## 14. Transitions

```css
transition: <property> <duration> <timing-function> <delay>;

.btn {
  transition: background 0.3s ease, transform 0.2s ease-out;
}
```

Timing functions: `linear`, `ease`, `ease-in`, `ease-out`, `ease-in-out`, `cubic-bezier(a, b, c, d)`.

---

## 15. Animations

```css
@keyframes spin {
  from { transform: rotate(0deg); }
  to   { transform: rotate(360deg); }
}

.spinner {
  animation: spin 1s linear infinite;
}
```

### Shorthand

```css
animation: name duration timing-function delay iteration direction fill-mode play-state;
```

### `animation-fill-mode`

`none`, `forwards`, `backwards`, `both` — what happens before start and after end.

### `animation-direction`

`normal`, `reverse`, `alternate`, `alternate-reverse`.

### Common Keyframes

```css
@keyframes fadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}

@keyframes slideIn {
  from { transform: translateX(-100%); }
  to   { transform: translateX(0); }
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50%      { transform: translateY(-30px); }
}

@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50%      { transform: scale(1.1); }
}
```

---

## 16. Transform

```css
transform: translate(10px, 20px);  /* move */
transform: translateX(10px);
transform: translateY(20px);
transform: scale(1.5);              /* grow */
transform: rotate(45deg);          /* rotate */
transform: skew(10deg, 5deg);
transform: translate(10px, 0) scale(1.1) rotate(5deg);  /* combined */
```

---

## 17. Custom Properties (Variables)

```css
:root {
  --primary: #2c3e50;
  --text: #333;
  --bg: #f8f9fa;
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 16px;
}

body {
  background: var(--bg);
  color: var(--text);
  padding: var(--space-3);
}

h1 { color: var(--primary); }
```

Use `var(--name, fallback)` for fallbacks.

---

## 18. Common UI Patterns

### Button

```css
.btn {
  display: inline-block;
  padding: 10px 20px;
  background: #3498db;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1rem;
  transition: background 0.2s ease;
}
.btn:hover { background: #2980b9; }
```

### Card

```css
.card {
  background: white;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}
```

### Alert

```css
.alert {
  padding: 15px 20px;
  border-left: 4px solid #3498db;
  background: #e8f4fd;
  color: #1c5680;
  border-radius: 5px;
}
```

### Centered Modal

```css
.overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal {
  background: white;
  padding: 30px;
  border-radius: 8px;
  width: 400px;
  max-width: 90%;
}
```

---

## 19. CSS Specificity

| Selector | Specificity (B, C, D) |
|----------|------------------------|
| `*` | 0, 0, 0 |
| `p` | 0, 0, 1 |
| `.card` | 0, 1, 0 |
| `p.card` | 0, 1, 1 |
| `#main` | 1, 0, 0 |
| `#main .card` | 1, 1, 0 |

Higher score wins. Ties broken by source order (later wins). `!important` overrides everything.

---

## 20. Reduced Motion (Always Include)

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

---

## 21. CSS Rules to Live By

1. **Always set `box-sizing: border-box`** at the top of your stylesheet.
2. **Reset margins and padding** with `*`.
3. **Use `rem`** for font sizes (respects user preferences).
4. **Use `min-height`/`max-width`** instead of fixed `height`/`width`.
5. **Mobile-first** design: default = mobile, `min-width` queries for larger screens.
6. **Always include the viewport meta tag** in HTML.
7. **Animate performant properties** (`transform`, `opacity`, `filter`).
8. **Use `gap`** for flex/grid spacing, not margins.
9. **Always provide fallbacks**: font-family fallback, color fallback, etc.
10. **Use CSS variables** for colors, spacing, fonts — change once, update everywhere.
11. **`flex` for one-dimension, `grid` for two-dimensions.**
12. **Always include `prefers-reduced-motion`** media query.
13. **Never use `outline: none`** without providing an alternative focus style.
14. **Prefer semantic HTML over `<div>` and `<span>`** when applicable.
15. **Validate at [validator.w3.org](https://validator.w3.org/)** and check the CSS at [jigsaw.w3.org/css-validator](https://jigsaw.w3.org/css-validator/).

---

## 22. The 15 Properties You Will Use Most

If you could only learn 15 CSS properties, learn these. They appear on virtually every web page:

1. `color`, `background-color`
2. `font-family`, `font-size`, `line-height`
3. `margin`, `padding`
4. `border`, `border-radius`
5. `width`, `height`, `max-width`
6. `display`
7. `position`
8. `flex` / `flex-direction` / `justify-content` / `align-items`
9. `grid-template-columns`
10. `gap`
11. `transition`
12. `transform`
13. `box-shadow`
14. `text-align`
15. `:hover`, `:focus`

Master these, and you can build 95% of the visual styling of any modern website.

---

## CSS Module Complete

<p align="center">
  <img src="../logorepo.png" alt="Learn Dev Logo" width="100" height="100" />
</p>

<p align="center">
  <strong>Congratulations!</strong> You have completed the CSS module of Learn Dev.
</p>

<p align="center">
  You can now build beautiful, responsive, accessible web pages. What's next? <a href="../README.md">Check the roadmap</a> for upcoming modules.
</p>
