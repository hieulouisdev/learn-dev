# Lesson 11: Position

> Module: CSS · Lesson 11 of 16
> Estimated time: 45–60 minutes

The `position` property controls **where** an element is placed on the page and **how** it responds to scrolling. There are five values: `static`, `relative`, `absolute`, `fixed`, and `sticky`. In this lesson, you'll learn each one, when to use it, and how to combine them.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain the five position values: `static`, `relative`, `absolute`, `fixed`, `sticky`.
2. Use `top`, `right`, `bottom`, `left` to offset elements.
3. Use `z-index` to control stacking order.
4. Build a sticky header, a fixed banner, a tooltip, and a modal.
5. Avoid common positioning pitfalls.

---

## 1. The Five Position Values

| Value | Behavior |
|-------|----------|
| `static` | Default. Element flows naturally in the document. |
| `relative` | Like static, but can be offset with `top/right/bottom/left`. |
| `absolute` | Removed from flow. Positioned relative to nearest positioned ancestor. |
| `fixed` | Removed from flow. Positioned relative to viewport (browser window). |
| `sticky` | Hybrid. Scrolls normally until threshold, then sticks. |

Let's go through each in detail.

---

## 2. `position: static` (Default)

Every element starts with `position: static`. The element flows naturally in the document — block elements stack vertically, inline elements flow horizontally.

```css
div { position: static; }   /* default */
```

`top`, `right`, `bottom`, `left`, and `z-index` have **no effect** on static elements.

You almost never need to write `position: static` — it's the default. But it's useful to override a previous position rule:

```css
.card { position: relative; }
.card.no-offset { position: static; }   /* reset */
```

---

## 3. `position: relative`

A relatively-positioned element behaves like static, but you can offset it with `top`, `right`, `bottom`, `left`:

```css
.box {
  position: relative;
  top: 10px;
  left: 20px;
}
```

This moves the box **10px down** and **20px right** from where it would normally be.

### Important: The Original Space Is Reserved

Even though the box is moved, the **space it would have occupied is still reserved**. Other elements don't move to fill the gap. The box "floats" offset from its original spot.

### Negative Offsets

You can use negative values:

```css
.box {
  position: relative;
  top: -20px;   /* moves up 20px */
  left: -10px;  /* moves left 10px */
}
```

### The Main Use of Relative

The most common use of `position: relative` is as a **containing block** for absolutely-positioned children. We'll see this in section 4 below.

### Tooltip Example

```css
.tooltip-trigger {
  position: relative;
}

.tooltip {
  position: absolute;
  bottom: 100%;   /* above the trigger */
  left: 50%;
  transform: translateX(-50%);
  background: black;
  color: white;
  padding: 5px 10px;
  border-radius: 4px;
  display: none;
}

.tooltip-trigger:hover .tooltip {
  display: block;
}
```

The tooltip is positioned absolutely **relative to its parent**, which is `position: relative`. This is the classic relative + absolute pattern.

---

## 4. `position: absolute`

An absolutely-positioned element is **removed from the normal flow**. Other elements act as if it doesn't exist. It's positioned relative to its **nearest positioned ancestor** (an ancestor with `position: relative`, `absolute`, `fixed`, or `sticky`).

```css
.tooltip {
  position: absolute;
  top: 100%;
  left: 0;
}
```

If no ancestor is positioned, the element is positioned relative to the initial containing block (the viewport, roughly).

### The "Containing Block" Rule

The most important concept: **an absolutely positioned element is positioned relative to its nearest positioned ancestor**.

```html
<div class="parent">   <!-- position: relative -->
  <div class="child">  <!-- position: absolute -->
    I'm positioned relative to .parent.
  </div>
</div>
```

If `.parent` is `position: relative` and `.child` is `position: absolute` with `top: 0; left: 0`, the child sits at the top-left corner of the parent.

Without `position: relative` on the parent, the child would position relative to the viewport (or `<html>`).

### The Relative + Absolute Pattern

This is one of the most common patterns in CSS:

```css
.parent { position: relative; }
.child  { position: absolute; top: 10px; left: 20px; }
```

Use cases:
- Tooltips that appear above a button.
- Badges in the corner of a card.
- Modals centered on screen.
- Close buttons in the top-right of a dialog.

### Centering an Absolutely Positioned Element

