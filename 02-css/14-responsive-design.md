# Lesson 14: Responsive Design

> Module: CSS · Lesson 14 of 16
> Estimated time: 60–75 minutes

In 2026, more than half of all web traffic comes from mobile devices. A website that only looks good on a desktop is a broken website. Responsive design is the practice of making your layouts adapt to any screen size — from a tiny phone to a huge desktop monitor. In this lesson, you'll learn the techniques that make this possible.

---

## Learning Objectives

After this lesson, you will be able to:

1. Set the viewport meta tag (already covered in Module 01).
2. Use relative units (`%`, `vw`, `vh`, `rem`, `em`, `clamp()`).
3. Use media queries to apply different styles at different screen sizes.
4. Apply mobile-first design principles.
5. Use responsive images with `srcset` and `picture`.
6. Test responsive layouts in browser DevTools.

---

## 1. The Viewport Meta Tag

We covered this in Module 01, but it's worth repeating. Every HTML page needs:

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

Without it, mobile browsers display the page at desktop width and shrink it down, making it tiny and unreadable. With it, the page renders at the device's actual width.

If your page looks broken on mobile, the viewport meta is the first thing to check.

---

## 2. Relative Units

Stop using fixed `px` for everything. Use relative units for responsiveness.

### `rem` — Root Em

```css
body { font-size: 1rem; }   /* = root font size, usually 16px */
h1 { font-size: 2.5rem; }   /* = 40px if root is 16px */
```

`rem` is relative to the root (`<html>`) font size. Doesn't compound. Respects user accessibility settings (preferred over `px`).

### `em` — Relative to Parent

```css
.parent { font-size: 20px; }
.child  { font-size: 1.5em; }   /* 30px (1.5 * 20) */
```

`em` is relative to the parent's font size. Compounds when nested. Use sparingly — can lead to unexpected scaling.

### `%` — Percentage of Parent

```css
.box { width: 50%; }     /* 50% of parent's width */
.full { width: 100%; }
```

### `vw` and `vh` — Viewport Units

```css
.hero { height: 100vh; }      /* full viewport height */
h1   { font-size: 5vw; }       /* 5% of viewport width */
```

`1vw` = 1% of viewport width. `1vh` = 1% of viewport height.

Useful for full-bleed heroes and large display type. Be careful: `vw` doesn't respect user font size preferences.

### `vmin` and `vmax`

```css
.box { width: 50vmin; }   /* 50% of the smaller viewport dimension */
```

`vmin` = the smaller of vw/vh. `vmax` = the larger.

Useful when you want an element that's a fixed proportion of the smaller screen dimension.

### `ch` — Character Width

```css
.paragraph { max-width: 70ch; }   /* 70 characters wide */
```

`1ch` = the width of the "0" character in the current font. Great for readable line lengths.

---

## 3. `clamp()` — Responsive Font Sizes (and More)

The `clamp()` function lets you set a value with a minimum, preferred, and maximum:

```css
h1 {
  font-size: clamp(2rem, 5vw, 4rem);
  /* min: 2rem, preferred: 5vw, max: 4rem */
}
```

This means:
- On small screens, the font is at least 2rem (32px on default root).
- As the viewport grows, the font grows with `5vw`.
- It never exceeds 4rem (64px) on huge screens.

`clamp()` is one of the most useful modern CSS features. Use it for fluid, responsive type and spacing.

### Use Cases

```css
:root {
  --font-size-body: clamp(1rem, 1.1vw, 1.25rem);
  --space-section: clamp(2rem, 5vw, 4rem);
  --container-width: clamp(280px, 90vw, 1200px);
}

body {
  font-size: var(--font-size-body);
  padding: var(--space-section) 20px;
  max-width: var(--container-width);
  margin: 0 auto;
}
```

---

## 4. Media Queries

Media queries let you apply CSS only under certain conditions:

```css
@media (max-width: 768px) {
  body { font-size: 14px; }
  .sidebar { display: none; }
}
```

The CSS inside the `@media` block only applies when the viewport is **768px wide or narrower**.

### Syntax

```css
@media (condition) {
  /* styles */
}
```

