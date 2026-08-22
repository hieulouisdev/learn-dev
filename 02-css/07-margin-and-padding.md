# Lesson 07: Margin and Padding

> Module: CSS · Lesson 7 of 16
> Estimated time: 30–45 minutes

In Lesson 06, you learned that every CSS box has four layers: content, padding, border, margin. In this lesson, we'll go deeper into padding and margin — the two spacing properties — and learn practical patterns you'll use every day.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use the full shorthand syntax for padding and margin (1, 2, 3, 4 values).
2. Set per-side padding and margin.
3. Use `margin: auto` for centering.
4. Understand vertical margin collapsing.
5. Use negative margins for advanced effects.
6. Apply padding and margin to real UI components (cards, buttons, navigation).

---

## 1. Recap: The Two Spacing Properties

- **`padding`** — space inside the border. Adds to the visible size (unless `box-sizing: border-box`). The element's background fills the padding.
- **`margin`** — space outside the border. Transparent. Pushes other elements away.

```css
.card {
  background: white;
  padding: 20px;        /* inner space, white background fills it */
  margin: 30px;         /* outer space, transparent */
  border: 1px solid #ccc;
}
```

---

## 2. The Shorthand Syntax — Four Patterns

Both padding and margin use the same shorthand. There are four ways to write it.

### Pattern 1: All Four Sides Equal

```css
.box {
  padding: 20px;   /* top, right, bottom, left all 20px */
  margin: 30px;
}
```

Use when all four sides should be equal.

### Pattern 2: Vertical / Horizontal

```css
.box {
  padding: 20px 40px;   /* top/bottom 20, left/right 40 */
  margin: 10px 20px;
}
```

The first number is vertical (top + bottom), the second is horizontal (left + right). Most common pattern — many cards want equal top/bottom and equal left/right.

### Pattern 3: Top / Horizontal / Bottom

```css
.box {
  padding: 20px 40px 30px;  /* top 20, sides 40, bottom 30 */
}
```

Three values: top, horizontal (left+right), bottom. Useful for elements with a distinct top and bottom (like articles with intro and conclusion).

### Pattern 4: All Four Sides Different

```css
.box {
  padding: 20px 40px 30px 10px;
  /* top 20, right 40, bottom 30, left 10 */
}
```

**TRBL** ("Top Right Bottom Left" — like a clock starting at 12, going clockwise).

### The Memory Trick

The pattern follows a **clockwise rotation starting from the top**:

```
     top (12 o'clock)
       ^
       |
left ←   → right
       |
       v
    bottom (6 o'clock)
```

- 1 value: all sides
- 2 values: vertical / horizontal
- 3 values: top / horizontal / bottom
- 4 values: top / right / bottom / left (clockwise from 12)

---

## 3. Per-Side Properties

If you only want to set one side, use the individual properties:

```css
.box {
  padding-top: 20px;
  padding-right: 15px;
  padding-bottom: 25px;
  padding-left: 10px;

  margin-top: 30px;
  margin-right: 20px;
  margin-bottom: 30px;
  margin-left: 20px;
}
```

### When to Use Per-Side

- When only one side needs to differ.
- When overriding a single side from a base rule.

```css
.card { padding: 20px; }
.card.large { padding-top: 40px; }   /* overrides only top */
```

---

## 4. The `auto` Value for Margins

```css
.container {
  width: 800px;
  margin: 0 auto;   /* top/bottom 0, left/right auto */
}
```

`margin: auto` on left and right **centers the element horizontally** within its parent. The browser calculates the available space and splits it evenly.

### Important Requirements

- The element must have a defined `width` (or `max-width`).
- The element must be a block-level element (or have `display: block`).
- It does NOT work vertically — `margin: auto 0` does not center vertically.

### Centering Patterns

```css
/* Center a fixed-width container */
.container { width: 800px; margin: 0 auto; }

/* Center a max-width container (responsive) */
.container { max-width: 800px; margin: 0 auto; }

/* Center with vertical spacing */
.container { max-width: 800px; margin: 40px auto; }
```

For vertical centering, you'll need flexbox (Lesson 12) or grid (Lesson 13).

---

## 5. Vertical Margin Collapsing

When two block elements are stacked vertically, their adjacent margins **collapse** — merge into a single margin equal to the **larger** of the two.

```html
<div style="margin-bottom: 30px;">Box A</div>
<div style="margin-top: 20px;">Box B</div>
```

The gap between them is **30px**, not 50px. The 20px margin collapses into the 30px.

### When Margins Collapse

