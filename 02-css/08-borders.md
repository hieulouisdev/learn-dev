# Lesson 08: Borders

> Module: CSS · Lesson 8 of 16
> Estimated time: 30–45 minutes

Borders are one of the most visually impactful CSS properties. A simple border can turn a plain `<div>` into a card, separate sections, or draw attention to important content. In this lesson, you'll learn every border feature — including the magical `border-radius` that rounds corners and turns squares into circles.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use the `border` shorthand and individual properties (`border-width`, `border-style`, `border-color`).
2. Apply all border styles: solid, dashed, dotted, double, etc.
3. Style borders per side.
4. Round corners with `border-radius`.
5. Create circles, ellipses, and pill shapes.
6. Use `outline` for focus indicators.
7. Build common UI components: cards, alerts, dividers.

---

## 1. The Three Parts of a Border

Every border has three parts:

1. **Width** — how thick (e.g., `2px`, `thin`, `thick`).
2. **Style** — what kind of line (e.g., `solid`, `dashed`).
3. **Color** — what color (e.g., `black`, `#ccc`).

Use the shorthand to set all three:

```css
.box {
  border: 2px solid black;
}
```

Order in the shorthand doesn't matter — `2px solid black`, `solid 2px black`, and `black solid 2px` all work the same.

### Individual Properties

```css
.box {
  border-width: 2px;
  border-style: solid;
  border-color: black;
}
```

Setting them separately is useful when you want to override just one part of an existing border rule.

---

## 2. Border Styles

The `border-style` property has 10 possible values:

```css
.solid    { border: 2px solid black; }
.dashed   { border: 2px dashed black; }
.dotted   { border: 2px dotted black; }
.double   { border: 4px double black; }    /* needs at least 3px width */
.groove   { border: 6px groove gray; }    /* 3D grooved effect */
.ridge    { border: 6px ridge gray; }     /* 3D raised effect */
.inset    { border: 6px inset gray; }     /* looks pressed in */
.outset   { border: 6px outset gray; }    /* looks pressed out */
.none     { border: 2px none black; }     /* no border */
.hidden   { border: 2px hidden black; }   /* like none but stronger */
```

### Most Common Styles

In practice, you'll almost always use:
- `solid` — clean and modern. Default for production.
- `dashed` — for warnings, placeholders, secondary elements.
- `dotted` — for subtle decorative borders.

The 3D styles (`groove`, `ridge`, `inset`, `outset`) look dated and should be used sparingly.

---

## 3. Per-Side Borders

You can set borders on each side independently:

```css
.box {
  border-top: 2px solid red;
  border-right: 1px solid blue;
  border-bottom: 2px solid green;
  border-left: 1px solid yellow;
}

/* Or individual properties */
.box {
  border-top-width: 2px;
  border-top-style: solid;
  border-top-color: red;
}
```

### Common Patterns

**Bottom border only (like a section divider):**

```css
.section {
  border-bottom: 2px solid #ddd;
  padding-bottom: 10px;
}
```

**Top accent border (like a highlighted card):**

```css
.card.featured {
  border-top: 4px solid gold;
  border-bottom: 1px solid #ddd;
  border-left: 1px solid #ddd;
  border-right: 1px solid #ddd;
}
```

Or more concisely:

```css
.card.featured {
  border: 1px solid #ddd;
  border-top: 4px solid gold;
}
```

**Left accent border (like a quote or alert):**

```css
.quote {
  border-left: 4px solid #3498db;
  padding-left: 20px;
}
```

---

## 4. Border Width Keywords

You can use named widths instead of pixels:

```css
.box { border: thin solid black; }     /* ~1px */
.box { border: medium solid black; }  /* ~3px (default) */
.box { border: thick solid black; }   /* ~5px */
```

These are imprecise (different browsers render different exact widths). For production, use explicit `px` values:

```css
.box { border: 2px solid black; }
```

---

## 5. Border Radius — Rounded Corners

The `border-radius` property rounds the corners of an element. This single property transforms the look of a UI.

