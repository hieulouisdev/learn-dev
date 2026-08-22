# Lesson 05: Text and Fonts

> Module: CSS · Lesson 5 of 16
> Estimated time: 45–60 minutes

Typography is one of the most impactful parts of web design. Most of your page is text — choosing the right font, size, line height, and spacing determines whether your page looks professional or amateurish. In this lesson, you'll learn everything you need to make text look beautiful.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use `font-family`, `font-size`, `font-weight`, and `font-style`.
2. Set line height, letter spacing, and word spacing.
3. Align, indent, decorate, and transform text.
4. Load custom web fonts with `@font-face` and Google Fonts.
5. Choose fonts that work well together.
6. Use the font shorthand property.

---

## 1. Font Families — `font-family`

The `font-family` property sets the font for text:

```css
body {
  font-family: Arial, sans-serif;
}
```

You can list multiple fonts separated by commas. The browser tries each one in order; if the first isn't available, it tries the next.

### Font Categories

- **Serif** — fonts with little feet on letters (Times New Roman, Georgia, Playfair Display). Traditional, formal.
- **Sans-serif** — fonts without feet (Arial, Helvetica, Roboto, Inter). Modern, clean.
- **Monospace** — equal-width characters (Courier New, Consolas, JetBrains Mono). For code.
- **Cursive** — cursive handwriting (Comic Sans, Brush Script).
- **Fantasy** — decorative fonts.

### Generic Family Fallbacks

Always end your font stack with a generic family:

```css
body { font-family: "Helvetica Neue", Arial, sans-serif; }
h1   { font-family: Georgia, "Times New Roman", serif; }
code { font-family: "JetBrains Mono", Consolas, monospace; }
```

If none of the named fonts are available, the browser falls back to its default sans-serif, serif, or monospace font.

### Quoting Font Names

Quote any font name that contains spaces or special characters:

```css
/* Quoted because of spaces */
font-family: "Times New Roman", "Helvetica Neue", sans-serif;

/* No quotes needed */
font-family: Arial, Georgia, monospace;
```

---

## 2. Font Size — `font-size`

```css
body { font-size: 16px; }
h1   { font-size: 2rem; }
.small { font-size: 0.875rem; }
```

### Units for Font Size

- **`px`** (pixels) — absolute. `16px` is 16 pixels regardless of user settings. Easy but ignores user preferences.
- **`em`** — relative to the parent's font size. `1.5em` means 1.5 times the parent's size. Compounds when nested.
- **`rem`** (root em) — relative to the root (`<html>`) font size. Doesn't compound. Best choice for general use.
- **`%`** — like `em`, relative to parent. `150%` means 1.5× parent.
- **`vw`, `vh`** — relative to viewport width/height. `5vw` means 5% of viewport width. Useful for huge hero text.

### Why Use `rem`?

Most browsers default to 16px. So `1rem = 16px` by default. If a user changes their browser's default font size (for accessibility), `rem` scales with them. `px` does not.

A common pattern:

```css
html { font-size: 16px; }   /* base */
h1   { font-size: 2rem; }   /* 32px */
h2   { font-size: 1.5rem; } /* 24px */
p    { font-size: 1rem; }   /* 16px */
small { font-size: 0.875rem; } /* 14px */
```

### Mobile Browsers and `px`

Some mobile browsers force a minimum font size of 16px to prevent unreadable text. Be careful: very small `px` sizes may render larger than expected on mobile.

---

## 3. Font Weight — `font-weight`

Controls how bold the text is:

```css
p { font-weight: 400; }   /* normal */
strong { font-weight: 700; }  /* bold */
```

Values:

- `100` — Thin
- `200` — Extra Light
- `300` — Light
- `400` — Normal (default)
- `500` — Medium
- `600` — Semibold
- `700` — Bold
- `800` — Extra Bold
- `900` — Black

Or use named values:

- `normal` — same as 400.
- `bold` — same as 700.
- `lighter` — one step lighter than parent.
- `bolder` — one step bolder than parent.

### Important: The Font Must Support the Weight

A font typically comes with multiple weights (e.g., Light 300, Regular 400, Bold 700). If you set `font-weight: 600` but the font only has 400 and 700, the browser will pick the closest one. You can't make text bolder than the font allows.

To use multiple weights, load each weight as a separate `@font-face` or Google Fonts request.

---

## 4. Font Style — `font-style`

```css
em { font-style: italic; }
.special { font-style: oblique; }
.normal { font-style: normal; }
```