- Vertical margins between **block siblings** (one element below another).
- Vertical margins between a **parent and its first/last child** (if no border, padding, or inline content separates them).

### When Margins Don't Collapse

- Horizontal margins (left/right) — they always add.
- Margins inside **flex** or **grid** containers.
- Margins between elements separated by **padding, border, or inline content**.
- Margins on **inline-block**, **floated**, or **absolutely positioned** elements.

### Workarounds for Collapsing

If you need margins to add (not collapse):

1. Add `padding-top: 1px` or `border-top: 1px solid transparent` to the parent.
2. Use flexbox (flex children's margins don't collapse).
3. Use `display: flow-root` on the parent.
4. Use `overflow: hidden` or `overflow: auto` on the parent.

### Margin Collapse Example

```html
<div class="parent">
  <p class="para-1">First paragraph</p>
  <p class="para-2">Second paragraph</p>
</div>
```

```css
.para-1 { margin-bottom: 30px; }
.para-2 { margin-top: 20px; }
```

The visible gap between paragraphs is **30px**, not 50px.

---

## 6. Negative Margins

Negative margins pull elements closer together or overlap them:

```css
.overlap {
  margin-top: -30px;   /* moves this element up by 30px, overlapping the previous one */
}
```

### Common Use Cases

**Pull an element to the edge of its parent:**

```css
.full-width-banner {
  margin-left: -20px;
  margin-right: -20px;
}
```

**Make text overlap an image:**

```css
.caption {
  margin-top: -50px;
  position: relative;
  background: rgba(0, 0, 0, 0.7);
  color: white;
}
```

### Beware

Negative margins can cause overlapping content, hidden text, and unpredictable layouts. Use sparingly and test thoroughly.

---

## 7. Padding Patterns for UI Components

### Buttons

```css
.btn {
  padding: 10px 20px;
  background: #3498db;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.btn:hover { background: #2980b9; }
```

Vertical padding for height, horizontal padding for width.

### Cards

```css
.card {
  padding: 20px 25px;
  background: white;
  border: 1px solid #ddd;
  border-radius: 8px;
  margin-bottom: 20px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
```

Slightly more horizontal padding than vertical often looks better.

### Articles

```css
.article {
  padding: 40px;
  max-width: 700px;
  margin: 0 auto;
  line-height: 1.7;
}
```

Generous padding around body content for comfortable reading.

### Navigation

```css
.nav { padding: 15px 30px; background: #2c3e50; }
.nav a { padding: 8px 15px; color: white; text-decoration: none; }
.nav a:hover { background: #34495e; border-radius: 4px; }
```

Padding on links makes them comfortable click targets.

---

## 8. Spacing Scale

For consistency across a project, use a fixed spacing scale. Common approach:

```css
:root {
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 16px;
  --space-4: 24px;
  --space-5: 32px;
  --space-6: 48px;
  --space-7: 64px;
}

.card {
  padding: var(--space-4);
  margin-bottom: var(--space-4);
}

.btn { padding: var(--space-2) var(--space-3); }
```

This forces you to use consistent spacing. Pages look more polished and intentional.

A popular scale (used by Tailwind CSS, Bootstrap, etc.):

| Token | Value |
|-------|-------|
| `0` | 0 |
| `1` | 0.25rem (4px) |
| `2` | 0.5rem (8px) |
| `3` | 0.75rem (12px) |
| `4` | 1rem (16px) |
| `6` | 1.5rem (24px) |
| `8` | 2rem (32px) |
| `12` | 3rem (48px) |
| `16` | 4rem (64px) |
| `24` | 6rem (96px) |

Sticking to a scale prevents off-by-one spacing inconsistencies.

---

## 9. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spacing Demo</title>
    <link rel="stylesheet" href="spacing.css">
  </head>
  <body>
    <header class="site-header">
      <nav class="nav">
        <a href="#" class="nav-link">Home</a>
        <a href="#" class="nav-link active">About</a>
        <a href="#" class="nav-link">Contact</a>
      </nav>
    </header>

    <main class="container">
      <article class="card">
        <h2>Article Title</h2>
        <p class="lead">This is the lead paragraph with larger text and more vertical margin.</p>
        <p>Regular paragraph. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.</p>
      </article>

      <article class="card">
        <h2>Second Card</h2>
        <p>Notice the consistent spacing between cards. The space between them is set by margin-bottom on .card.</p>
        <button class="btn">Click Me</button>
      </article>
    </main>
  </body>
</html>
```

```css
/* spacing.css */

