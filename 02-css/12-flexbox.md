# Lesson 12: Flexbox

> Module: CSS · Lesson 12 of 16
> Estimated time: 60–90 minutes

Flexbox (Flexible Box Layout) is the modern answer to layout problems. Before flexbox, developers used floats, tables, and hacks to lay out elements — it was painful. Flexbox made it trivial. If you learn one layout system in CSS, learn flexbox.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain the flexbox model: flex container, flex items, main axis, cross axis.
2. Use `display: flex` to create a flex container.
3. Use `justify-content` to align items along the main axis.
4. Use `align-items` to align items along the cross axis.
5. Use `flex-direction`, `flex-wrap`, `gap`, `flex-grow`, `flex-shrink`, `flex-basis`.
6. Center anything horizontally and vertically with three lines of CSS.
7. Build navigation bars, card grids, sidebars, and toolbars.

---

## 1. The Flexbox Model

When you set `display: flex` on an element, you create a **flex container**. Its direct children become **flex items**.

```html
<div class="container">   <!-- flex container -->
  <div class="item">1</div>   <!-- flex item -->
  <div class="item">2</div>
  <div class="item">3</div>
</div>
```

```css
.container { display: flex; }
```

By default, flex items:
- Sit in a row (left to right).
- Don't wrap (they shrink to fit).
- Stretch to fill the container's cross-axis (height).
- Have no gap between them.

### The Two Axes

- **Main axis** — the direction items flow (default: horizontal, left to right).
- **Cross axis** — perpendicular to main (default: vertical).

```
   ───────────────►  (main axis)
   ┌───┐ ┌───┐ ┌───┐
   │ 1 │ │ 2 │ │ 3 │
   └───┘ └───┘ └───┘
   │
   ▼ (cross axis)
```

`justify-content` aligns items along the **main axis**.
`align-items` aligns items along the **cross axis**.

---

## 2. `flex-direction`

```css
.container {
  display: flex;
  flex-direction: row;            /* default - left to right */
  flex-direction: row-reverse;    /* right to left */
  flex-direction: column;         /* top to bottom */
  flex-direction: column-reverse; /* bottom to top */
}
```

Changing `flex-direction` changes which is the main axis:
- `row` / `row-reverse`: main axis is horizontal.
- `column` / `column-reverse`: main axis is vertical.

`justify-content` and `align-items` always refer to the current main and cross axes, respectively.

---

## 3. `justify-content` — Main Axis Alignment

```css
.container { justify-content: flex-start; }    /* default - packed to start */
.container { justify-content: flex-end; }       /* packed to end */
.container { justify-content: center; }         /* centered */
.container { justify-content: space-between; }  /* first and last at edges, equal space between */
.container { justify-content: space-around; }   /* equal space around each, including edges */
.container { justify-content: space-evenly; }    /* equal space between and at edges */
```

For `flex-direction: row`:
- `flex-start` = left
- `flex-end` = right
- `center` = horizontally centered

For `flex-direction: column`:
- `flex-start` = top
- `flex-end` = bottom
- `center` = vertically centered

---

## 4. `align-items` — Cross Axis Alignment

```css
.container { align-items: stretch; }       /* default - fill cross axis */
.container { align-items: flex-start; }    /* align to start of cross axis */
.container { align-items: flex-end; }      /* align to end */
.container { align-items: center; }        /* centered on cross axis */
.container { align-items: baseline; }      /* aligned by text baseline */
```

For `flex-direction: row`:
- `align-items: flex-start` = items align to top.
- `align-items: center` = items vertically centered.
- `align-items: flex-end` = items align to bottom.
- `align-items: stretch` = items stretch to fill the container's height.

---

## 5. The Centering Trick

To center an element both horizontally and vertically:

```css
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;   /* or some fixed height */
}
```

That's it. Three lines (plus a height) and any child is perfectly centered.

This used to be incredibly hard in CSS. Flexbox made it trivial.

---

## 6. `flex-wrap`

By default, flex items don't wrap — they shrink to fit. To allow wrapping:

```css
.container {
  display: flex;
  flex-wrap: nowrap;     /* default - one line */
  flex-wrap: wrap;        /* wrap to next line if too wide */
  flex-wrap: wrap-reverse; /* wrap upward */
}
```

### Card Grid with Wrap

```css
.card-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.card { flex: 1 1 250px; }   /* grow, shrink, basis */
```

Cards grow to fill space, but if the container gets too narrow, they wrap to a new line.

---

## 7. The `gap` Property

Modern flexbox supports `gap`:

```css
.container {
  display: flex;
  gap: 20px;            /* 20px between items, both directions */
  gap: 20px 10px;       /* row gap 20, column gap 10 */
  row-gap: 20px;
  column-gap: 10px;
}
```

`gap` is the modern replacement for margins between flex items. No more "first-child margin" hacks.

---

## 8. `flex` Shorthand on Items

Individual flex items can grow, shrink, or have a base size:

```css
.item {
  flex: 1 1 200px;
  /* grow | shrink | basis */
}
```

- `flex-grow` (default 0) — how much the item grows to fill free space.
- `flex-shrink` (default 1) — how much it shrinks when there's not enough space.
- `flex-basis` (default auto) — the item's base size before growing/shrinking.

### Common Patterns

**Equal-width items:**

```css
.item { flex: 1; }   /* grow: 1, shrink: 1, basis: 0 */
```

All items grow equally to fill the container.

**Fixed-width sidebar, flexible main content:**

```css
.sidebar { flex: 0 0 250px; }   /* don't grow or shrink, 250px wide */
.main { flex: 1; }              /* grow to fill remaining space */
```

**Don't shrink:**

```css
.logo { flex: 0 0 auto; }   /* stay at content size, don't shrink */
```

---

## 9. `align-self`

Override the container's `align-items` for a single item:

```css
.container { align-items: center; }
.item.special { align-self: flex-start; }   /* this one aligns to top */
```

Useful when one item should behave differently from the rest.

---

## 10. `order`

By default, flex items display in source order. You can change this:

```css
.item-1 { order: 2; }
.item-2 { order: 1; }   /* appears first */
.item-3 { order: 3; }
```

Lower `order` value appears first. Default is 0. Negative values are allowed.

**Warning:** changing visual order with `order` doesn't change keyboard navigation order, which can confuse screen reader and keyboard users. Use sparingly, and prefer to change the source order in HTML when possible.

---

## 11. Flex Container Property Summary

| Property | Values | Effect |
|----------|--------|--------|
| `display: flex` | (sets container) | Make a flex container |
| `flex-direction` | `row`, `row-reverse`, `column`, `column-reverse` | Direction of items |
| `flex-wrap` | `nowrap`, `wrap`, `wrap-reverse` | Whether items wrap |
| `justify-content` | `flex-start`, `flex-end`, `center`, `space-between`, `space-around`, `space-evenly` | Main-axis alignment |
| `align-items` | `stretch`, `flex-start`, `flex-end`, `center`, `baseline` | Cross-axis alignment |
| `align-content` | (same as justify-content) | Multi-line cross-axis alignment |
| `gap` | length | Spacing between items |

## 12. Flex Item Property Summary

| Property | Values | Effect |
|----------|--------|--------|
| `flex-grow` | number (default 0) | How much the item grows |
| `flex-shrink` | number (default 1) | How much the item shrinks |
| `flex-basis` | length or `auto` (default auto) | Initial size |
| `flex` | shorthand for grow/shrink/basis | One-liner |
| `align-self` | (same as align-items) | Override parent's align-items |
| `order` | integer (default 0) | Visual order |

---

## 13. Common Patterns

### Navigation Bar

```css
.nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px 30px;
  background: #2c3e50;
}

.nav-links { display: flex; gap: 20px; }
.nav-links a { color: white; text-decoration: none; }
```

Logo on left, links on right, vertically centered.

### Card Grid

```css
.cards {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.card {
  flex: 1 1 300px;
  background: white;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
}
```

Cards grow to fill, wrap to new line when needed.

### Centered Hero

```css
.hero {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  min-height: 60vh;
  text-align: center;
  padding: 40px 20px;
}
```

Content centered both horizontally and vertically.

### Sidebar + Main

```css
.layout {
  display: flex;
  gap: 30px;
  max-width: 1200px;
  margin: 0 auto;
}

.sidebar { flex: 0 0 250px; }
.main { flex: 1; }
```

Fixed-width sidebar, flexible main content.

### Sticky Footer

```css
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  margin: 0;
}

header { flex-shrink: 0; }
main { flex: 1; }
footer { flex-shrink: 0; }
```

Footer stays at the bottom even when content is short.

---

## 14. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flexbox Demo</title>
    <link rel="stylesheet" href="flexbox.css">
  </head>
  <body>
    <header class="site-header">
      <div class="logo">My Site</div>
      <nav class="nav-links">
        <a href="#">Home</a>
        <a href="#">About</a>
        <a href="#">Contact</a>
      </nav>
    </header>

    <main>
      <section class="hero">
        <h1>Welcome to My Site</h1>
        <p>Built with flexbox.</p>
        <button class="cta">Get Started</button>
      </section>

      <section class="cards">
        <div class="card">
          <h3>Card 1</h3>
          <p>Some content here.</p>
        </div>
        <div class="card">
          <h3>Card 2</h3>
          <p>Some content here.</p>
        </div>
        <div class="card">
          <h3>Card 3</h3>
          <p>Some content here.</p>
        </div>
      </section>
    </main>

    <footer class="site-footer">
      <p>© 2026 My Site</p>
    </footer>
  </body>
</html>
```

```css
/* flexbox.css */

* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: Arial, sans-serif;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background: #f8f9fa;
  color: #333;
  line-height: 1.6;
}

/* Sticky Header with Flex */
.site-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px 30px;
  background: #2c3e50;
  color: white;
}

