# Lesson 13: HTML Cheatsheet

> Module: HTML · Lesson 13 of 13
> Print this page or bookmark it — this is your one-page reference for every HTML tag you've learned in this module.

---

## 1. The Document Skeleton

Every HTML page starts with this exact structure. Memorize it.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
  </head>
  <body>
    <!-- Visible content goes here -->
  </body>
</html>
```

---

## 2. Head & Metadata

| Tag | Purpose | Example |
|-----|---------|---------|
| `<head>` | Container for metadata | `<head>...</head>` |
| `<title>` | Page title (browser tab, SEO) | `<title>My Page</title>` |
| `<meta charset>` | Character encoding | `<meta charset="UTF-8">` |
| `<meta name="viewport">` | Mobile responsiveness | `<meta name="viewport" content="width=device-width, initial-scale=1.0">` |
| `<meta name="description">` | SEO description | `<meta name="description" content="...">` |
| `<link rel="stylesheet">` | Link to CSS | `<link rel="stylesheet" href="style.css">` |
| `<link rel="icon">` | Favicon | `<link rel="icon" href="favicon.ico">` |
| `<script src>` | Link to JS | `<script src="app.js" defer></script>` |
| `<style>` | Inline CSS (avoid) | `<style>body { ... }</style>` |

---

## 3. Headings & Paragraphs

| Tag | Purpose |
|-----|---------|
| `<h1>` to `<h6>` | Headings (h1 = most important) |
| `<p>` | Paragraph |
| `<br>` | Line break (void) |
| `<hr>` | Horizontal rule / thematic break (void) |

```html
<h1>Main Page Title</h1>
<h2>Section Heading</h2>
<h3>Subsection</h3>
<p>A paragraph of text.</p>
<p>Line one<br>Line two</p>
```

---

## 4. Text Formatting

### Semantic (preferred)

| Tag | Effect | Use |
|-----|--------|-----|
| `<strong>` | Bold | Important text |
| `<em>` | Italic | Emphasized text |
| `<mark>` | Highlighted | Marked as relevant |
| `<del>` | Strikethrough | Deleted content |
| `<ins>` | Underline | Inserted content |
| `<sub>` | Subscript | H₂O |
| `<sup>` | Superscript | πr² |
| `<small>` | Smaller | Side comments |
| `<abbr>` | With title attribute | `<abbr title="World Wide Web">WWW</abbr>` |
| `<code>` | Monospace | Inline code |
| `<kbd>` | Keyboard style | `<kbd>Ctrl</kbd> + <kbd>S</kbd>` |
| `<blockquote>` | Indented block | Long quotes |
| `<q>` | Quoted inline | Short quotes |
| `<cite>` | Italic | Titles of works |

### Presentational (use rarely)

| Tag | Effect |
|-----|--------|
| `<b>` | Bold |
| `<i>` | Italic |
| `<u>` | Underline |
| `<s>` | Strikethrough |

---

## 5. Links

| Pattern | Purpose |
|---------|---------|
| `<a href="url">Text</a>` | Basic link |
| `<a href="https://...">` | Absolute (external) |
| `<a href="page.html">` | Relative (same site) |
| `<a href="../page.html">` | Relative (up one folder) |
| `<a href="#section">` | In-page anchor |
| `<a href="page.html#section">` | Anchor on another page |
| `<a href="mailto:hi@example.com">` | Email link |
| `<a href="tel:+84123">` | Phone link |
| `<a href="file.pdf" download>` | Download link |
| `<a href="..." target="_blank" rel="noopener noreferrer">` | Open in new tab (always include `rel`) |

```html
<a href="https://example.com" target="_blank" rel="noopener noreferrer">
  External link opens in new tab
