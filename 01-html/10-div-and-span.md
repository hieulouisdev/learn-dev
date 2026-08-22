# Lesson 10: `<div>` and `<span>` — The Generic Containers

> Module: HTML · Lesson 10 of 13
> Estimated time: 30–45 minutes

Two of the most common HTML elements are also the simplest: `<div>` and `<span>`. They are generic containers that don't carry any inherent meaning. They exist so you have something to attach CSS and JavaScript to when no semantic tag fits. In this lesson, you will learn when to use them and when to prefer a semantic alternative.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain the difference between `<div>` (block-level) and `<span>` (inline).
2. Use both elements to group content for styling.
3. Understand the concept of **block** vs. **inline** elements.
4. Know when to use `<div>` and when to use semantic tags like `<header>`, `<main>`, `<article>`.
5. Apply `class` and `id` attributes to target containers with CSS.

---

## 1. Block vs. Inline Elements

This distinction is fundamental to understanding how HTML renders.

### Block Elements

A **block-level** element takes up the full width available to it and starts on a new line. Examples:

- `<div>`, `<p>`, `<h1>`–`<h6>`, `<ul>`, `<ol>`, `<table>`, `<form>`, `<header>`, `<main>`, `<footer>`, `<section>`, `<article>`.

Think of blocks as boxes stacked on top of each other. Each block fills the available width by default and pushes the next element down.

### Inline Elements

An **inline** element only takes up as much width as its content and does **not** start on a new line. Examples:

- `<span>`, `<a>`, `<strong>`, `<em>`, `<code>`, `<img>`, `<br>`.

Inline elements flow within a line of text, like words in a paragraph.

### Visual Comparison

```html
<p>This is a paragraph. <span>This span is inline</span> and continues the same line.</p>

<div>This div is block.</div>
<div>So is this one. They stack vertically.</div>
```

The two `<div>`s stack vertically because each is a block. The `<span>` sits inside the same line as the surrounding text.

### The Rule

- Want to group elements that should stack vertically or take full width? Use a **block** element (`<div>` or a semantic alternative).
- Want to style a piece of text inline without breaking the line? Use an **inline** element (`<span>`).

---

## 2. The `<div>` Element

`<div>` is the generic block container. It has no meaning of its own — it just groups other elements.

```html
<div class="card">
  <h2>Card Title</h2>
  <p>Card description text.</p>
  <a href="#">Read more</a>
</div>
```

The `<div>` here groups the heading, paragraph, and link into one unit so we can style them together (give them a border, padding, background, etc.) using the `class="card"` selector.

### When to Use `<div>`

- You need a wrapper element for styling (e.g., a card, a container, a row, a column).
- No semantic tag fits the content.
- You're building a layout structure (sidebar, content area).

### When NOT to Use `<div>`

Don't use `<div>` when a semantic tag fits better. We will cover semantic tags in Lesson 11. As a quick preview:

```html
<!-- BAD: every section is a div -->
<div class="header">...</div>
<div class="nav">...</div>
<div class="main">...</div>
<div class="footer">...</div>

<!-- GOOD: use semantic tags -->
<header>...</header>
<nav>...</nav>
<main>...</main>
<footer>...</footer>
```

Semantic tags give meaning to your structure, helping screen readers and search engines. Use them where they fit. Use `<div>` when there's no better fit.

---

## 3. The `<span>` Element

`<span>` is the generic inline container. It has no meaning of its own — it just wraps a piece of inline content for styling.

```html
<p>
  Welcome back, <span class="username">Alice</span>!
  You have <span class="notifications">3</span> new notifications.
</p>
```

Here, the `<span class="username">` lets us make "Alice" bold or a different color without breaking the line. The `<span class="notifications">` lets us style the number "3" specially (e.g., a red badge).

### When to Use `<span>`

- You need to style a word or phrase inside a larger block of text.
- You need to attach JavaScript to a specific portion of inline content.
- No semantic inline tag fits (like `<strong>`, `<em>`, `<a>`).

### When NOT to Use `<span>`

Don't use `<span>` for things that have semantic meaning. If you want emphasis, use `<em>`. If you want importance, use `<strong>`. If you want a link, use `<a>`. Use `<span>` only as a last resort for pure styling.

---

## 4. The `class` and `id` Attributes

`<div>` and `<span>` are useless without attributes that let CSS and JavaScript target them. The two most important are:

### `class`

A reusable label that can be applied to multiple elements:

```html
<div class="card">Card 1</div>
<div class="card">Card 2</div>
<div class="card featured">Card 3 (also featured)</div>
```

In CSS:

```css
.card { background: white; }
.featured { border: 2px solid gold; }
```

Multiple classes are space-separated: `class="card featured"`.

### `id`

A unique identifier for a single element on the page:

```html
<div id="main-content">...</div>
```

In CSS:

```css
#main-content { padding: 20px; }
```

`id`s must be unique — never reuse the same `id` on multiple elements.

### Class vs. ID

- Use `class` for most things. Classes can be reused and combined.
- Use `id` only when an element is truly unique on the page (e.g., `id="main-nav"` for the one and only main navigation).
- Use `id` for in-page anchors (the `href="#section1"` pattern from Lesson 05).
- Prefer `class` for styling (more flexible, easier to override).
- Use `id` for JavaScript targeting when you need to identify a specific element.

---

## 5. A Complete Layout Example

