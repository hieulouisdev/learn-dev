# Lesson 02: How to Add CSS

> Module: CSS · Lesson 2 of 16
> Estimated time: 30–45 minutes

There are three ways to add CSS to a web page. In this lesson, you will learn all three, understand the trade-offs of each, and learn why professional developers almost always use **external** stylesheets.

---

## Learning Objectives

After this lesson, you will be able to:

1. Add inline CSS using the `style` attribute.
2. Add internal CSS using the `<style>` element.
3. Add external CSS using the `<link>` element.
4. Explain the trade-offs of each approach.
5. Choose the right approach for any situation.
6. Set up a multi-page project with a shared external stylesheet.

---

## 1. Inline CSS — The `style` Attribute

Inline CSS applies styles directly to a single element using the `style` attribute:

```html
<p style="color: navy; font-size: 18px;">This is a styled paragraph.</p>
```

The styles go inside the `style` attribute as `property: value;` pairs, separated by spaces or newlines.

### Pros

- Useful for quick one-off tests.
- The styles apply to only one element — no risk of accidentally affecting others.

### Cons

- **Mixes content with presentation** — bad practice in general.
- **Not reusable** — if you want the same style on 10 paragraphs, you have to repeat it 10 times.
- **No way to style pseudo-classes** like `:hover` (no inline equivalent).
- **High specificity** — inline styles override almost everything else, making them hard to override with CSS later.
- **No media queries** — can't do responsive design inline.

### When to Use Inline

Almost never. Use it for quick demos, testing, or HTML emails (which have weird CSS limitations). In normal web development, avoid inline styles.

---

## 2. Internal CSS — The `<style>` Element

Internal CSS goes inside a `<style>` element in the HTML's `<head>`:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Internal CSS Demo</title>
    <style>
      body {
        background: #f0f0f0;
        font-family: Arial, sans-serif;
      }
      h1 {
        color: navy;
      }
      p {
        font-size: 18px;
        line-height: 1.6;
      }
    </style>
  </head>
  <body>
    <h1>Hello, CSS!</h1>
    <p>This page uses internal CSS.</p>
  </body>
</html>
```

### Pros

- Styles in one place, at the top of the file.
- Affects every element on the page.
- No extra HTTP request.

### Cons

- **Not reusable across pages** — if you have 10 pages, you'd have to copy the `<style>` block into all 10.
- **Mixes HTML and CSS** in the same file (though they're at least separated by the `<style>` tag).
- **Cache inefficiency** — the browser has to re-download the CSS with every page load, since it's part of the HTML.

### When to Use Internal

- Quick single-page demos.
- Styles that only apply to one specific page (e.g., a unique plugin or template).
- Email templates (with many limitations).
- Performance-critical "above the fold" CSS that you want to load instantly.

For most sites, prefer external stylesheets.

---

## 3. External CSS — The `<link>` Element

External CSS lives in a separate `.css` file linked from your HTML:

### `index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>External CSS Demo</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <h1>Hello, CSS!</h1>
    <p>This page uses external CSS.</p>
  </body>
</html>
```

### `style.css`

```css
body {
  background: #f0f0f0;
  font-family: Arial, sans-serif;
}

h1 {
  color: navy;
}

p {
  font-size: 18px;
  line-height: 1.6;
}
```

The `<link>` element tells the browser: "load the file at `href` and treat it as a stylesheet." The `rel="stylesheet"` attribute describes the relationship.

### Pros

- **Reusable across pages** — link the same `style.css` from every page.
- **Cleaner HTML** — your HTML file stays focused on structure.
- **Better caching** — browsers cache the CSS file, so subsequent page loads are faster.
- **Easier maintenance** — change one rule, all pages update instantly.
- **Team collaboration** — designers can edit CSS while developers edit HTML.

### Cons

- **Extra HTTP request** — the browser must download the CSS file before rendering styled content. (This is usually negligible thanks to caching.)

### When to Use External

Always, in production code. This is the professional standard. Use external stylesheets for all real projects.

---

## 4. Linking Multiple Stylesheets

You can link more than one stylesheet:

```html
<link rel="stylesheet" href="reset.css">
<link rel="stylesheet" href="typography.css">
<link rel="stylesheet" href="layout.css">
<link rel="stylesheet" href="theme.css">
```

They apply in order — if `theme.css` and `layout.css` both set the same property on the same element, the later one wins (assuming equal specificity). This is the cascade in action.

A common pattern is a **CSS reset** (removes default browser styling) followed by your own styles:

```html
<link rel="stylesheet" href="reset.css">
<link rel="stylesheet" href="style.css">
```

---

## 5. The `@import` Rule

You can also import one CSS file from another:

```css
/* style.css */
@import url('reset.css');
@import url('typography.css');

body {
  background: white;
}
```

This works, but it's **slower** than using `<link>` — each `@import` is a separate request that the browser can't start until the parent CSS finishes loading. Prefer `<link>` for performance. Use `@import` only for legacy reasons or for very small projects.

---

## 6. The `media` Attribute

You can specify that a stylesheet only applies under certain conditions:

```html
<link rel="stylesheet" href="mobile.css" media="screen and (max-width: 768px)">
<link rel="stylesheet" href="desktop.css" media="screen and (min-width: 769px)">
<link rel="stylesheet" href="print.css" media="print">
```

- `media="screen"` — applies on screens (default).
- `media="print"` — applies when printing the page.
- `media="screen and (max-width: 768px)"` — applies on screens narrower than 768px (phones).

This is one way to do responsive design. The more common approach is to use `@media` queries inside one stylesheet, which we'll cover in Lesson 14.

---

## 7. Real-World Project Structure

A typical small project:

```
my-project/
├── index.html
├── about.html
├── contact.html
├── css/
│   ├── reset.css       (browser reset)
│   ├── typography.css  (fonts, colors, spacing)
│   ├── layout.css      (positioning, flexbox, grid)
│   └── theme.css       (theme colors, dark mode)
└── js/
    └── main.js