```css
.box {
  border: 2px solid black;
  border-radius: 8px;     /* all four corners */
}
```

### Per-Corner Radius

```css
.box { border-radius: 8px; }              /* all corners 8px */
.box { border-radius: 8px 4px; }          /* TL/BR 8, TR/BL 4 */
.box { border-radius: 8px 4px 16px; }     /* TL 8, TR/BL 4, BR 16 */
.box { border-radius: 8px 4px 16px 4px; } /* TL TR BR BL (clockwise from TL) */
```

Same TRBL-like pattern as padding/margin, but for corners.

### Per-Corner Properties

```css
.box {
  border-top-left-radius: 8px;
  border-top-right-radius: 4px;
  border-bottom-right-radius: 8px;
  border-bottom-left-radius: 4px;
}
```

### Rounded Buttons (Pill Shape)

```css
.btn {
  border-radius: 9999px;   /* or 999px */
  padding: 10px 30px;
}
```

A very large radius creates fully rounded ends — a pill shape.

### Circles

```css
.avatar {
  width: 100px;
  height: 100px;
  border-radius: 50%;   /* 50% makes a circle */
  overflow: hidden;     /* clip overflowing content */
}
```

A 50% radius on a square element turns it into a perfect circle. Common for profile avatars.

### Ellipses

```css
.oval {
  width: 200px;
  height: 100px;
  border-radius: 50%;   /* ellipse: 50% horizontal, 50% vertical */
}
```

### Asymmetric Radius

```css
.leaf {
  border-radius: 0 100% 0 100%;   /* leaf shape */
}
```

You can create interesting organic shapes by mixing different radii.

### Percentage Radii

```css
.box {
  border-radius: 50%;          /* circle/ellipse */
  border-radius: 25%;          /* gently rounded */
}
```

Percentages are relative to the element's dimensions.

---

## 6. Border Color

```css
.box {
  border: 2px solid;
  border-color: #3498db;       /* all sides */
  border-color: red green blue yellow;  /* per side */
}

/* Per-side color */
.box {
  border-top-color: red;
  border-right-color: green;
  border-bottom-color: blue;
  border-left-color: yellow;
}
```

### Gradient Borders (Modern)

You can use gradient images as borders:

```css
.box {
  border: 4px solid transparent;
  border-image: linear-gradient(to right, red, blue) 1;
}
```

Or with `background-clip`:

```css
.gradient-border {
  background: linear-gradient(white, white) padding-box,
              linear-gradient(to right, red, blue) border-box;
  border: 4px solid transparent;
  border-radius: 8px;
}
```

Gradient borders are an advanced technique — useful for buttons and badges.

---

## 7. Outline vs. Border

```css
.box {
  border: 2px solid black;
  outline: 2px solid red;
  outline-offset: 4px;
}
```

- `outline` draws **outside** the border.
- `outline` does **not** take up space — doesn't affect layout.
- `outline-offset` adds space between border and outline.
- `outline` doesn't follow `border-radius` by default (it's rectangular), though newer browsers do support rounded outlines.

### Use Cases for Outline

**Focus indicators for keyboard navigation:**

```css
button:focus {
  outline: 3px solid #3498db;
  outline-offset: 2px;
}
```

**Highlighting an element on hover without shifting layout:**

```css
.card:hover {
  outline: 2px solid gold;
  outline-offset: 2px;
}
```

Never use `outline: none` without providing an alternative — it removes keyboard accessibility.

---

## 8. Building UI Components with Borders

### Card

```css
.card {
  background: white;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}
```

### Alert Boxes

```css
.alert {
  padding: 15px 20px;
  border-radius: 5px;
  margin-bottom: 15px;
}

.alert-info {
  background: #e8f4fd;
  border-left: 4px solid #3498db;
  color: #1c5680;
}

.alert-success {
  background: #e8f8ec;
  border-left: 4px solid #2ecc71;
  color: #1a6b3a;
}

.alert-warning {
  background: #fdf3e7;
  border-left: 4px solid #f39c12;
  color: #8a5300;
}

.alert-danger {
  background: #fdecea;
  border-left: 4px solid #e74c3c;
  color: #8a2620;
}
```