Here is a typical blog layout using `<div>`s, semantic tags, `class`es, and `id`s:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Blog Layout</title>
  </head>
  <body>
    <header class="site-header">
      <div class="container">
        <h1 class="logo">My Blog</h1>
        <nav class="main-nav">
          <a href="#">Home</a>
          <a href="#">Archive</a>
          <a href="#">About</a>
        </nav>
      </div>
    </header>

    <main id="main-content">
      <div class="container">
        <article class="post">
          <h2 class="post-title">My First Post</h2>
          <p class="post-meta">
            By <span class="author">Hieu Louis</span> on
            <time datetime="2026-08-22">August 22, 2026</time>
          </p>
          <p>
            This is the start of an amazing journey. I'm learning HTML
            one <span class="highlight">tag</span> at a time.
          </p>
        </article>

        <article class="post featured">
          <h2 class="post-title">Featured Post</h2>
          <p>Short description.</p>
        </article>
      </div>
    </main>

    <footer class="site-footer">
      <div class="container">
        <p>© 2026 My Blog. All rights reserved.</p>
      </div>
    </footer>
  </body>
</html>
```

Notice the patterns:

- `<header>`, `<main>`, `<footer>`, `<nav>`, `<article>` — semantic tags for major sections.
- `<div class="container">` — generic wrapper for centering content (will be styled with CSS later).
- `<span class="author">`, `<span class="highlight">` — inline styling hooks.
- `<time>` — semantic tag for dates with a machine-readable `datetime` attribute.
- `class="post featured"` — multiple classes on one element.

This is the kind of structure you'll write every day as a web developer.

---

## 6. The Inline-Block Hybrid

Some elements are neither pure block nor pure inline — they are **inline-block**. Examples: `<input>`, `<button>`. They flow inline but accept width/height like blocks. The `<img>` element is technically `display: inline` by default (a "replaced" element), but it behaves like `inline-block` because it accepts width and height — we will clarify this in the CSS module.

You can also make any element `inline-block` with CSS:

```css
.display-card {
  display: inline-block;
  width: 200px;
  vertical-align: top;
}
```

This is useful for things like tag clouds or small inline widgets. We'll cover `display` thoroughly in the CSS module.

---

## Try It Yourself

### Exercise 1: Card Layout

Build a page with three "cards" side by side (using `<div class="card">` for now, even though they'll stack until we add CSS in the next module). Each card has:

- An image (or placeholder).
- A title (`<h3>`).
- A description paragraph.
- A "Read more" link.

Use the `class="card"` on all three. Add a second class `featured` to one of them.

### Exercise 2: Highlight Words

Take a paragraph of your own writing. Wrap three different words in `<span>` tags with three different classes (`highlight`, `keyword`, `warning`). Don't worry about styling them yet — that's CSS.

### Exercise 3: Identify What to Replace

Look at this code. Replace each `<div>` with the most appropriate semantic tag (you'll need to research `<header>`, `<nav>`, `<main>`, `<article>`, `<section>`, `<aside>`, `<footer>` — Google is your friend).

```html
<div class="header">
  <div class="nav">...</div>
</div>
<div class="main">
  <div class="article">...</div>
  <div class="sidebar">...</div>
</div>
<div class="footer">...</div>
```

---

## Common Mistakes

### Mistake 1: "Div-itus" — too many nested `<div>`s

```html
<!-- BAD: div soup -->
<div class="container">
  <div class="row">
    <div class="col">
      <div class="card">
        <div class="card-body">
          <p>Hello</p>
        </div>
      </div>
    </div>
  </div>
</div>
```

Some nesting is fine, but if you have five levels of `<div>` for one piece of content, you're overdoing it. Use semantic tags to break up the structure.

### Mistake 2: Using `<div>` where a semantic tag fits

```html
<!-- BAD -->
<div class="header">...</div>

<!-- GOOD -->
<header>...</header>
```

Always prefer semantic tags (Lesson 11) when they fit.

### Mistake 3: Using `<span>` for blocks

```html
<!-- BAD: span is inline, can't hold block content properly -->
<span class="card">
  <h2>Title</h2>
  <p>Text</p>
</span>
```

`<span>` is inline. If you need a block container, use `<div>` or a semantic block tag.

### Mistake 4: Reusing `id`s

```html
<!-- WRONG -->
<div id="card">Card 1</div>
<div id="card">Card 2</div>  <!-- duplicate id is invalid -->
```

Use `class` for repeatable labels, `id` only for unique elements.

### Mistake 5: Forgetting that `<div>` and `<span>` carry no meaning

A `<div>` tells the browser nothing about the content. A screen reader cannot tell if a `<div>` is a sidebar or a hero banner. Always use a semantic tag when one fits, and reserve `<div>`/`<span>` for cases where you truly need a generic container.

---

## Summary

- `<div>` is a generic block container. It stacks vertically and takes full width.
- `<span>` is a generic inline container. It flows within text without breaking the line.
- **Block elements** start on a new line and fill available width.
- **Inline elements** flow within text and take only their content's width.
- Use `class` for reusable labels (space-separated for multiple).
- Use `id` for unique identifiers (no duplicates on the same page).
- Prefer semantic tags (`<header>`, `<nav>`, `<main>`, `<article>`, `<footer>`, etc.) when they fit. Use `<div>` and `<span>` as fallbacks.

In the next lesson, we will go deeper into all the semantic HTML5 tags and learn how to give your content real meaning.

---

**Next:** [Lesson 11: Semantic HTML →](./11-semantic-html.md)