Common conditions:
- `(max-width: 768px)` — viewport 768px or narrower.
- `(min-width: 768px)` — viewport 768px or wider.
- `(orientation: portrait)` — device is in portrait.
- `(orientation: landscape)` — device is in landscape.
- `(prefers-color-scheme: dark)` — user prefers dark mode.

### Combining Conditions

```css
@media (min-width: 768px) and (max-width: 1024px) {
  /* tablet styles */
}

@media (max-width: 768px), (orientation: portrait) {
  /* small screen or portrait */
}
```

---

## 5. Mobile-First Design

There are two strategies for responsive design:

### Desktop-First (Old Way)

Start with desktop styles, then use `max-width` queries to override on smaller screens:

```css
/* Default = desktop */
.container { max-width: 1200px; margin: 0 auto; padding: 0 40px; }

/* Tablet */
@media (max-width: 768px) {
  .container { padding: 0 20px; }
}

/* Mobile */
@media (max-width: 480px) {
  .container { padding: 0 10px; }
}
```

### Mobile-First (Modern Best Practice)

Start with mobile styles, then use `min-width` queries to enhance on larger screens:

```css
/* Default = mobile */
.container { padding: 0 10px; }

/* Tablet and up */
@media (min-width: 768px) {
  .container { padding: 0 20px; }
}

/* Desktop and up */
@media (min-width: 1024px) {
  .container { max-width: 1200px; margin: 0 auto; padding: 0 40px; }
}
```

**Why mobile-first is better:**

1. Mobile devices have less CPU and bandwidth — they shouldn't download desktop styles they'll override.
2. Forces you to design for the most constrained environment first.
3. CSS is "additive" — adding styles for larger screens is cleaner than overriding.

Adopt mobile-first from day one. It's the modern standard.

---

## 6. Common Breakpoints

There's no universal set of breakpoints, but a common scale:

```css
/* Mobile-first default styles */

/* Small tablets - 640px+ */
@media (min-width: 640px) { ... }

/* Tablets - 768px+ */
@media (min-width: 768px) { ... }

/* Small desktops - 1024px+ */
@media (min-width: 1024px) { ... }

/* Large desktops - 1280px+ */
@media (min-width: 1280px) { ... }
```

Tailwind CSS uses these standard breakpoints:

- `sm`: 640px
- `md`: 768px
- `lg`: 1024px
- `xl`: 1280px
- `2xl`: 1536px

Use these as a starting point. Adjust to your design.

### Don't Target Devices

Don't write `@media (max-width: 414px)` for iPhone. New devices come out constantly. Use round numbers (768, 1024) instead of specific device widths.

---

## 7. Responsive Patterns

### Hide on Mobile

```css
.sidebar { /* visible by default */ }

@media (max-width: 768px) {
  .sidebar { display: none; }
}
```

Or, mobile-first:

```css
.sidebar { display: none; }

@media (min-width: 768px) {
  .sidebar { display: block; }
}
```

### Stack to Side-by-Side

Mobile-first: stack by default, side-by-side on larger screens:

```css
/* Mobile: stack */
.cards { display: grid; grid-template-columns: 1fr; gap: 15px; }

/* Tablet+: 2 columns */
@media (min-width: 768px) {
  .cards { grid-template-columns: 1fr 1fr; }
}

/* Desktop+: 3 columns */
@media (min-width: 1024px) {
  .cards { grid-template-columns: 1fr 1fr 1fr; }
}
```

Or simpler, use auto-fit:

```css
.cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 15px;
}
```

One line, fully responsive.

### Navigation: Hamburger Menu

```css
.nav { display: flex; flex-direction: column; }

@media (min-width: 768px) {
  .nav { flex-direction: row; }
  .nav-toggle { display: none; }   /* hide hamburger on desktop */
}
```

### Responsive Images

```css
img { max-width: 100%; height: auto; }
```

This single rule ensures images never exceed their container's width while maintaining aspect ratio.

For responsive source images:

```html
<img
  src="photo-800.jpg"
  srcset="photo-400.jpg 400w, photo-800.jpg 800w, photo-1200.jpg 1200w"
  sizes="(max-width: 600px) 400px, (max-width: 1200px) 800px, 1200px"
  alt="..."
>
```

