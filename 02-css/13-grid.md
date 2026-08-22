# Lesson 13: Grid

> Module: CSS · Lesson 13 of 16
> Estimated time: 60–90 minutes

CSS Grid is the most powerful layout system in CSS. While flexbox is one-dimensional (rows OR columns), grid is **two-dimensional** (rows AND columns simultaneously). Use grid for complex page layouts: header/sidebar/main/footer grids, photo galleries, dashboards.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain when to use grid vs. flexbox.
2. Use `display: grid` and `grid-template-columns`.
3. Use the `fr` unit for flexible sizes.
4. Use `repeat()`, `minmax()`, and `auto-fit` for responsive grids.
5. Place items with `grid-column` and `grid-row`.
6. Use `grid-template-areas` for visual layouts.
7. Build a complete page layout with grid.

---

## 1. Grid vs. Flexbox

| Aspect | Flexbox | Grid |
|--------|--------|------|
| Dimensions | One (row OR column) | Two (row AND column) |
| Best for | Component-level layout (nav, card row, toolbar) | Page-level layout (header, sidebar, main, footer) |
| Item sizing | Based on content | Explicit or fractional |
| Wrapping | Yes, but unpredictable | Yes, with `auto-fit` |

**Rule of thumb**: Use flexbox for one-dimensional layouts (a single row or column). Use grid for two-dimensional layouts (multiple rows and columns).

In practice, you'll use both — grid for the page skeleton, flexbox inside components.

---

## 2. Creating a Grid

```css
.container {
  display: grid;
  grid-template-columns: 200px 200px 200px;
  gap: 20px;
}
```

```html
<div class="container">
  <div class="item">1</div>
  <div class="item">2</div>
  <div class="item">3</div>
  <div class="item">4</div>
  <div class="item">5</div>
  <div class="item">6</div>
</div>
```

This creates a grid with three columns of 200px each, 20px gaps between them. The six items fill the grid in order (left-to-right, top-to-bottom).

### The `fr` Unit

`fr` (fraction) is a flexible unit for grid:

```css
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;   /* three equal columns */
}
```

```css
grid-template-columns: 2fr 1fr;   /* first column twice as wide */
grid-template-columns: 200px 1fr 200px;   /* fixed sidebars, flexible middle */
```

`fr` distributes available space proportionally.

---

## 3. `repeat()`

The `repeat()` function is more concise:

```css
grid-template-columns: repeat(3, 200px);   /* same as 200px 200px 200px */
grid-template-columns: repeat(3, 1fr);     /* three equal columns */
grid-template-columns: repeat(4, 100px 1fr);   /* alternating 100px and 1fr, 4 times = 8 columns */
```

---

## 4. `gap`

```css
.container {
  display: grid;
  gap: 20px;            /* row and column gap = 20px */
  gap: 20px 10px;        /* row gap 20, column gap 10 */
  row-gap: 20px;
  column-gap: 10px;
}
```

Same syntax as flexbox `gap`.

---

## 5. `grid-template-rows`

You can also define row heights:

```css
.container {
  display: grid;
  grid-template-rows: 100px 200px;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}
```

This creates a 2×2 grid with row heights of 100px and 200px.

---

## 6. `minmax()` and Responsive Grids

The killer feature of grid:

```css
.container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
}
```

This creates a responsive grid where:
- Each column is at least 300px wide.
- Columns grow equally to fill available space (`1fr`).
- The number of columns adjusts automatically based on container width (`auto-fit`).

This single line creates a fully responsive card grid without any media queries.

### `auto-fit` vs `auto-fill`

- `auto-fit`: collapses empty columns. Items stretch to fill available space.
- `auto-fill`: keeps empty columns. Items stay at their natural size.

For most cases, `auto-fit` is what you want.

---

## 7. Placing Items Explicitly

By default, items flow into the grid in source order. You can place them explicitly:

```css
.item-1 {
  grid-column: 1 / 3;   /* starts at line 1, ends at line 3 (spans 2 columns) */
  grid-row: 1;          /* row 1 */
}
```

### Grid Lines

A 3-column grid has 4 vertical lines:

```
1       2       3       4
│       │       │       │
```

`grid-column: 1 / 3` means "start at line 1, end at line 3" — spans two columns.

### Shorthand for Spans

```css
.item { grid-column: span 2; }   /* span 2 columns */
.item { grid-row: span 3; }       /* span 3 rows */
```

### Both at Once

```css
.item {
  grid-column: 2 / span 2;   /* start at line 2, span 2 columns */
}
```

---

## 8. `grid-area` Shorthand