:root {
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 16px;
  --space-4: 24px;
  --space-5: 32px;
  --space-6: 48px;
}

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: Arial, sans-serif;
  background: #f0f0f0;
  color: #333;
  line-height: 1.6;
}

.site-header {
  background: #2c3e50;
  padding: var(--space-3) var(--space-5);
}

.nav a {
  color: white;
  text-decoration: none;
  padding: var(--space-2) var(--space-3);
  margin-right: var(--space-1);
  border-radius: 4px;
}

.nav a:hover {
  background: #34495e;
}

.nav a.active {
  background: #3498db;
}

.container {
  max-width: 800px;
  margin: var(--space-6) auto;
  padding: 0 var(--space-4);
}

.card {
  background: white;
  padding: var(--space-5) var(--space-6);
  margin-bottom: var(--space-5);
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}

.card h2 {
  margin-bottom: var(--space-3);
  color: #2c3e50;
}

.lead {
  font-size: 1.2em;
  margin-bottom: var(--space-4);
  color: #555;
}

.btn {
  display: inline-block;
  padding: var(--space-2) var(--space-4);
  background: #3498db;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
}

.btn:hover {
  background: #2980b9;
}
```

Notice:
- Consistent spacing using CSS variables.
- `box-sizing: border-box` resets all elements.
- Margins create gaps between cards.
- Padding creates inner space inside cards.
- `margin: 0 auto` centers the container.

---

## Try It Yourself

### Exercise 1: Spacing Scale

Define your own spacing scale (e.g., 8px, 16px, 24px, 32px, 48px, 64px). Use it consistently in a small page. Notice how consistent spacing makes the page feel polished.

### Exercise 2: Card Grid

Build three cards side by side (you can use `display: inline-block` for now, even though flexbox is better — we'll get there). Add:
- Padding inside each card.
- Margin between them.
- A hover effect that adds a shadow.

### Exercise 3: Test Margin Collapsing

Create two paragraphs with `margin-bottom: 50px` and `margin-top: 30px` respectively. Measure the gap in DevTools. Confirm it's 50px (not 80px).

---

## Common Mistakes

### Mistake 1: Padding instead of margin for gaps

```css
/* BAD: padding adds to the element's size */
.card { padding: 20px; }
.card + .card { padding-top: 40px; }  /* trying to add gap */
```

Use margin for **gaps between elements**. Padding is for **space inside** an element.

### Mistake 2: Margin instead of padding for inner space

```css
/* BAD: margin pushes other elements away, doesn't fill background */
.card { margin: 20px; background: white; }
```

Use padding for **space inside the element** so the background fills it. Margin is transparent.

### Mistake 3: Forgetting `auto` for centering

```css
/* BAD: doesn't center */
.container { width: 800px; margin: 0; }
```

`margin: 0 auto` centers horizontally. Always include `auto` for the horizontal sides.

### Mistake 4: Vertical `margin: auto`

```css
/* WON'T WORK for vertical centering */
.box { height: 100px; margin: auto; }
```

Vertical `auto` doesn't center. Use flexbox or grid.

### Mistake 5: Forgetting margin collapse

Beginners are surprised when their margins "disappear". Always test in DevTools to see the actual computed spacing.

### Mistake 6: Spacing chaos (random px values)

```css
/* BAD: random spacing looks messy */
.card { padding: 17px; margin-bottom: 23px; }
.btn { padding: 9px 18px; }
```

Pick a scale (4, 8, 12, 16, 24, 32, 48) and stick to it. Your design will look intentional.

### Mistake 7: `padding: 0` thinking it does nothing

`padding: 0` is explicit zero — it overrides any other padding rule with equal specificity. Useful for resets, but be careful when overriding.

---

## Summary

- Padding and margin both use the TRBL shorthand (top, right, bottom, left, clockwise from 12).
- Four shorthand patterns: 1 value (all sides), 2 values (V/H), 3 values (T/H/B), 4 values (TRBL).
- Per-side properties: `padding-top`, `padding-right`, `padding-bottom`, `padding-left` (and same for `margin-*`).
- `margin: 0 auto` centers a fixed-width block horizontally.
- Vertical margins between block siblings collapse to the larger value.
- Horizontal margins never collapse.
- Margins don't collapse inside flex/grid containers, or when separated by padding/border.
- Negative margins pull elements together or overlap them.
- Use a spacing scale (CSS variables) for consistent design.
- Padding creates inner space (with background); margin creates outer gaps (transparent).

In the next lesson, we'll cover borders — including rounded corners and creative border patterns.

---

**Next:** [Lesson 08: Borders →](./08-borders.md)