- `italic` — uses the italic version of the font (true italics, slanted design).
- `oblique` — slants the regular font artificially (less elegant).
- `normal` — upright text.

Italic fonts are usually designed separately from the upright version. They look better than oblique.

---

## 5. Line Height — `line-height`

`line-height` controls vertical spacing between lines of text:

```css
p { line-height: 1.5; }
```

Values:

- Unitless number (recommended) — multiplier of font size. `1.5` means line height is 1.5× the font size.
- `px` — absolute line height. Less flexible.
- `%` — like a unitless number. `150%` = `1.5`.

### Recommended Line Heights

- **Body text**: 1.5 to 1.7. Comfortable reading.
- **Headings**: 1.1 to 1.3. Tighter for impact.
- **Code blocks**: 1.4 to 1.6.

```css
body { line-height: 1.6; }
h1 { line-height: 1.2; }
code { line-height: 1.4; }
```

Poor line height is the single biggest typography mistake beginners make. Make body text comfortable to read.

---

## 6. Letter and Word Spacing

### `letter-spacing`

```css
h1 { letter-spacing: 2px; }      /* letters spread out */
.tight { letter-spacing: -1px; }  /* letters pulled together */
```

Use sparingly — small adjustments have big effects. Good for uppercase headings and labels.

### `word-spacing`

```css
p { word-spacing: 4px; }
```

Adds space between words. Useful for justified text or wide, airy designs.

---

## 7. Text Alignment — `text-align`

```css
p { text-align: left; }       /* default for LTR text */
h1 { text-align: center; }
.quote { text-align: right; }
.justified { text-align: justify; }
```

Values:
- `left`, `right`, `center`, `justify`.
- `start`, `end` — for internationalization (left in LTR languages, right in RTL).

### Don't Use `justify` Casually

Justified text (text aligned to both left and right edges) can create ugly "rivers" of white space between words on narrow screens. Use it sparingly — mainly for printed material or carefully typeset articles.

---

## 8. Text Indentation — `text-indent`

```css
p { text-indent: 20px; }      /* first line indented */
```

Indents only the first line of each paragraph. Useful for essays and articles.

---

## 9. Text Decoration — `text-decoration`

```css
a { text-decoration: none; }    /* remove underline */
a:hover { text-decoration: underline; }
.strike { text-decoration: line-through; }
.overline { text-decoration: overline; }
.under-over { text-decoration: underline overline; }
```

The `text-decoration` shorthand can take multiple values: `underline overline line-through` all at once.

### Color and Style

```css
a {
  text-decoration: underline;
  text-decoration-color: red;
  text-decoration-style: wavy;  /* solid, double, dotted, dashed, wavy */
  text-decoration-thickness: 2px;
}
```

Modern CSS gives you full control over the decoration.

---

## 10. Text Transform — `text-transform`

```css
.upper { text-transform: uppercase; }
.lower { text-transform: lowercase; }
.capital { text-transform: capitalize; }  /* first letter of each word */
.normal { text-transform: none; }
```

Changes the case of the rendered text without changing the HTML source. Great for headings, labels, and badges.

---

## 11. Text Shadow — `text-shadow`

```css
h1 {
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}
```

Syntax: `text-shadow: <horizontal> <vertical> <blur> <color>;`

- Horizontal offset (positive = right, negative = left).
- Vertical offset (positive = down, negative = up).
- Blur radius (0 = sharp, larger = blurrier).
- Color.

Multiple shadows:

```css
h1 {
  text-shadow:
    1px 1px 0 #fff,
    2px 2px 4px rgba(0, 0, 0, 0.5);
}
```

Use sparingly — text shadows can look amateur if overused.

---

## 12. White Space — `white-space`

Controls how whitespace and line breaks in the source are rendered:

```css
pre, code { white-space: pre; }       /* preserve all whitespace */
.nowrap { white-space: nowrap; }     /* never wrap, may overflow */
.pre-wrap { white-space: pre-wrap; } /* preserve whitespace, wrap long lines */
```

- `normal` (default) — collapse whitespace, wrap on spaces.
- `nowrap` — never wrap. May cause horizontal overflow.
- `pre` — preserve whitespace, don't wrap.
- `pre-wrap` — preserve whitespace, wrap long lines.
- `pre-line` — collapse spaces but preserve newlines.

---

## 13. Loading Custom Web Fonts