### Dividers

```css
hr {
  border: none;
  border-top: 1px solid #ddd;
  margin: 20px 0;
}
```

Remove the default borders, then add a single top border for a clean horizontal line.

### Buttons

```css
.btn {
  padding: 10px 20px;
  border: 1px solid #3498db;
  border-radius: 5px;
  background: #3498db;
  color: white;
  cursor: pointer;
}

.btn:hover {
  background: #2980b9;
  border-color: #2980b9;
}

.btn-outline {
  background: transparent;
  color: #3498db;
  border: 1px solid #3498db;
}

.btn-outline:hover {
  background: #3498db;
  color: white;
}
```

### Avatars (Circles)

```css
.avatar {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  border: 2px solid white;
  box-shadow: 0 1px 3px rgba(0,0,0,0.2);
}
```

### Tabs

```css
.tab {
  padding: 10px 20px;
  border: 1px solid #ddd;
  border-bottom: none;
  border-radius: 5px 5px 0 0;
  background: #f8f8f8;
  margin-right: 5px;
}

.tab.active {
  background: white;
  border-bottom: 1px solid white;
  position: relative;
  top: 1px;
}
```

---

## 9. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Borders Demo</title>
    <link rel="stylesheet" href="borders.css">
  </head>
  <body>
    <h1>Borders Demo</h1>

    <h2>Border Styles</h2>
    <div class="row">
      <div class="box border-solid">Solid</div>
      <div class="box border-dashed">Dashed</div>
      <div class="box border-dotted">Dotted</div>
      <div class="box border-double">Double</div>
    </div>

    <h2>Border Radius</h2>
    <div class="row">
      <div class="box radius-4">4px</div>
      <div class="box radius-8">8px</div>
      <div class="box radius-16">16px</div>
      <div class="box radius-pill">Pill</div>
    </div>

    <h2>Circles</h2>
    <div class="row">
      <div class="circle">A</div>
      <div class="circle">B</div>
      <div class="circle">C</div>
    </div>

    <h2>Alerts</h2>
    <div class="alert alert-info">Information message.</div>
    <div class="alert alert-success">Operation completed successfully.</div>
    <div class="alert alert-warning">This action cannot be undone.</div>
    <div class="alert alert-danger">An error occurred.</div>

    <h2>Buttons</h2>
    <button class="btn">Primary</button>
    <button class="btn btn-outline">Outline</button>
    <button class="btn btn-pill">Pill</button>
  </body>
</html>
```

```css
/* borders.css */

* { box-sizing: border-box; }

body {
  font-family: Arial, sans-serif;
  padding: 30px;
  background: #f8f9fa;
  color: #333;
}