```css
.item {
  grid-area: 1 / 1 / 3 / 4;
  /* row-start / col-start / row-end / col-end */
}
```

---

## 9. Grid Template Areas — Visual Layouts

The most readable way to define layouts:

```css
.layout {
  display: grid;
  grid-template-columns: 200px 1fr;
  grid-template-rows: auto 1fr auto;
  grid-template-areas:
    "header header"
    "sidebar main"
    "footer footer";
  min-height: 100vh;
  gap: 20px;
}

.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main    { grid-area: main; }
.footer  { grid-area: footer; }
```

The `grid-template-areas` property is a visual ASCII-art representation of your layout. Each quoted string is a row, with area names separated by spaces. Matching names in the same row span.

You can use a `.` to leave a cell empty:

```css
grid-template-areas:
  "header header header"
  "sidebar main ."
  "footer footer footer";
```

This is by far the most readable layout technique in CSS. Use it whenever you have a complex page layout.

---

## 10. Justify and Align Items

Grid has alignment properties similar to flexbox:

```css
.container {
  justify-items: start;     /* default: items align to start of cell */
  align-items: start;
  justify-items: center;
  align-items: center;
  justify-items: stretch;   /* default: items fill cell */
  align-items: stretch;
}
```

These apply to **all items** in the grid.

### Per-Item Alignment

```css
.item { justify-self: center; align-self: center; }
```

Override the container's `justify-items`/`align-items` for one item.

### Container Content Alignment

```css
.container {
  justify-content: center;   /* align the entire grid horizontally */
  align-content: center;      /* align the entire grid vertically */
}
```

Used when the grid is smaller than its container.

---

## 11. Implicit Grid

If you only specify `grid-template-columns`, the rows are created **implicitly** as items are added:

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}
```

Adding 7 items creates 3 rows (the first two rows are explicit, the third is implicit).

You can control implicit row sizes with:

```css
.container {
  grid-auto-rows: 100px;       /* implicit rows are 100px tall */
  grid-auto-flow: row;          /* default - adds rows as needed */
  grid-auto-flow: column;        /* fills columns instead */
  grid-auto-columns: 200px;       /* column size when flow is column */
}
```

---

## 12. Common Patterns

### Holy Grail Layout

```css
.layout {
  display: grid;
  grid-template-areas:
    "header header header"
    "nav main aside"
    "footer footer footer";
  grid-template-columns: 200px 1fr 200px;
  grid-template-rows: auto 1fr auto;
  min-height: 100vh;
  gap: 20px;
}
```

Header on top, nav/main/aside in the middle, footer at bottom. Three columns, three rows, all explicit.

### Photo Gallery

```css
.gallery {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 15px;
}

.gallery-item.featured {
  grid-column: span 2;
  grid-row: span 2;
}
```

A responsive grid where featured items take 2×2 space.

### Bento Box Layout

```css
.bento {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(3, 100px);
  gap: 15px;
}

.bento .a { grid-column: span 2; grid-row: span 2; }
.bento .b { grid-column: span 2; }
.bento .c { grid-column: span 1; grid-row: span 2; }
.bento .d { grid-column: span 1; }
```

A bento-box style dashboard with varied tile sizes.

---

## 13. A Complete Example: Page Layout

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grid Demo</title>
    <link rel="stylesheet" href="grid.css">
  </head>
  <body>
    <div class="layout">
      <header class="header">
        <h1>My Site</h1>
      </header>

      <nav class="sidebar">
        <h3>Menu</h3>
        <ul>
          <li><a href="#">Home</a></li>
          <li><a href="#">About</a></li>
          <li><a href="#">Blog</a></li>
        </ul>
      </nav>

      <main class="main">
        <h2>Welcome</h2>
        <p>This is the main content area.</p>
        <div class="cards">
          <div class="card">Card 1</div>
          <div class="card">Card 2</div>
          <div class="card">Card 3</div>
          <div class="card">Card 4</div>
        </div>
      </main>

      <aside class="aside">
        <h3>Side</h3>
        <p>Related links.</p>
      </aside>

      <footer class="footer">
        <p>© 2026</p>
      </footer>
    </div>
  </body>
</html>
```