</a>
```

---

## 6. Images & Media

| Tag | Purpose |
|-----|---------|
| `<img>` | Image (void) |
| `<figure>` | Captioned content |
| `<figcaption>` | Caption for figure |
| `<picture>` | Responsive images |
| `<video>` | Embedded video |
| `<audio>` | Embedded audio |
| `<iframe>` | Embedded page |
| `<svg>` | Inline SVG graphic |
| `<canvas>` | JS-drawn graphic |

```html
<img src="photo.jpg" alt="Description" width="400" height="300" loading="lazy">

<figure>
  <img src="chart.png" alt="...">
  <figcaption>Quarterly sales chart.</figcaption>
</figure>
```

---

## 7. Lists

### Unordered (bullets)
```html
<ul>
  <li>Item 1</li>
  <li>Item 2</li>
</ul>
```

### Ordered (numbers)
```html
<ol>
  <li>First</li>
  <li>Second</li>
</ol>
```

### Ordered with attributes
```html
<ol type="A" start="3" reversed>
  <li>Item</li>
</ol>
```
- `type`: `1` (default), `A`, `a`, `I`, `i`
- `start`: number to start from
- `reversed`: count backwards

### Description List
```html
<dl>
  <dt>Term</dt>
  <dd>Definition</dd>
</dl>
```

### Nested Lists
```html
<ul>
  <li>
    Parent
    <ul>
      <li>Child</li>
    </ul>
  </li>
</ul>
```

---

## 8. Tables

```html
<table>
  <caption>Table title</caption>
  <thead>
    <tr>
      <th scope="col">Header</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Data</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>Footer</td>
    </tr>
  </tfoot>
</table>
```

| Tag | Purpose |
|-----|---------|
| `<table>` | Table wrapper |
| `<tr>` | Row |
| `<th>` | Header cell |
| `<td>` | Data cell |
| `<thead>` | Header section |
| `<tbody>` | Body section |
| `<tfoot>` | Footer section |
| `<caption>` | Table title |
| `<colgroup>` | Column group |
| `<col>` | Single column |

Cell merging:
- `colspan="2"` — merge across columns
- `rowspan="3"` — merge across rows

Accessibility: always add `scope="col"` or `scope="row"` to `<th>`.

---

## 9. Forms

### Form structure
```html
<form action="/submit" method="POST" enctype="multipart/form-data">
  <fieldset>
    <legend>Group Title</legend>
    <label for="x">Label:</label>
    <input type="text" id="x" name="x" required>
  </fieldset>
  <button type="submit">Submit</button>