h1, h2 { color: #2c3e50; }

.row {
  display: flex;
  gap: 15px;
  margin-bottom: 20px;
}

.box {
  width: 100px;
  height: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: white;
  color: #333;
}

.border-solid  { border: 2px solid #2c3e50; }
.border-dashed { border: 2px dashed #2c3e50; }
.border-dotted { border: 2px dotted #2c3e50; }
.border-double { border: 4px double #2c3e50; }

.radius-4  { border: 2px solid #2c3e50; border-radius: 4px; }
.radius-8  { border: 2px solid #2c3e50; border-radius: 8px; }
.radius-16 { border: 2px solid #2c3e50; border-radius: 16px; }
.radius-pill { border: 2px solid #2c3e50; border-radius: 999px; }

.circle {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background: #3498db;
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  border: 2px solid white;
  box-shadow: 0 1px 3px rgba(0,0,0,0.2);
}

.alert {
  padding: 15px 20px;
  border-radius: 5px;
  margin-bottom: 10px;
}

.alert-info    { background: #e8f4fd; border-left: 4px solid #3498db; color: #1c5680; }
.alert-success { background: #e8f8ec; border-left: 4px solid #2ecc71; color: #1a6b3a; }
.alert-warning { background: #fdf3e7; border-left: 4px solid #f39c12; color: #8a5300; }
.alert-danger  { background: #fdecea; border-left: 4px solid #e74c3c; color: #8a2620; }

.btn {
  padding: 10px 20px;
  background: #3498db;
  color: white;
  border: 1px solid #3498db;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1rem;
  margin-right: 10px;
}

.btn:hover { background: #2980b9; border-color: #2980b9; }

.btn-outline {
  background: transparent;
  color: #3498db;
}

.btn-outline:hover {
  background: #3498db;
  color: white;
}

.btn-pill {
  border-radius: 999px;
}
```

Save and open. Notice the variety of borders and shapes you can create with just these properties.

---

## Try It Yourself

### Exercise 1: Border Style Sampler

Build a page showing all 10 border styles side by side. Compare how they look at different widths (1px, 2px, 5px, 10px).

### Exercise 2: Avatar Component

Build an avatar component:
- 80×80 px circle.
- 3px white border.
- Subtle shadow.
- Centered initials inside.

### Exercise 3: Alert System

Build a reusable alert component with four variants (info, success, warning, danger). Each has:
- Light background color.
- Left accent border.
- Darker text color matching the border.

### Exercise 4: Tab Navigation

Build a tab navigation component with three tabs. The active tab has a different border treatment (top accent or no bottom border).

---

## Common Mistakes

### Mistake 1: Border without style

```css
/* BAD: no style, no border shows */
.box { border: 2px black; }
```

Without `border-style` (or a shorthand that includes it), the border doesn't render. The default style is `none`.

### Mistake 2: Forgetting width

```css
/* BAD: no width */
.box { border: solid black; }
```

Default width is `medium` (~3px). It works, but explicit widths are clearer:

```css
.box { border: 2px solid black; }
```

### Mistake 3: Removing `outline` from focusable elements

```css
/* BAD: removes keyboard accessibility */
button:focus { outline: none; }
```

If you remove the default outline, provide an alternative:

```css
button:focus {
  outline: none;
  box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.5);
}
```

### Mistake 4: Circle without overflow hidden

```css
.avatar {
  border-radius: 50%;
  width: 60px;
  height: 60px;
  /* missing overflow: hidden */
}
```

If the avatar contains an image, the image will be square and poke out of the rounded corners. Add `overflow: hidden` or use `object-fit: cover` on the image.

### Mistake 5: Trying to make a circle from a non-square element

```css
.badge {
  width: 100px;
  height: 50px;
  border-radius: 50%;
}
```

This makes an ellipse, not a circle. To make a circle, width must equal height.

### Mistake 6: Per-side radius confusion

```css
/* This is TL TR BR BL, not TRBL */
.box { border-radius: 10px 5px 10px 5px; }
```

Border-radius uses TRBL-clockwise-from-top-left, but it's specifically: TL, TR, BR, BL.

### Mistake 7: Using `border: 0` to remove default borders

```css
/* Works, but more explicit: */
button { border: 0; }

/* Better: */
button { border: none; }
```

`border: none` is more readable for "no border at all". `border: 0` means width zero. Both work; `none` is preferred for clarity.

---

## Summary

- Every border has three parts: width, style, color. Use the shorthand `border: 2px solid black`.
- The 10 styles are: solid, dashed, dotted, double, groove, ridge, inset, outset, none, hidden.
- Style each side independently with `border-top`, `border-right`, `border-bottom`, `border-left`.
- `border-radius` rounds corners. Per-corner: TL, TR, BR, BL.
- A `border-radius: 50%` on a square creates a circle.
- A `border-radius: 9999px` on a button creates a pill shape.
- `outline` is like border but doesn't take space; use for focus states.
- Common UI patterns: cards, alerts, dividers, buttons, avatars, tabs.

In the next lesson, we'll learn about backgrounds — colors, images, gradients, and positioning.

---

**Next:** [Lesson 09: Backgrounds →](./09-backgrounds.md)