```css
/* grid.css */

* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: Arial, sans-serif;
  background: #f8f9fa;
  color: #333;
  line-height: 1.6;
}

.layout {
  display: grid;
  grid-template-areas:
    "header header header"
    "sidebar main aside"
    "footer footer footer";
  grid-template-columns: 220px 1fr 220px;
  grid-template-rows: auto 1fr auto;
  min-height: 100vh;
  gap: 15px;
}

.header {
  grid-area: header;
  background: #2c3e50;
  color: white;
  padding: 20px 30px;
}

.header h1 { color: white; }

.sidebar {
  grid-area: sidebar;
  background: white;
  padding: 20px;
  border-radius: 8px;
}

.sidebar ul {
  list-style: none;
  padding: 0;
}

.sidebar a {
  display: block;
  padding: 8px 12px;
  color: #2c3e50;
  text-decoration: none;
  border-radius: 4px;
  margin-bottom: 5px;
}

.sidebar a:hover { background: #ecf0f1; }

.main {
  grid-area: main;
  background: white;
  padding: 30px;
  border-radius: 8px;
}

.cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 15px;
  margin-top: 20px;
}

.card {
  background: #f0f4f8;
  padding: 20px;
  border-radius: 6px;
  text-align: center;
  border: 1px solid #d0d8e0;
}

.aside {
  grid-area: aside;
  background: white;
  padding: 20px;
  border-radius: 8px;
}

.footer {
  grid-area: footer;
  background: #2c3e50;
  color: white;
  text-align: center;
  padding: 15px;
}

/* Responsive */
@media (max-width: 768px) {
  .layout {
    grid-template-areas:
      "header"
      "main"
      "sidebar"
      "aside"
      "footer";
    grid-template-columns: 1fr;
  }
}
```

Save and open. The page has a header, sidebar, main content, right aside, and footer — all laid out with grid. On narrow screens, everything stacks vertically.

---

## Try It Yourself

### Exercise 1: Photo Gallery

Build a responsive photo gallery:
- 4 columns on desktop.
- 2 columns on tablet.
- 1 column on mobile.

Use `repeat(auto-fit, minmax(200px, 1fr))`.

### Exercise 2: Magazine Layout

Build a magazine-style layout with one large featured article on the left (spanning 2 rows) and three smaller articles on the right.

### Exercise 3: Holy Grail

Build the Holy Grail layout: header, three columns (nav, main, aside), footer — all in a single grid.

### Exercise 4: Grid Template Areas

Recreate this layout using `grid-template-areas`:

```
+-------+--------+
| HEADER         |
+-------+--------+
| NAV | MAIN     |
+-----+----------+
| FOOTER         |
+----------------+
```

---

## Common Mistakes

### Mistake 1: Forgetting `display: grid`

```css
/* This does nothing */
.container { grid-template-columns: 1fr 1fr; }
```

Grid properties only work on grid containers. Set `display: grid` first.

### Mistake 2: Using `fr` outside of grid

```css
/* WRONG: fr is grid-only */
.box { width: 1fr; }
```

`fr` is a grid unit. For flexible widths outside grid, use percentages, `flex`, or `calc()`.

### Mistake 3: Confusing grid lines with columns

A 3-column grid has **4 lines**: 1, 2, 3, 4. To span the middle column, use `grid-column: 2 / 3`.

### Mistake 4: Forgetting `gap` (using margins)

```css
/* BAD: margin hacks for gaps */
.item { margin: 10px; }
```

Use `gap: 10px` on the container. Cleaner and more predictable.

### Mistake 5: Not using `minmax()` for responsive grids

```css
/* Won't be responsive - just fixed columns */
.container { grid-template-columns: repeat(3, 1fr); }
```

Use `repeat(auto-fit, minmax(300px, 1fr))` for responsiveness without media queries.

### Mistake 6: Hardcoding heights on grid items

Grid items will fill their cells (stretch by default). Don't set explicit heights unless you need to.

### Mistake 7: Forgetting `min-height: 100vh` on layout grids

If you want a full-page layout, the grid container needs `min-height: 100vh` so it fills the viewport.

---

## Summary

- `display: grid` creates a grid container.
- `grid-template-columns` and `grid-template-rows` define the grid structure.
- Use the `fr` unit for flexible sizing.
- Use `repeat(n, value)` for repeated tracks.
- Use `repeat(auto-fit, minmax(min, max))` for responsive grids without media queries.
- `gap` adds space between tracks (no margin hacks).
- `grid-column` and `grid-row` place items explicitly using lines (`start / end`) or spans (`span N`).
- `grid-template-areas` gives a visual ASCII-art representation of the layout — most readable technique.
- `justify-items` and `align-items` align items within their cells.
- Use grid for two-dimensional layouts (page structure). Use flexbox for one-dimensional layouts (component internals).

In the next lesson, we'll cover responsive design — making your layouts adapt to phones, tablets, and desktops.

---

**Next:** [Lesson 14: Responsive Design →](./14-responsive-design.md)