</form>
```

### Input types
| Type | Purpose |
|------|---------|
| `text` | Single-line text |
| `email` | Email (validated) |
| `password` | Masked password |
| `number` | Number with spinner |
| `date` | Date picker |
| `time` | Time picker |
| `datetime-local` | Date + time picker |
| `month`, `week` | Month/week picker |
| `color` | Color picker |
| `range` | Slider |
| `checkbox` | Toggle box |
| `radio` | One-of-many selection |
| `file` | File upload |
| `hidden` | Invisible field |
| `submit` | Submit button |
| `reset` | Reset button |
| `button` | Generic button (needs JS) |
| `tel` | Phone number |
| `url` | URL (validated) |
| `search` | Search field |

### Other form elements
| Tag | Purpose |
|-----|---------|
| `<textarea>` | Multi-line text |
| `<select>` | Dropdown |
| `<option>` | Dropdown item |
| `<optgroup>` | Group of options |
| `<button>` | Button |
| `<label>` | Input label |
| `<fieldset>` | Group of fields |
| `<legend>` | Fieldset title |

### Validation attributes
| Attribute | Purpose |
|-----------|---------|
| `required` | Must be filled |
| `min`, `max` | Number/date range |
| `minlength`, `maxlength` | Text length range |
| `pattern` | Regex the value must match |
| `placeholder` | Hint text |
| `checked` | Pre-check a radio/checkbox |
| `selected` | Pre-select an option |
| `disabled` | Disable the field |
| `readonly` | Read-only field |
| `multiple` | Allow multiple selections |

---

## 10. Generic Containers

| Tag | Type | Use |
|-----|------|-----|
| `<div>` | Block | Generic block container |
| `<span>` | Inline | Generic inline container |

Attributes for targeting:
- `class="name"` — reusable, space-separated for multiple
- `id="name"` — unique per page

```html
<div class="card">Block</div>
<p>Text with <span class="highlight">inline</span> styling.</p>
```

---

## 11. Semantic Structure Tags

| Tag | Purpose |
|-----|---------|
| `<header>` | Top of a section/page |
| `<nav>` | Major navigation |
| `<main>` | Main content (one per page) |
| `<footer>` | Bottom of a section/page |
| `<section>` | Thematic grouping with heading |
| `<article>` | Self-contained content |
| `<aside>` | Side content (sidebar) |
| `<figure>` | Captioned content |
| `<figcaption>` | Caption for figure |
| `<address>` | Contact information |

---

## 12. Content Semantic Tags

| Tag | Purpose | Example |
|-----|---------|---------|
| `<time>` | Date/time | `<time datetime="2026-08-22">Aug 22, 2026</time>` |
| `<mark>` | Highlight | `<mark>important</mark>` |
| `<details>` | Collapsible | `<details>...</details>` |
| `<summary>` | Click target inside details | `<summary>Click me</summary>` |
| `<progress>` | Progress bar | `<progress value="50" max="100">` |
| `<meter>` | Static measurement | `<meter value="0.5">` |
| `<blockquote>` | Block quote | `<blockquote cite="...">...</blockquote>` |
| `<q>` | Inline quote | `<q>text</q>` |
| `<cite>` | Citation | `<cite>Book Title</cite>` |
| `<abbr>` | Abbreviation | `<abbr title="...">HTML</abbr>` |
| `<code>` | Inline code | `<code>console.log()</code>` |
| `<pre>` | Preformatted text | `<pre>...</pre>` |

---

## 13. Comments

```html
<!-- This is a comment, ignored by the browser -->
```

```html
<!--
  Multi-line comment.
  Useful for section markers and explanations.
-->
```

---

## 14. HTML Entities (Special Characters)

Some characters are reserved in HTML. To display them as text, use entities.

| Character | Entity | Name |
|-----------|--------|------|
| `<` | `&lt;` | Less than |
| `>` | `&gt;` | Greater than |
| `&` | `&amp;` | Ampersand |
| `"` | `&quot;` | Quotation mark |
| `'` | `&apos;` | Apostrophe |
| ` ` (non-breaking) | `&nbsp;` | Non-breaking space |
| `©` | `&copy;` | Copyright |
| `®` | `&reg;` | Registered trademark |
| `™` | `&trade;` | Trademark |
| `—` | `&mdash;` | Em dash |
| `–` | `&ndash;` | En dash |
| `→` | `&rarr;` | Right arrow |
| `•` | `&bull;` | Bullet |

Example: to display `<p>` as text in HTML, write `&lt;p&gt;`.

---

## 15. Common Attribute Reference

| Attribute | Used on | Purpose |
|-----------|---------|---------|
| `class` | All | CSS/JS targeting (reusable) |
| `id` | All | Unique identifier |
| `style` | All | Inline CSS (avoid) |
| `title` | All | Tooltip on hover |
| `lang` | `<html>` | Page language |
| `dir` | All | Text direction (`ltr`/`rtl`) |
| `tabindex` | All | Keyboard navigation order |
| `hidden` | All | Hide element |
| `data-*` | All | Custom data attributes |

### Link & image specifics
| Attribute | Used on | Purpose |
|-----------|---------|---------|
| `href` | `<a>` | Link URL |
| `target` | `<a>` | Where to open (`_blank`, `_self`) |
| `rel` | `<a>` | Relationship (`noopener`, `noreferrer`) |
| `src` | `<img>`, `<script>` | Source URL |
| `alt` | `<img>` | Description (critical for accessibility) |
| `width`, `height` | `<img>`, `<video>` | Dimensions |
| `loading` | `<img>` | `lazy` or `eager` |
| `srcset` | `<img>` | Multiple sizes for responsive |