```css
.modal {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

This centers the modal both horizontally and vertically. (For new browsers, you can also use flexbox on the parent, which is simpler.)

### Width and Height

Absolute elements shrink to fit their content by default. Set explicit `width`/`height` if needed:

```css
.modal {
  position: absolute;
  width: 400px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

### Badge Example

```html
<div class="card">
  <span class="badge">New</span>
  <h3>Card Title</h3>
  <p>Card description.</p>
</div>
```

```css
.card { position: relative; padding: 20px; }
.badge {
  position: absolute;
  top: -10px;
  right: -10px;
  background: gold;
  color: black;
  padding: 5px 10px;
  border-radius: 12px;
  font-size: 0.8em;
}
```

The badge sits at the top-right corner of the card, slightly outside it.

---

## 5. `position: fixed`

A fixed element is positioned relative to the **viewport** (the browser window). It stays in place even when the page scrolls.

```css
.banner {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: #2c3e50;
  color: white;
  padding: 10px;
  text-align: center;
}
```

A banner fixed to the bottom of the screen.

### Fixed Header

```css
.header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  background: white;
  border-bottom: 1px solid #ddd;
  padding: 15px 30px;
  z-index: 100;
}
```

The header stays at the top of the viewport when scrolling.

### Caveat: Mobile Issues

On mobile, `position: fixed` can be glitchy when the on-screen keyboard appears or when the browser's address bar shows/hides. Test on real devices.

### Caveat: Transformed Ancestors

If an ancestor of a fixed element has a `transform`, `filter`, or `perspective` property, the fixed element becomes relative to that ancestor instead of the viewport. This is a common source of bugs.

---

## 6. `position: sticky`

A hybrid of relative and fixed. The element starts as `relative` (in normal flow), then becomes "fixed" when scrolling reaches a threshold.

```css
.header {
  position: sticky;
  top: 0;
  background: white;
  border-bottom: 1px solid #ddd;
  padding: 15px;
}
```

The header scrolls normally until it reaches the top of the viewport, then sticks there.

### Sticky Sidebar Items

```css
.sidebar-section {
  position: sticky;
  top: 20px;   /* sticks 20px from top */
}
```

### Sticky vs Fixed

- `fixed`: always stuck to the viewport.
- `sticky`: starts in normal flow, sticks when scrolled past.

Use sticky for headers that should be visible while scrolling but don't need to be there when at the top.

### Sticky Caveats

- Sticky doesn't work inside containers with `overflow: hidden` or `overflow: auto` (in some browsers).
- The element's parent must be taller than the element, otherwise there's nothing to "scroll past."
- Sticky elements stop sticking when their parent's bottom is reached.

---

## 7. The `top`, `right`, `bottom`, `left` Properties

These properties offset positioned elements. They work for `relative`, `absolute`, `fixed`, and `sticky` (not `static`).

```css
.box {
  position: absolute;
  top: 10px;     /* 10px from top of containing block */
  right: 20px;   /* 20px from right */
  bottom: 0;     /* at bottom */
  left: 0;       /* at left */
}
```

### To "Stretch" an Element

If you set all four:

```css
.modal-overlay {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background: rgba(0,0,0,0.5);
}
```

The element fills the entire viewport. Useful for modal overlays.

### Use `inset` Shorthand (Modern)

```css
.modal-overlay {
  position: fixed;
  inset: 0;   /* same as top:0; right:0; bottom:0; left:0 */
}
```

`inset: 10px 20px 30px 40px` follows the same TRBL pattern as padding/margin.

---

## 8. `z-index` — Stacking Order

When elements overlap, `z-index` decides which appears on top. Higher `z-index` = on top.

```css
.modal { z-index: 1000; }
.overlay { z-index: 999; }
.header { z-index: 100; }
```

### z-index Rules

1. `z-index` only works on **positioned elements** (`relative`, `absolute`, `fixed`, `sticky`). It's ignored on `static`.
2. Default is `auto`, which means "same level as parent".
3. Higher number = on top.
4. Negative numbers go behind.
5. Within the same stacking context, z-index is compared directly.

### Stacking Contexts

A new stacking context is created by:
- Root element (`<html>`)
- Positioned elements with `z-index` other than `auto`
- Elements with `opacity` less than 1
- Elements with `transform`, `filter`, `perspective`
- Flex/grid children with `z-index` other than `auto`

Inside a stacking context, child `z-index` values are scoped — they don't escape their parent.

This is one of the most confusing parts of CSS. The general rule: **if your z-index isn't working, it's probably because a parent created a new stacking context.**

### Common z-index Scale

A common scale to avoid chaos:

```css
:root {
  --z-base: 1;
  --z-dropdown: 100;
  --z-sticky: 200;
  --z-modal: 1000;
  --z-toast: 1100;
}
```

Use these variables for consistency.

---

## 9. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Position Demo</title>
    <link rel="stylesheet" href="position.css">
  </head>
  <body>
    <header class="site-header">
      <h1>Position Demo</h1>
    </header>

    <main>
      <section>
        <h2>Relative</h2>
        <p>The "LOL" word below is offset 10px up and 5px right from its normal position.</p>
        <p>Look at this <span class="relative-box">LOL</span> word.</p>
      </section>

      <section>
        <h2>Absolute</h2>
        <div class="card">
          <span class="badge">NEW</span>
          <h3>Absolutely-Positioned Badge</h3>
          <p>The badge in the corner is positioned absolutely relative to the card.</p>
        </div>
      </section>

      <section>
        <h2>Sticky</h2>
        <p>Scroll down. The header sticks to the top.</p>
        <p style="height: 800px">Spacer paragraph to make page tall enough to scroll.</p>
      </section>

      <section>
        <h2>Fixed Banner</h2>
        <p>There's a fixed banner at the bottom of the screen.</p>
      </section>
    </main>

    <div class="banner">
      Fixed banner at the bottom of the viewport.
    </div>

    <!-- Modal example (initially hidden) -->
    <div class="modal-overlay">
      <div class="modal">
        <h3>Modal Title</h3>
        <p>This is a modal. It's positioned absolutely, centered in the viewport.</p>
      </div>
    </div>
  </body>
</html>
```

```css
/* position.css */

* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: Arial, sans-serif;
  background: #f8f9fa;
  color: #333;
  line-height: 1.6;
  padding-bottom: 60px;   /* room for fixed banner */
}