### Method 1: Google Fonts (Easiest)

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&family=Playfair+Display:wght@400;700&display=swap" rel="stylesheet">
```

Then use in CSS:

```css
body { font-family: "Inter", sans-serif; }
h1 { font-family: "Playfair Display", serif; }
```

Visit [fonts.google.com](https://fonts.google.com) to pick fonts and get the exact `<link>` tags.

### Method 2: `@font-face` (Self-Hosted)

```css
@font-face {
  font-family: "MyFont";
  src: url("fonts/MyFont-Regular.woff2") format("woff2"),
       url("fonts/MyFont-Regular.woff") format("woff");
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: "MyFont";
  src: url("fonts/MyFont-Bold.woff2") format("woff2");
  font-weight: 700;
  font-style: normal;
  font-display: swap;
}

body { font-family: "MyFont", sans-serif; }
```

Self-hosting is more private (no third-party requests) and lets you use any font, including paid ones.

### Font Formats

- **WOFF2** — modern, smallest. Use this.
- **WOFF** — older, slightly larger. Fallback.
- **TTF / OTF** — desktop formats. Avoid for web.
- **EOT** — ancient Internet Explorer. Don't bother.

### `font-display: swap`

```css
font-display: swap;
```

This tells the browser: "if the web font hasn't loaded yet, show a fallback font, then swap to the web font when it arrives." Prevents the "invisible text" problem during slow loads.

---

## 14. The `font` Shorthand

You can set many font properties at once:

```css
p {
  font: italic bold 16px/1.5 "Inter", sans-serif;
}
```

Format: `font-style font-variant font-weight font-size/line-height font-family;`

- `font-style` (optional)
- `font-variant` (optional)
- `font-weight` (optional)
- `font-size` (required)
- `line-height` (optional, after `/`)
- `font-family` (required)

```css
h1 { font: 700 2rem/1.2 "Playfair Display", serif; }
p  { font: 400 16px/1.6 "Inter", sans-serif; }
em { font: italic 400 1em "Inter", sans-serif; }
```

Use the shorthand for concise code. Be careful — the shorthand resets unspecified properties to defaults.

---

## 15. Choosing Fonts That Work Together

A simple, foolproof rule: **use one serif + one sans-serif**, or **one heading font + one body font**.

### Classic Pairings

- Headings: Playfair Display (serif). Body: Source Sans Pro (sans-serif).
- Headings: Montserrat (sans-serif). Body: Lora (serif).
- Headings: Inter (sans-serif). Body: Inter (sans-serif) at lighter weight.

### Font Recommendation Sites

- [Google Fonts pairings](https://fonts.google.com)
- [Typ.io](https://typ.io)
- [Typespiration](https://typespiration.com)

### My Recommendations for Beginners

If you're not sure, use these — they look great and are widely supported:

- **Body**: Inter, Source Sans Pro, Open Sans, or Roboto.
- **Headings**: same as body but bolder, or a contrasting serif like Playfair Display.
- **Code**: JetBrains Mono, Fira Code, or Source Code Pro.

---

## 16. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Typography Demo</title>

    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&family=Playfair+Display:wght@700&display=swap" rel="stylesheet">

    <link rel="stylesheet" href="typography.css">
  </head>
  <body>
    <article>
      <header>
        <h1>The Art of Typography</h1>
        <p class="subtitle">A short guide for beginners</p>
      </header>

      <p class="lead">
        Typography is the foundation of web design. Most of your users
        will <em>read</em> your content, even if they don't notice it.
        Good typography is invisible; bad typography is impossible to ignore.
      </p>

      <p>
        Choose a body font that is comfortable at <strong>16 pixels</strong>
        with a <strong>line height of 1.5 or higher</strong>. Sans-serif
        fonts like Inter work well on screens; serif fonts like Georgia
        work well for body text in long articles.
      </p>

      <h2>Code Snippets</h2>
      <p>Use a monospace font for code:</p>
      <pre><code>function greet(name) {
  console.log("Hello, " + name);
}</code></pre>

      <h2>Headings</h2>
      <p>
        Use a contrasting heading font. <span class="caps">All caps</span>
        can work for labels and badges — add letter spacing.
      </p>

      <blockquote>
        <p>
          Good typography should be invisible. When people read,
          they should not notice the typography at all.
        </p>
        <footer>— Anonymous Designer</footer>
      </blockquote>
    </article>
  </body>
</html>
```