The browser picks the best image based on screen size.

---

## 8. `prefers-color-scheme` — Dark Mode

```css
@media (prefers-color-scheme: dark) {
  body {
    background: #1a1a1a;
    color: #f0f0f0;
  }
}
```

Applies when the user's OS or browser is set to dark mode. You can also offer a manual toggle with CSS variables.

### With CSS Variables

```css
:root {
  --bg: white;
  --text: black;
}

@media (prefers-color-scheme: dark) {
  :root {
    --bg: #1a1a1a;
    --text: #f0f0f0;
  }
}

body { background: var(--bg); color: var(--text); }
```

---

## 9. `prefers-reduced-motion`

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

Some users have vestibular disorders that make animations cause dizziness. Respect their preference for reduced motion.

---

## 10. Testing Responsiveness

### Browser DevTools

In Chrome, Firefox, or Edge DevTools (`F12`), click the "Toggle device toolbar" icon (or press `Ctrl+Shift+M`). You can:
- Choose from preset device sizes (iPhone, iPad, etc.).
- Drag to resize the viewport.
- Rotate to test portrait/landscape.

### Real Devices

Test on real phones and tablets. Emulators aren't perfect — touch behavior, performance, and rendering can differ.

### Online Tools

- [BrowserStack](https://www.browserstack.com/) (paid, comprehensive)
- [Responsive Viewer](https://responsiveviewer.com/) (free, multiple sizes at once)

---

## 11. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Responsive Demo</title>
    <link rel="stylesheet" href="responsive.css">
  </head>
  <body>
    <header class="header">
      <div class="logo">My Site</div>
      <button class="menu-toggle">☰</button>
      <nav class="nav">
        <a href="#">Home</a>
        <a href="#">About</a>
        <a href="#">Blog</a>
        <a href="#">Contact</a>
      </nav>
    </header>

    <main class="main">
      <section class="hero">
        <h1>Welcome</h1>
        <p>A responsive page that adapts to any screen.</p>
      </section>

      <section class="cards">
        <div class="card">Card 1</div>
        <div class="card">Card 2</div>
        <div class="card">Card 3</div>
      </section>
    </main>

    <footer class="footer">
      <p>© 2026</p>
    </footer>
  </body>
</html>
```

```css
/* responsive.css - mobile first */

* { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --bg: #f8f9fa;
  --text: #333;
  --primary: #2c3e50;
  --accent: #3498db;
}

body {
  font-family: Arial, sans-serif;
  background: var(--bg);
  color: var(--text);
  line-height: 1.6;
}

/* Mobile: hamburger menu, stacked */
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  background: var(--primary);
  color: white;
}

.menu-toggle {
  background: transparent;
  border: none;
  color: white;
  font-size: 1.5rem;
  cursor: pointer;
}

.nav {
  display: none;   /* hidden on mobile */
  flex-direction: column;
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background: var(--primary);
  padding: 15px;
}

.nav a {
  display: block;
  padding: 10px;
  color: white;
  text-decoration: none;
}

.nav a:hover { background: #34495e; }

/* Hero - mobile */
.hero {
  padding: 30px 20px;
  background: linear-gradient(135deg, #667eea, #764ba2);
  color: white;
  text-align: center;
}

.hero h1 {
  font-size: clamp(2rem, 5vw, 3.5rem);
  margin-bottom: 10px;
}

/* Cards - mobile: stack */
.cards {
  display: grid;
  grid-template-columns: 1fr;
  gap: 15px;
  padding: 30px 15px;
}

.card {
  background: white;
  padding: 20px;
  border-radius: 8px;
  border: 1px solid #ddd;
  text-align: center;
}

.footer {
  background: var(--primary);
  color: white;
  text-align: center;
  padding: 20px;
}

/* Tablet+: show nav inline, 2 cards per row */
@media (min-width: 768px) {
  .menu-toggle { display: none; }
  .nav {
    display: flex;
    position: static;
    flex-direction: row;
    background: transparent;
    padding: 0;
  }
  .nav a { padding: 8px 15px; }

  .cards { grid-template-columns: 1fr 1fr; }

  .hero { padding: 60px 30px; }
}

/* Desktop+: 3 cards per row, max-width container */
@media (min-width: 1024px) {
  .cards {
    grid-template-columns: repeat(3, 1fr);
    max-width: 1200px;
    margin: 0 auto;
  }
}
```

Resize the browser to see the layout adapt: hamburger menu on mobile, inline nav on tablet+, stacked cards on mobile, 2 columns on tablet, 3 on desktop.

---

## Try It Yourself

### Exercise 1: Responsive Hero

Build a hero section with:
- Mobile: smaller padding, smaller heading.
- Tablet: medium padding, medium heading.
- Desktop: large padding, large heading.

Use `clamp()` for the heading size to make it fluid.

### Exercise 2: Responsive Navigation

Build a navigation that:
- Mobile: hamburger menu (CSS-only — clicking can require a checkbox hack, or just always show).
- Tablet+: horizontal menu.

### Exercise 3: Responsive Card Grid

Build a card grid that:
- Mobile: 1 card per row.
- Tablet: 2 cards per row.
- Desktop: 3 cards per row.
- Wide desktop: 4 cards per row.

### Exercise 4: Dark Mode

Use `prefers-color-scheme` to make your page switch to dark colors when the user prefers dark mode.

---

## Common Mistakes

### Mistake 1: Forgetting the viewport meta tag

```html
<!-- MISSING -->
<head>
  <title>Page</title>
</head>
```

Without `<meta name="viewport">`, your page looks tiny on mobile. Always include it.

### Mistake 2: Fixed widths everywhere

```css
/* BAD: breaks on small screens */
.container { width: 1200px; }
.sidebar { width: 300px; }
```

Use `max-width` with `width: 100%` instead:

```css
.container { width: 100%; max-width: 1200px; margin: 0 auto; }
```

### Mistake 3: Using `px` for font sizes

```css
/* BAD: ignores user accessibility settings */
body { font-size: 14px; }
```

Use `rem` instead:

```css
body { font-size: 0.875rem; }
```

### Mistake 4: Desktop-first with `max-width` queries

```css
/* OLD WAY: hard to maintain */
.container { padding: 0 40px; }
@media (max-width: 1024px) { .container { padding: 0 30px; } }
@media (max-width: 768px) { .container { padding: 0 20px; } }
@media (max-width: 480px) { .container { padding: 0 10px; } }
```

Use mobile-first with `min-width`:

```css
.container { padding: 0 10px; }
@media (min-width: 480px) { .container { padding: 0 20px; } }
@media (min-width: 768px) { .container { padding: 0 30px; } }
@media (min-width: 1024px) { .container { padding: 0 40px; } }
```

### Mistake 5: Forgetting responsive images

```html
<!-- BAD: huge image on small screen -->
<img src="huge-photo.jpg" alt="...">
```

Use `max-width: 100%; height: auto;` on all images, and consider `srcset` for multiple sizes.

### Mistake 6: Targeting specific devices

```css
/* BAD: targets iPhone 12 only */
@media (max-width: 390px) { ... }
```

Don't target devices. Use round breakpoints (480, 768, 1024).

### Mistake 7: Forgetting touch targets

Mobile users use fingers, not mice. Make buttons and links at least 44×44 pixels for comfortable touch targets.

---

## Summary

- Always include the viewport meta tag.
- Use relative units: `rem` for fonts, `%` for widths, `vw`/`vh` for viewport sizes, `ch` for line lengths.
- Use `clamp(min, preferred, max)` for fluid responsive values.
- Media queries: `@media (condition) { ... }`.
- Common conditions: `min-width`, `max-width`, `orientation`, `prefers-color-scheme`, `prefers-reduced-motion`.
- Use **mobile-first** design: default styles for mobile, `min-width` queries to enhance.
- Common breakpoints: 640, 768, 1024, 1280.
- Use `auto-fit` with `minmax()` for responsive grids without media queries.
- Always make images responsive: `img { max-width: 100%; height: auto; }`.
- Test in DevTools device mode and on real devices.
- Respect user preferences: dark mode, reduced motion.

In the next lesson, we'll add motion with transitions and animations.

---

**Next:** [Lesson 15: Transitions and Animations →](./15-transitions-and-animations.md)