.site-header {
  position: sticky;
  top: 0;
  background: #2c3e50;
  color: white;
  padding: 15px 30px;
  z-index: 100;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

main { padding: 30px; max-width: 800px; margin: 0 auto; }

section { margin-bottom: 40px; }
h1, h2, h3 { color: #2c3e50; margin-bottom: 10px; }

.relative-box {
  position: relative;
  top: -10px;
  left: 5px;
  background: yellow;
  padding: 2px 5px;
}

.card {
  position: relative;
  background: white;
  padding: 25px;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}

.badge {
  position: absolute;
  top: -12px;
  right: -12px;
  background: gold;
  color: black;
  padding: 5px 12px;
  border-radius: 20px;
  font-size: 0.8em;
  font-weight: bold;
  box-shadow: 0 2px 4px rgba(0,0,0,0.2);
}

.banner {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: #34495e;
  color: white;
  padding: 15px;
  text-align: center;
  z-index: 50;
  box-shadow: 0 -2px 6px rgba(0,0,0,0.2);
}

.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
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
  box-shadow: 0 10px 30px rgba(0,0,0,0.3);
}
```

Save and open. Scroll the page — the header sticks to the top. The badge sits at the card's corner. The banner stays at the bottom. The modal overlays everything.

---

## Try It Yourself

### Exercise 1: Sticky Header

Build a long page with a header that sticks to the top when scrolling.

### Exercise 2: Badge on a Card

Build a card with a "NEW" badge positioned at the top-right corner, slightly outside the card.

### Exercise 3: Modal Dialog

Build a modal that:
- Has a dark overlay filling the viewport.
- Centers a dialog box inside it.
- Has a close button in the top-right corner of the dialog.

### Exercise 4: Tooltip on Hover

Build a button with a tooltip that appears on hover, positioned above the button.

---

## Common Mistakes

### Mistake 1: Forgetting `position: relative` on the parent

```css
/* BAD: parent has no position, so .badge positions relative to viewport */
.card { padding: 20px; }
.badge { position: absolute; top: 0; right: 0; }
```

If you want `.badge` to position relative to `.card`, set `.card { position: relative; }`.

### Mistake 2: Using `z-index` on static elements

```css
/* WON'T WORK */
.box { z-index: 10; }  /* no position - z-index is ignored */
```

Set `position: relative` (or any non-static position) first.

### Mistake 3: Forgetting to reserve space for sticky/fixed

A fixed header overlays the content beneath it. Add `padding-top` to the body to push content below the header:

```css
body { padding-top: 60px; }
.header { position: fixed; top: 0; height: 60px; }
```

### Mistake 4: Sticky inside overflow container

```css
/* Sticky may not work */
.container { overflow: hidden; }
.sticky-element { position: sticky; top: 0; }
```

Sticky can fail inside containers with `overflow: hidden`, `auto`, or `scroll`. Move the sticky element outside the overflow container, or change the overflow.

### Mistake 5: Transformed ancestor changing fixed behavior

```css
/* Transformed parent breaks fixed children */
.parent { transform: translate(0, 0); }
.child { position: fixed; top: 0; }   /* now relative to parent, not viewport */
```

Any ancestor with `transform`, `filter`, or `perspective` becomes the containing block for fixed elements. Avoid transforms on parents of fixed elements.

### Mistake 6: Big z-index numbers everywhere

Don't use `z-index: 99999` everywhere. Use a consistent scale (e.g., 1, 10, 100, 1000). Manage stacking contexts carefully.

### Mistake 7: Forgetting that absolute removes from flow

```html
<div class="container">
  <div class="absolute-child">...</div>
  <p>...</p>
</div>
```

The absolutely-positioned child doesn't take space. The paragraph below sits on top of where the child would have been. Add padding to the container if needed.

---

## Summary

- `static` (default): natural flow. `top/right/bottom/left` ignored.
- `relative`: offset from natural position, but space is reserved.
- `absolute`: removed from flow, positioned relative to nearest positioned ancestor.
- `fixed`: removed from flow, positioned relative to viewport.
- `sticky`: starts in flow, becomes "fixed" when scrolling past threshold.
- Use `position: relative` on parents of `absolute` children to control their containing block.
- `top/right/bottom/left` offset positioned elements.
- `inset` shorthand sets all four at once.
- `z-index` controls stacking order; only works on positioned elements.
- Stacking contexts can trap z-index — beware of `transform`, `opacity`, `filter`.

In the next lesson, we'll cover **flexbox** — the modern, flexible layout system that solves 90% of CSS layout problems.

---

**Next:** [Lesson 12: Flexbox →](./12-flexbox.md)