### Form specifics
| Attribute | Used on | Purpose |
|-----------|---------|---------|
| `for` | `<label>` | Matches input `id` |
| `name` | All inputs | Field name submitted to server |
| `value` | Most inputs | Default value |
| `placeholder` | Text inputs | Hint text |
| `required` | All inputs | Must be filled |
| `disabled` | All inputs | Disable field |
| `readonly` | Text inputs | Read-only |
| `checked` | Radio, checkbox | Pre-selected |
| `selected` | `<option>` | Pre-selected |
| `multiple` | `<select>`, `<input type="file">` | Allow multiple |
| `accept` | `<input type="file">` | Allowed file types |
| `enctype` | `<form>` | Encoding type for submission |
| `action` | `<form>` | Submit URL |
| `method` | `<form>` | HTTP method (`GET`/`POST`) |

---

## 16. Commonly Deprecated Tags (Don't Use)

| Tag | Why | Modern alternative |
|-----|-----|---------------------|
| `<center>` | Presentational | CSS `text-align: center` |
| `<font>` | Presentational | CSS `font-family`, `color`, `font-size` |
| `<marquee>` | Distracting | CSS animations |
| `<blink>` | Annoying | None — just don't |
| `<frame>`, `<frameset>` | Old layout | CSS layout |
| `<basefont>` | Presentational | CSS |
| `<big>` | Presentational | CSS `font-size` |
| `<tt>` | Presentational | CSS `font-family: monospace` |

---

## 17. HTML Rules to Live By

1. **Always include `<!DOCTYPE html>`** at the top.
2. **Always set `<html lang="...">`** to the page's language.
3. **Always include the viewport meta tag** for mobile.
4. **Every image needs an `alt`** (empty if decorative).
5. **Every input needs a `<label>`** with matching `for` and `id`.
6. **One `<h1>` per page**, in order without skipping levels.
7. **One `<main>` per page**.
8. **Always include `rel="noopener noreferrer"`** when using `target="_blank"`.
9. **Prefer semantic tags** over `<div>`/`<span>` whenever possible.
10. **Close every tag you open** (except void elements like `<br>`, `<img>`, `<input>`).
11. **Indent your code** consistently (2 or 4 spaces).
12. **Validate your HTML** at [validator.w3.org](https://validator.w3.org/).
13. **Never use tables for layout** — only for tabular data.

---

## 18. The 10 Most-Used Tag Groups

If you could only learn 10 tag groups, learn these. They appear on virtually every page. Several groups bundle related tags that you will almost always use together, so we count the group, not each individual tag:

1. `<html>`, `<head>`, `<body>` — the document skeleton (always together)
2. `<p>` — paragraphs of text
3. `<h1>`–`<h3>` — headings (one family of related tags)
4. `<a>` — links
5. `<img>` — images
6. `<ul>`, `<ol>`, `<li>` — lists (parent + items, always together)
7. `<div>` — generic block container
8. `<span>` — generic inline container
9. `<form>`, `<input>` — forms (input never lives outside a form)
10. `<header>`, `<main>`, `<footer>` — semantic structure (always together)

Master these, and you can build 90% of the structure of any web page you can imagine.

---

## 19. What's Next?

You've finished the HTML module! You can now:

- Build a complete, accessible HTML page from scratch.
- Use every common tag with the right semantic meaning.
- Validate your HTML and avoid beginner mistakes.
- Read other people's HTML and understand it.

But the pages you can build right now are ugly — they have default browser styling. To make them look modern, professional, and beautiful, you need CSS. That's the next module.

Open [the CSS module →](../02-css/README.md) when you're ready to make your pages look amazing.

---

## HTML Module Complete

<p align="center">
  <img src="../logorepo.png" alt="Learn Dev Logo" width="100" height="100" />
</p>

<p align="center">
  <strong>Congratulations!</strong> You have completed the HTML module of Learn Dev.
</p>