.logo { font-weight: bold; font-size: 1.3em; }

.nav-links {
  display: flex;
  gap: 20px;
}

.nav-links a {
  color: white;
  text-decoration: none;
  padding: 5px 10px;
  border-radius: 4px;
}

.nav-links a:hover { background: #34495e; }

main { flex: 1; }

/* Centered Hero */
.hero {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  padding: 80px 20px;
  background: linear-gradient(135deg, #667eea, #764ba2);
  color: white;
}

.hero h1 { font-size: 2.5rem; margin-bottom: 15px; }
.hero p { font-size: 1.2rem; margin-bottom: 30px; opacity: 0.9; }

.cta {
  padding: 12px 30px;
  background: white;
  color: #2c3e50;
  border: none;
  border-radius: 5px;
  font-size: 1rem;
  cursor: pointer;
  font-weight: bold;
}

.cta:hover { background: #f0f0f0; }

/* Card Grid */
.cards {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  padding: 40px;
  max-width: 1200px;
  margin: 0 auto;
}

.card {
  flex: 1 1 300px;
  background: white;
  padding: 25px;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}

.card h3 { color: #2c3e50; margin-bottom: 10px; }

.site-footer {
  background: #2c3e50;
  color: white;
  text-align: center;
  padding: 20px;
}
```

Save and open. The header has logo left, links right. The hero is centered. The cards wrap responsively. The footer stays at the bottom.

---

## Try It Yourself

### Exercise 1: Three-Column Layout

Build a page with a left sidebar (250px fixed), a main content area (flexible), and a right sidebar (200px fixed). Use flexbox.

### Exercise 2: Centered Modal

Build a modal overlay with a centered dialog box. Use flexbox on the overlay with `justify-content: center` and `align-items: center`.

### Exercise 3: Responsive Cards

Build a card grid that:
- Shows 3 cards per row on desktop.
- Shows 2 cards per row on tablet.
- Shows 1 card per row on mobile.

Use `flex: 1 1 300px` and let the browser wrap automatically.

### Exercise 4: Navigation with Logo and Links

Build a header with a logo on the left and three nav links on the right. Use `justify-content: space-between`.

---

## Common Mistakes

### Mistake 1: Forgetting `display: flex`

```css
/* This does nothing */
.container { justify-content: center; }
```

`justify-content` only works on flex containers. Set `display: flex` first.

### Mistake 2: Confusing `justify-content` and `align-items`

- `justify-content` = main axis.
- `align-items` = cross axis.

If the items aren't centering the way you expect, you may have confused the axes. Try swapping the properties.

### Mistake 3: Not setting a height on the flex container

```css
/* This won't vertically center - no height to center in */
.parent {
  display: flex;
  align-items: center;
}
```

If `.parent` has no height (which is the default), there's no vertical space to center in. Set a height or `min-height`:

```css
.parent {
  display: flex;
  align-items: center;
  min-height: 100vh;
}
```

### Mistake 4: Forgetting `flex-wrap`

By default, flex items don't wrap. They'll shrink indefinitely to fit on one line. If you want wrapping, set `flex-wrap: wrap`.

### Mistake 5: Using `flex: 1` on items that shouldn't grow

```css
/* BAD: button grows to fill row */
.btn { flex: 1; }
```

Buttons usually should stay at their natural size. Remove `flex: 1`.

### Mistake 6: Confusing `flex-basis` with `width`

`flex-basis` is the **initial size before growing/shrinking**. It's similar to `width`, but interacts with `flex-grow` and `flex-shrink`. For most cases, `flex: 0 0 200px` (don't grow, don't shrink, 200px) is what you want for fixed-width items.

### Mistake 7: Too much margin between items

Use `gap` instead of margins. Margins can collapse and create off-by-one bugs. `gap` is the modern, clean solution.

---

## Summary

- `display: flex` creates a flex container; its direct children become flex items.
- **Main axis** = direction of items. **Cross axis** = perpendicular.
- `flex-direction`: `row` (default), `row-reverse`, `column`, `column-reverse`.
- `justify-content`: align items on main axis (`flex-start`, `center`, `space-between`, etc.).
- `align-items`: align items on cross axis (`stretch`, `center`, `flex-start`, etc.).
- `flex-wrap: wrap` allows items to wrap to new lines.
- `gap` adds space between items without margin hacks.
- `flex: grow shrink basis` shorthand on items.
- Common values: `flex: 1` (equal growth), `flex: 0 0 250px` (fixed width).
- Center anything: `display: flex; justify-content: center; align-items: center`.
- Sticky footer: make body `display: flex; flex-direction: column; min-height: 100vh;`, with `main { flex: 1 }`.
- Sidebar + main: `sidebar { flex: 0 0 250px; } main { flex: 1; }`.

Flexbox is the workhorse of modern CSS layouts. Practice it until it's second nature.

---

**Next:** [Lesson 13: Grid →](./13-grid.md)
