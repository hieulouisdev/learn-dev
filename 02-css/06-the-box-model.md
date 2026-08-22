# Lesson 06: The Box Model

> Module: CSS · Lesson 6 of 16
> Estimated time: 60–90 minutes

**This is the single most important lesson in the entire CSS module.** Every element on a web page is a rectangular box, and CSS gives you precise control over that box's dimensions, padding, border, and margin. Master the box model, and you've mastered 80% of CSS.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain the four parts of every CSS box: content, padding, border, margin.
2. Calculate the actual rendered size of an element.
3. Use `box-sizing: border-box` to make sizes predictable.
4. Use `width`, `height`, `min-width`, `max-width`, `min-height`, `max-height`.
5. Apply background and border to elements.
6. Debug box model issues with the browser DevTools.

---

## 1. Every Element is a Box

Open any web page, right-click, and select "Inspect Element" (or press `F12`). Click any element in the Elements panel. At the bottom of the Styles sidebar, you'll see a colorful diagram like this:

```
              margin
            ┌───────────────┐
            │     border    │
            │   ┌─────────┐ │
            │   │ padding │ │
            │   │  ┌────┐ │ │
            │   │  │    │ │ │
margin ←──  │   │  │CONT│ │ │  ──→ margin
            │   │  │ENT │ │ │
            │   │  └────┘ │ │
            │   └─────────┘ │
            └───────────────┘
```

This diagram shows the four layers of every CSS box, from inside out:

1. **Content** — the text, image, or other content inside the element.
2. **Padding** — space between the content and the border.
3. **Border** — a line around the padding.
4. **Margin** — space outside the border, between this element and its neighbors.

Let's examine each.

---

## 2. Content — Width and Height

The content area is the innermost box. Its size is set by `width` and `height`:

```css
.box {
  width: 200px;
  height: 100px;
  background: lightblue;
}
```

This creates a 200×100 pixel box. If there's no content, you'll just see the colored box.

### Percentage Widths

```css
.box {
  width: 50%;
  background: lightblue;
}
```

`50%` means 50% of the **parent's width**. So a `<div class="box">` inside a `<body>` that's 1000px wide will be 500px wide.

### Auto Width

The default `width` for block elements is `auto`, which means "fill all available width." A `<div>` without a width takes the full width of its parent.

### `min-width` and `max-width`

```css
.box {
  width: 50%;
  min-width: 200px;
  max-width: 500px;
}
```

The box tries to be 50% of the parent. If 50% is less than 200px, it becomes 200px. If 50% is more than 500px, it becomes 500px. This is the foundation of responsive design.

### Height Is Tricky

Avoid setting fixed heights on text-containing elements. If you set `height: 50px` and the text is longer, it overflows. Use `min-height` instead:

```css
.card {
  min-height: 200px;  /* at least 200px tall */
  /* will grow if content is taller */
}
```

---

## 3. Padding — Inner Space

Padding is space between the content and the border. It's part of the element (it has the element's background).

```css
.box {
  width: 200px;
  height: 100px;
  padding: 20px;
  background: lightblue;
}
```

This adds 20px of padding on all four sides. The content is 200×100... but wait, see below.

### Per-Side Padding

```css
.box {
  padding-top: 20px;
  padding-right: 15px;
  padding-bottom: 20px;
  padding-left: 15px;
}
```

Or use the shorthand:

```css
.box { padding: 20px 15px 20px 15px; }   /* top right bottom left (clockwise from top) */
.box { padding: 20px 15px; }              /* top/bottom 20px, left/right 15px */
.box { padding: 20px 15px 30px; }        /* top 20, sides 15, bottom 30 */
.box { padding: 20px; }                  /* all four sides 20px */
```

The shorthand order: **T R B L** ("Top Right Bottom Left" — like a clock).

Two-value shorthand: `padding: A B` means top/bottom = A, left/right = B.

Three-value shorthand: `padding: A B C` means top = A, sides = B, bottom = C.

### Padding Affects Visible Size

By default, padding **adds to** the width and height. A 200px-wide box with 20px padding is actually **240px wide** rendered (200 content + 20 padding left + 20 padding right).

This is surprising and is the source of countless beginner bugs. See section 5 below for the fix.

---

## 4. Border — Around the Element

A border is a line around the element:

```css
.box {
  border: 2px solid black;
}
```

Three parts to a border: width, style, color.

### Border Styles

```css
border-style: solid;
border-style: dashed;
border-style: dotted;
border-style: double;
border-style: groove;
border-style: ridge;
border-style: inset;
border-style: outset;
border-style: none;       /* no border, default */
border-style: hidden;     /* like none but stronger for tables */
```

### Border Shorthand

```css
border: 1px solid #ccc;
border: 2px dashed red;
border: thick double blue;
```

### Per-Side Borders

```css
border-top: 2px solid red;
border-right: 1px solid blue;
border-bottom: 2px solid green;
border-left: 1px solid yellow;
```

### Individual Properties