```css
/* typography.css */

:root {
  --body-font: "Inter", -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  --heading-font: "Playfair Display", Georgia, serif;
  --code-font: "JetBrains Mono", Consolas, monospace;

  --text-color: #2c3e50;
  --muted: #6c7a89;
  --accent: #3498db;
}

body {
  font-family: var(--body-font);
  font-size: 1rem;
  line-height: 1.7;
  color: var(--text-color);
  background: #fafafa;
  margin: 0;
  padding: 40px 20px;
}

article {
  max-width: 700px;
  margin: 0 auto;
  background: white;
  padding: 40px 50px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

h1 {
  font-family: var(--heading-font);
  font-size: 2.5rem;
  font-weight: 700;
  line-height: 1.2;
  color: var(--text-color);
  margin: 0 0 5px 0;
}

.subtitle {
  font-size: 1rem;
  color: var(--muted);
  font-style: italic;
  margin: 0 0 30px 0;
}

h2 {
  font-family: var(--heading-font);
  font-size: 1.5rem;
  font-weight: 700;
  margin-top: 30px;
  color: var(--text-color);
}

.lead {
  font-size: 1.2rem;
  color: var(--text-color);
  font-weight: 500;
  line-height: 1.5;
}

strong { font-weight: 700; }
em { font-style: italic; }

pre {
  background: #f4f4f4;
  padding: 15px;
  border-radius: 5px;
  overflow-x: auto;
  font-family: var(--code-font);
  font-size: 0.9rem;
  line-height: 1.4;
}

code {
  font-family: var(--code-font);
}

.caps {
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 600;
}

blockquote {
  border-left: 4px solid var(--accent);
  padding-left: 20px;
  margin: 30px 0;
  font-style: italic;
  color: var(--muted);
}

blockquote footer {
  font-size: 0.9rem;
  margin-top: 10px;
  font-style: normal;
}
```

Save both files and open in a browser. You'll see a beautiful article layout with custom fonts.

---

## Try It Yourself

### Exercise 1: Pair Two Fonts

Pick one heading font and one body font from Google Fonts. Apply them to an article page. Adjust sizes and line heights until it looks good.

### Exercise 2: Style a Code Block

Style a `<pre><code>` block with:

- Monospace font.
- Background color.
- Padding.
- Rounded corners.
- Scrollable on overflow.

### Exercise 3: Build a Blockquote

Style a `<blockquote>` with:

- Left border accent.
- Italic text.
- Larger font size.
- The author's name styled differently (smaller, normal style, accent color).

---

## Common Mistakes

### Mistake 1: Forgetting font fallbacks

```css
/* BAD */
body { font-family: "Inter"; }
```

If "Inter" doesn't load (slow internet, font service down), the browser uses its default. Always include a generic family:

```css
body { font-family: "Inter", sans-serif; }
```

### Mistake 2: Tiny body text

```css
/* BAD: 12px body text is hard to read */
p { font-size: 12px; }
```

Body text should be at least 16px. Don't make users squint.

### Mistake 3: Tight line height on body text

```css
/* BAD */
p { line-height: 1; }   /* lines overlap */
```

Use 1.5 or higher for body text.

### Mistake 4: Too many fonts

Loading 5+ fonts slows your page dramatically. Use 2-3 fonts max. Use multiple weights of the same family instead.

### Mistake 5: Quoting font names that don't need it

```css
/* Unnecessary but harmless */
font-family: "Arial", "sans-serif";
```

Single-word font names and generic families don't need quotes. It works, but it's noise.

### Mistake 6: Forgetting `font-display: swap`

Without it, web fonts can be invisible during slow loads, causing layout shifts. Always set `font-display: swap` on `@font-face` declarations.

---

## Summary

- Use `font-family` with multiple fallbacks ending in a generic family (`sans-serif`, `serif`, `monospace`).
- Use `rem` for font sizes — it respects user accessibility settings.
- Common weights: 400 (normal), 700 (bold). The font must support the weight.
- `line-height: 1.5` or higher for body text. Tighter for headings.
- `text-align` for alignment; avoid `justify` on the web unless carefully typeset.
- `text-decoration: none` removes underlines; modern CSS supports color, style, thickness.
- `text-transform: uppercase` capitalizes without changing source.
- Load custom fonts via Google Fonts `<link>` or `@font-face`. Use `font-display: swap`.
- The `font` shorthand sets multiple properties at once.
- Pair 2 fonts: one for headings, one for body. Sans-serif body + serif heading is a classic combo.

In the next lesson, we'll cover the most important CSS concept of all: **the box model**. Every element on a page is a box, and understanding that box is the foundation of all CSS layout.

---

**Next:** [Lesson 06: The Box Model →](./06-the-box-model.md)