```

Each HTML page links all four CSS files:

```html
<link rel="stylesheet" href="css/reset.css">
<link rel="stylesheet" href="css/typography.css">
<link rel="stylesheet" href="css/layout.css">
<link rel="stylesheet" href="css/theme.css">
```

This separation makes maintenance easier. If you want to change the typography, you edit `typography.css` — you don't have to find the relevant rules buried in a 1000-line stylesheet.

---

## 8. A Complete Example: Multi-Page Site with Shared CSS

Let's build a tiny two-page site with shared external CSS.

### `index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home — My Site</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <header>
      <h1>My Site</h1>
      <nav>
        <a href="index.html">Home</a>
        <a href="about.html">About</a>
      </nav>
    </header>
    <main>
      <h2>Welcome</h2>
      <p>This is the home page. The CSS is shared with the About page.</p>
    </main>
  </body>
</html>
```

### `about.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>About — My Site</Title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <header>
      <h1>My Site</h1>
      <nav>
        <a href="index.html">Home</a>
        <a href="about.html">About</a>
      </nav>
    </header>
    <main>
      <h2>About Me</h2>
      <p>I am learning CSS one lesson at a time.</p>
    </main>
  </body>
</html>
```

### `style.css`

```css
body {
  font-family: Arial, sans-serif;
  background: #f8f9fa;
  color: #333;
  margin: 0;
  padding: 0;
}

header {
  background: #2c3e50;
  color: white;
  padding: 20px;
}

header h1 {
  margin: 0 0 10px 0;
}

nav a {
  color: white;
  margin-right: 15px;
  text-decoration: none;
}

nav a:hover {
  text-decoration: underline;
}

main {
  padding: 30px;
}

h2 {
  color: #2c3e50;
}
```

Save all three files in the same folder. Open `index.html` — the page is styled. Click "About" — the same styling applies. Change something in `style.css` (e.g., change `#2c3e50` to `red`) and refresh — both pages update. That's the power of external CSS.

---

## Try It Yourself

### Exercise 1: Three Ways to Style

Create three small HTML pages, each using one of the three CSS approaches:

1. `inline.html` — uses `style` attributes only.
2. `internal.html` — uses a `<style>` element.
3. `external.html` — links an external `style.css`.

All three should make the page look identical. Then reflect: which was easiest to write? Which would be easiest to maintain if you had 10 pages?

### Exercise 2: Shared Stylesheet

Build a three-page mini-site (Home, About, Contact) with a single shared `style.css`. Confirm that changes to `style.css` apply to all three pages.

### Exercise 3: Print Styles

Add a `print.css` file with `media="print"`. Make it hide the navigation and use a serif font for printing. Use `Ctrl+P` (Cmd+P on Mac) to preview the printed page.

---

## Common Mistakes

### Mistake 1: Wrong path to the CSS file

```html
<!-- WRONG: path is wrong -->
<link rel="stylesheet" href="style.css">
```

If your CSS is in a `css/` folder, the path must be `css/style.css`, not just `style.css`. The path is relative to the HTML file, not the project root.

### Mistake 2: Forgetting `rel="stylesheet"`

```html
<!-- WRONG: no rel attribute -->
<link href="style.css">
```

Without `rel="stylesheet"`, the browser doesn't know what to do with the linked file.

### Mistake 3: Inline styles overriding external CSS

If you have `<p style="color: red">` and `p { color: blue }` in your external CSS, the inline style wins (because inline has higher specificity). Avoid inline styles to prevent this confusion.

### Mistake 4: Putting the `<link>` outside `<head>`

The `<link>` element should always go inside `<head>`. Putting it in `<body>` may work in modern browsers but is invalid HTML.

### Mistake 5: Using `<link>` for non-stylesheet files

`<link>` is for stylesheets, icons, and other document relationships. For JavaScript, use `<script src="...">`, not `<link>`.

### Mistake 6: Multiple `<style>` blocks for the same page

Having multiple `<style>` blocks works, but it's hard to maintain. Combine them into one block, or better, use an external file.

---

## Summary

- Three ways to add CSS: inline (`style` attribute), internal (`<style>` element), external (`<link>` element).
- Inline is for testing only — not reusable, mixes content with presentation, high specificity.
- Internal is for single-page demos — not reusable across pages.
- External is the professional choice — reusable, cacheable, separates concerns.
- Link external stylesheets with `<link rel="stylesheet" href="path.css">`.
- You can link multiple stylesheets in order.
- Use the `media` attribute to apply styles only under certain conditions (screen, print, max-width).
- Avoid `@import` for performance; prefer `<link>`.

In the next lesson, we'll dive deep into CSS selectors — how to target exactly the elements you want to style.

---

**Next:** [Lesson 03: Selectors →](./03-selectors.md)