```css
border-width: 2px;
border-style: solid;
border-color: black;
```

### Rounded Corners — `border-radius`

```css
.box {
  border-radius: 8px;          /* all corners */
  border-radius: 8px 4px;       /* TL/BR 8, TR/BL 4 */
  border-radius: 8px 4px 16px; /* TL 8, TR/BL 4, BR 16 */
  border-radius: 50%;           /* makes a square element a circle */
}
```

Perfect for cards, buttons, and avatars.

### Border Affects Size Too

Like padding, the border adds to the rendered size by default. A 200px box with 2px borders is **204px** wide.

---

## 5. Margin — Outer Space

Margin is space **outside** the border, between this element and others. It's transparent.

```css
.box {
  margin: 20px;
}
```

### Per-Side Margins

```css
.box {
  margin-top: 20px;
  margin-right: 15px;
  margin-bottom: 20px;
  margin-left: 15px;
}

/* Or with the same shorthand as padding */
.box { margin: 20px 15px 20px 15px; }  /* T R B L */
.box { margin: 20px 15px; }            /* TB 20, LR 15 */
```

### `margin: auto` — Centering

```css
.container {
  width: 500px;
  margin: 0 auto;  /* top/bottom 0, left/right auto */
}
```

Setting left and right margins to `auto` **centers** the element horizontally within its parent. This only works on elements with a defined width.

### Negative Margins

```css
.box {
  margin-top: -20px;  /* moves the box up by 20px */
}
```

Negative margins pull elements together or overlap them. Useful but advanced — be careful.

### Margin Collapsing

When two block elements with vertical margins are stacked, their margins **collapse** (merge) — only the larger of the two margins is applied.

```html
<div style="margin-bottom: 30px;">Box 1</div>
<div style="margin-top: 20px;">Box 2</div>
```

The gap between them is **30px**, not 50px. The 20px margin collapses into the 30px.

This is one of the most confusing parts of CSS for beginners. Just remember: **vertical margins between sibling block elements collapse to the larger value.**

Margins don't collapse:
- Horizontally (left/right margins always add up).
- Inside flex or grid containers.
- When using padding or borders between elements.

---

## 6. The Total Width Problem (and How to Fix It)

By default, the `width` and `height` you set only apply to the **content**. Padding and border add to that. So:

```css
.box {
  width: 200px;
  padding: 20px;
  border: 2px solid black;
}
```

The actual rendered width is **244px** (200 content + 20 padding-left + 20 padding-right + 2 border-left + 2 border-right).

This is annoying. You think you're making a 200px box, but you get 244px. Layouts break. You pull your hair out.

### The Fix: `box-sizing: border-box`

```css
* {
  box-sizing: border-box;
}
```

With `border-box`, the width you set includes padding and border. So a `width: 200px` box with 20px padding and 2px border has a **total width of 200px**. The content area shrinks to fit.

This is the standard professional approach. Every modern project starts with this rule:

```css
* {
  box-sizing: border-box;
}
```

Or, more thoroughly:

```css
*, *::before, *::after {
  box-sizing: border-box;
}
```

Always include this at the top of your stylesheet. It will save you from countless layout bugs.

---

## 7. The CSS Reset

A common pattern is to also reset margins and padding to zero on all elements, removing browser defaults:

```css
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
```

This gives you a clean slate. Be warned: this also removes default list bullets, heading sizes, and other browser conveniences. You'll need to add them back manually.

A more gentle alternative is **Normalize.css** or **modern-normalize**, which preserves useful defaults while smoothing out inconsistencies.

---

## 8. Outline — Like Border, But Different

```css
.box {
  outline: 2px solid red;
  outline-offset: 4px;
}
```

- `outline` draws a line around the element **outside** the border.
- `outline` does **not** take up space — it doesn't affect layout.
- `outline-offset` adds space between the border and the outline.

Use `outline` for focus indicators (`:focus`) since it doesn't push the layout around:

```css
button:focus {
  outline: 3px solid blue;
  outline-offset: 2px;
}
```

Never use `outline: none` without providing an alternative — it removes keyboard navigation accessibility.

---

## 9. Background — Color, Image, Gradient

```css
.box {
  background: lightblue;
  background: #2c3e50;
  background: url("photo.jpg");
  background: linear-gradient(to right, red, blue);
}
```

We'll cover backgrounds in depth in Lesson 09. For now: the background fills the content + padding area (and extends under the border by default).

---

## 10. Inline Elements and the Box Model

Inline elements (`<span>`, `<a>`, `<strong>`) respect the box model differently:

- `width` and `height` **do not apply** to inline elements.
- Vertical `margin` and `padding` apply visually but **don't take up space** (they overlap with neighbors).
- Horizontal `margin` and `padding` work normally.

If you need to set width/height on an inline element, use `display: inline-block` or `display: block`.

```css
.badge {
  display: inline-block;
  width: 100px;
  padding: 5px 10px;
  background: gold;
  text-align: center;
}
```

We'll cover `display` in Lesson 10.

---

