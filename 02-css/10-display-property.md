# Lesson 10: The `display` Property

> Module: CSS · Lesson 10 of 16
> Estimated time: 45–60 minutes

The `display` property is the master switch of CSS. It determines **how an element flows** on the page — whether it starts on a new line, whether it takes a width, whether it's visible at all. Understanding `display` is essential for building any layout.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain the difference between block, inline, and inline-block elements.
2. Use `display: none` to hide elements.
3. Use `display: flex` and `display: grid` (we'll cover them in depth in later lessons).
4. Understand `display: table` for legacy layouts.
5. Override default display values for any element.
6. Build navigation menus, badges, and grids with the right display value.

---

## 1. The Three Most Common Display Values

### Block — `display: block`

A block element:
- Starts on a new line.
- Takes the full available width by default.
- Respects `width`, `height`, `margin`, `padding` on all sides.

Default block elements: `<div>`, `<p>`, `<h1>`–`<h6>`, `<ul>`, `<ol>`, `<table>`, `<form>`, `<section>`, `<article>`, `<header>`, `<footer>`, `<nav>`, `<main>`.

```css
div { display: block; }   /* default for div */
```

### Inline — `display: inline`

An inline element:
- Does **not** start a new line.
- Takes only as much width as its content.
- Ignores `width`, `height`, and vertical margin/padding (mostly).

Default inline elements: `<span>`, `<a>`, `<strong>`, `<em>`, `<code>`, `<img>`, `<br>`.

```css
span { display: inline; }   /* default for span */
```

### Inline-Block — `display: inline-block`

Combines both:
- Flows inline (no new line).
- Accepts `width`, `height`, `margin`, `padding` on all sides.

```css
.badge {
  display: inline-block;
  width: 100px;
  height: 30px;
  padding: 5px;
  background: gold;
  text-align: center;
}
```

Default inline-block elements: `<img>`, `<input>`, `<button>`.

---

## 2. Changing an Element's Display

You can override the default display of any element.

### Make an Inline Element Block

```css
a { display: block; }   /* links become block-level, take full width */
```

Useful for navigation links that should fill their container:

```css
nav a {
  display: block;
  padding: 10px 20px;
}
```

### Make a Block Element Inline

```css
div { display: inline; }  /* divs no longer break to a new line */
```

Rare. Usually you want inline-block instead.

### Make a Block Element Inline-Block

```css
.card { display: inline-block; width: 300px; }
```

Cards now sit side by side on the same line (if there's room) instead of stacking.

### Hide an Element Completely

```css
.hidden { display: none; }
```

The element is removed from the page flow entirely — it's as if it never existed. No space is reserved for it.

---

## 3. The Difference Between `display: none` and `visibility: hidden`

```css
.gone     { display: none; }       /* removes from layout, no space */
.invisible { visibility: hidden; } /* hides, but space is still reserved */
```

- `display: none`: element is gone, no space, no rendering.
- `visibility: hidden`: element is invisible but still takes up space.

Use `display: none` when the element shouldn't affect layout at all.
Use `visibility: hidden` when you want the element to disappear but keep its place (e.g., a placeholder).

### `opacity: 0` — A Third Option

```css
.transparent { opacity: 0; }
```

`opacity: 0` makes the element fully transparent but it's still there — you can interact with it, it still takes space.

---

## 4. Common Display Patterns

### Navigation Menu (Horizontal)

Make list items sit side by side:

```css
nav ul { list-style: none; padding: 0; }
nav li { display: inline-block; margin-right: 10px; }
nav a { display: block; padding: 8px 15px; }
```

The list items are inline-block (sit on one row). The links inside are block (fill the li, so the entire padded area is clickable).

### Badge

```css
.badge {
  display: inline-block;
  padding: 2px 8px;
  background: gold;
  color: black;
  font-size: 0.8em;
  border-radius: 12px;
}
```

Sits inline with text but can have padding and a background.

### Cards Side by Side

```css
.card {
  display: inline-block;
  width: 200px;
  vertical-align: top;
  margin: 10px;
  padding: 20px;
  background: white;
  border: 1px solid #ddd;
  vertical-align: top;
}
```

Note `vertical-align: top` — important when using inline-block for cards. Without it, items align to the baseline of the text inside them, which looks weird.

---

## 5. The Inline-Block Whitespace Problem

When you put inline-block elements next to each other in HTML:

```html
<div class="card">A</div>
<div class="card">B</div>
<div class="card">C</div>
```

There's whitespace between the `</div>` and the next `<div>` (the line break in your source code). The browser renders this as a small space — usually about 4px — between the cards.

This causes off-by-a-few-pixels layout bugs that drive beginners crazy.

### Solutions

**Solution 1: Put all elements on one line**

```html
<div class="card">A</div><div class="card">B</div><div class="card">C</div>
```

No whitespace, no gap. But it's ugly in source code.

**Solution 2: Use comments to fill whitespace**

```html
<div class="card">A</div><!--
--><div class="card">B</div>
```

The comment "absorbs" the whitespace.

**Solution 3: Set font-size: 0 on the parent**

```css
.cards-container { font-size: 0; }
.card { font-size: 16px; }
```

Whitespace has no width when font-size is 0. Reset the font-size on the children.

**Solution 4: Use flexbox (recommended)**

```css
.cards-container { display: flex; gap: 10px; }
```

Flexbox handles this automatically. We'll cover it in Lesson 12.

For modern development, **prefer flexbox or grid over inline-block** for layout. Inline-block has too many edge cases.

---

## 6. Display Values Summary Table

| Value | Behavior | Common Use |
|-------|----------|-------------|
| `block` | New line, full width | Default for div, p, h1-h6 |
| `inline` | Same line, content width | Default for span, a, strong |
| `inline-block` | Same line, accepts width/height | Badges, small UI elements |
| `none` | Removed from layout | Hiding elements |
| `flex` | Flexible one-dimensional layout | Modern layouts |
| `inline-flex` | Flex, but flows inline | Inline flexible elements |
| `grid` | Two-dimensional layout | Complex page structures |
| `inline-grid` | Grid, but flows inline | Inline complex elements |
| `table`, `table-row`, `table-cell` | Behaves like a table | Legacy, email templates |
| `list-item` | Behaves like `<li>` | Custom list items |
| `contents` | Element's box disappears, children participate in parent's layout | Advanced |
| `flow-root` | Creates a new block formatting context | Clearing floats |

---

## 7. `display: contents`

A modern, somewhat unusual value:

```html
<div class="wrapper">
  <span class="button">Click me</span>
</div>
```

```css
.wrapper { display: contents; }
```

With `display: contents`, the `.wrapper` box itself disappears, but its children (the span) behave as if they were direct children of `.wrapper`'s parent. This is useful for semantic wrappers that shouldn't affect layout.

This is advanced — be careful, it has accessibility implications in some browsers.

---

## 8. `display: flow-root`

A modern fix for clearfix:

```css
.parent { display: flow-root; }
```

This creates a new block formatting context, which means floated children are contained. We'll see this when we cover floats (a legacy layout technique).

For modern layouts, prefer flexbox or grid over floats.

---

## 9. Default Display Values of Common Elements

| Element | Default Display |
|---------|-----------------|
| `<div>` | block |
| `<p>` | block |
| `<h1>`–`<h6>` | block |
| `<ul>`, `<ol>`, `<li>` | list-item (similar to block) |
| `<table>` | table |
| `<tr>` | table-row |
| `<td>`, `<th>` | table-cell |
| `<form>` | block |
| `<header>`, `<main>`, `<footer>`, `<nav>`, `<section>`, `<article>` | block |
| `<span>` | inline |
| `<a>` | inline |
| `<strong>`, `<em>`, `<code>` | inline |
| `<img>` | inline (behaves like inline-block) |
| `<input>`, `<button>` | inline-block |
| `<br>` | inline |
| `<label>` | inline |

---

## 10. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Display Demo</title>
    <link rel="stylesheet" href="display.css">
  </head>
  <body>
    <h1>Display Demo</h1>

    <h2>Block vs Inline vs Inline-Block</h2>
    <p>This is a paragraph (block). It starts on a new line and fills the width.</p>
    <p>Another paragraph. Notice the spacing.</p>

    <p>
      Inside this paragraph, we have <span class="inline">inline span</span>,
      <span class="inline">another inline span</span>, and
      <span class="inline-block">inline-block span</span>.
    </p>

    <h2>Hidden Elements</h2>
    <p>This paragraph is visible.</p>
    <p class="hidden">This paragraph is hidden (display: none).</p>
    <p>This paragraph is visible too. Notice no gap where the hidden one would be.</p>

    <p class="invisible">This paragraph is invisible (visibility: hidden) but takes space.</p>
    <p>This paragraph comes after. Notice the gap above.</p>

    <h2>Horizontal Navigation (Inline-Block)</h2>
    <nav class="nav">
      <ul>
        <li><a href="#">Home</a></li>
        <li><a href="#">About</a></li>
        <li><a href="#">Blog</a></li>
        <li><a href="#">Contact</a></li>
      </ul>
    </nav>

    <h2>Cards Side by Side</h2>
    <div class="cards">
      <div class="card">
        <h3>Card 1</h3>
        <p>Some text inside the first card.</p>
      </div>
      <div class="card">
        <h3>Card 2</h3>
        <p>Some text inside the second card.</p>
      </div>
      <div class="card">
        <h3>Card 3</h3>
        <p>Some text inside the third card.</p>
      </div>
    </div>
  </body>
</html>
```

```css
/* display.css */

body {
  font-family: Arial, sans-serif;
  padding: 30px;
  max-width: 900px;
  margin: 0 auto;
  line-height: 1.6;
  color: #333;
}

h1, h2 { color: #2c3e50; }

.inline {
  background: #ffeeba;
  padding: 2px 4px;
}

.inline-block {
  display: inline-block;
  background: #bde0fe;
  padding: 5px 10px;
  width: 100px;
  text-align: center;
}

.hidden { display: none; }
.invisible { visibility: hidden; }

.nav ul {
  list-style: none;
  padding: 0;
  background: #2c3e50;
  padding: 10px;
}

.nav li {
  display: inline-block;
  margin-right: 5px;
}

.nav a {
  display: block;
  padding: 10px 15px;
  color: white;
  text-decoration: none;
  border-radius: 4px;
}

.nav a:hover { background: #34495e; }

.cards {
  font-size: 0;   /* fix whitespace between inline-block items */
}

.card {
  display: inline-block;
  width: 30%;
  vertical-align: top;
  margin-right: 3%;
  background: white;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 16px;   /* restore font-size */
  box-sizing: border-box;
}

.card:last-child { margin-right: 0; }

.card h3 { margin-top: 0; color: #2c3e50; }
```

Save and open. Note the navigation menu (inline-block list items), the cards side by side, and the difference between `display: none` and `visibility: hidden`.

---

## Try It Yourself

### Exercise 1: Inline to Block

Make all the links in a paragraph start on new lines by adding `display: block`. Notice how they each take their own line.

### Exercise 2: Hidden vs Invisible

Create three paragraphs. Hide one with `display: none`, make another invisible with `visibility: hidden`, leave the third alone. Compare the layout.

### Exercise 3: Horizontal Menu

Build a horizontal navigation menu using `display: inline-block` on list items. Make each link fill its container with `display: block`.

### Exercise 4: Three Cards

Build three cards side by side using inline-block. Add `vertical-align: top` and `font-size: 0` on the parent to handle whitespace.

---

## Common Mistakes

### Mistake 1: Setting `width`/`height` on an inline element

```css
/* WON'T WORK */
span {
  display: inline;
  width: 200px;   /* ignored */
  height: 50px;   /* ignored */
}
```

Inline elements ignore width and height. Use `display: inline-block` or `display: block` first.

### Mistake 2: Forgetting `vertical-align: top` on inline-block cards

```css
/* Cards align to baseline of inner text - looks weird */
.card { display: inline-block; }
```

Always add `vertical-align: top` (or `middle`) when using inline-block for cards.

### Mistake 3: Whitespace gaps between inline-block elements

```html
<div class="card">A</div>
<div class="card">B</div>
```

The line break creates a small visible gap. Use flexbox instead, or use the `font-size: 0` trick.

### Mistake 4: Using `display: none` when you should use `visibility: hidden`

If you want the element to disappear but keep its space (like a placeholder), use `visibility: hidden`. If you want it completely gone, use `display: none`.

### Mistake 5: Overriding display without understanding the original

```css
/* BAD: making a span block but not understanding why */
span { display: block; }
```

Think about why you're changing display. Is the default wrong for this element? Or are you using the wrong element to begin with?

### Mistake 6: Confusing `display: none` with `aria-hidden`

`display: none` hides from everyone (including screen readers). `aria-hidden="true"` hides from screen readers but keeps visible for sighted users. Use the right tool.

### Mistake 7: Forgetting to reset `font-size: 0` children

```css
.parent { font-size: 0; }
/* child elements inherit font-size: 0 - text becomes invisible */
.child { /* need to set font-size: 16px */ }
```

---

## Summary

- `display` controls how an element flows on the page.
- `block`: new line, full width. Default for div, p, h1-h6.
- `inline`: same line, content width. Default for span, a, strong.
- `inline-block`: same line, accepts width/height. Best for badges, small UI.
- `none`: removes from layout entirely (no space).
- `visibility: hidden`: invisible but takes space.
- `opacity: 0`: transparent but interactive.
- `flex` and `grid`: modern layout systems (covered in Lessons 12 and 13).
- Inline-block has whitespace gaps between elements — use flexbox instead.
- Use `vertical-align: top` with inline-block.
- Override default display values to fit your needs.

In the next lesson, we'll cover CSS positioning — `static`, `relative`, `absolute`, `fixed`, and `sticky`.

---

**Next:** [Lesson 11: Position →](./11-position.md)