## 11. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Box Model Demo</title>
    <link rel="stylesheet" href="box-model.css">
  </head>
  <body>
    <div class="container">
      <div class="card">
        <h2>Box Model</h2>
        <p>Notice the padding inside and margin outside.</p>
      </div>
      <div class="card">
        <h2>Another Card</h2>
        <p>Each card has padding, border, and margin.</p>
      </div>
    </div>
  </body>
</html>
```

```css
/* box-model.css */

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: Arial, sans-serif;
  background: #f0f0f0;
  padding: 30px;
}

.container {
  max-width: 800px;
  margin: 0 auto;
}

.card {
  background: white;
  padding: 30px;
  margin-bottom: 20px;
  border: 2px solid #2c3e50;
  border-radius: 8px;
}

.card h2 {
  margin-bottom: 10px;
  color: #2c3e50;
}

.card p {
  line-height: 1.6;
  color: #555;
}

/* Demonstrate: with box-sizing: border-box, the card's width stays 800px
   even with padding and border. Try changing box-sizing to content-box
   and see what happens. */
```

Save and open in browser. Open DevTools (`F12`), click on a `.card`, and look at the box model diagram. You'll see content, padding, border, and margin visualized clearly.

---

## 12. Inspecting with DevTools

The browser DevTools box model visualization is the most important debugging tool you have for CSS. To use it:

1. Open the page in Chrome, Firefox, or Edge.
2. Press `F12` (or right-click and "Inspect").
3. Click the "Elements" tab.
4. Click on any element in the HTML tree.
5. Look at the right sidebar — under "Styles" or "Computed", you'll see the box model diagram with real numbers.

You can edit values directly in DevTools to see changes live. This is the fastest way to debug layout problems.

### The "Computed" Tab

The "Computed" tab shows the final, computed values of every CSS property after all rules have been applied. If your `width: 200px` isn't behaving as expected, the Computed tab tells you the actual rendered width (and why).

---

## Try It Yourself

### Exercise 1: The Width Problem

Create a `<div>` with `width: 300px`, `padding: 20px`, `border: 5px solid black`. Without `box-sizing: border-box`, measure its actual width in DevTools. Then add `box-sizing: border-box` and measure again. Confirm the rendered width changes from 350px to 300px.

### Exercise 2: A Centered Card

Make a `<div class="card">` that:
- Has a max width of 600px.
- Is centered horizontally in its parent (use `margin: 0 auto`).
- Has 40px padding, 1px border, light background.

### Exercise 3: Inspect Any Web Page

Open a favorite website (Wikipedia, your favorite blog). Open DevTools. Click on the main content. Look at the box model. Try to recreate the spacing (padding, margin) on your own page.

---

## Common Mistakes

### Mistake 1: Forgetting `box-sizing: border-box`

Without it, every padding and border increases the element's total size. Always start your stylesheet with the universal `box-sizing: border-box` rule.

### Mistake 2: Using fixed heights

```css
/* BAD */
.card { height: 200px; }
```

If content is longer than 200px, it overflows. Use `min-height` instead:

```css
.card { min-height: 200px; }
```

### Mistake 3: Confusing margin and padding

- **Padding** is **inside** the border, part of the element.
- **Margin** is **outside** the border, between elements.

If your background color doesn't reach the edge, you forgot padding. If two boxes are touching when they shouldn't, add margin.

### Mistake 4: Forgetting that vertical margins collapse

```html
<div style="margin-bottom: 30px;">A</div>
<div style="margin-top: 20px;">B</div>
```

Gap is 30px, not 50px. Don't add 20px to "compensate" — it won't help. Use padding or flexbox instead.

### Mistake 5: Inline elements ignoring width/height

```css
/* WON'T WORK on inline element */
span { width: 100px; height: 100px; }
```

Inline elements ignore width/height. Use `display: inline-block` first.

### Mistake 6: `outline: none` on focused elements

Removing the focus outline is illegal in some jurisdictions for accessibility compliance. Always provide an alternative:

```css
button:focus {
  outline: none;
  box-shadow: 0 0 0 3px blue;
}
```

---

## Summary

- Every element is a box with four layers: content, padding, border, margin.
- `width` and `height` set the content area by default.
- `padding` adds space inside the border (and is part of the element's background).
- `border` is a line around the padding.
- `margin` is space outside the border, between elements.
- **Always use `box-sizing: border-box`** to make `width` and `height` include padding and border.
- Padding and margin use the TRBL shorthand (top, right, bottom, left).
- Vertical margins between block siblings collapse to the larger value.
- `margin: 0 auto` centers a fixed-width block horizontally.
- `min-width`, `max-width`, `min-height`, `max-height` create responsive constraints.
- Use the DevTools box model diagram to debug layout problems.

This lesson was the most important in the entire CSS module. Read it again. Do all the exercises. Open DevTools and inspect any web page until you can see the box model in your sleep.

---

**Next:** [Lesson 07: Margin and Padding →](./07-margin-and-padding.md)
