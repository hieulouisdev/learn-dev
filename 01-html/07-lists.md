# Lesson 07: Lists

> Module: HTML · Lesson 7 of 13
> Estimated time: 30–45 minutes

Lists are everywhere on the web. Navigation menus, recipe ingredients, blog post outlines, search results, comment threads — all are lists. In this lesson, you will learn the three types of HTML lists: unordered, ordered, and description lists.

---

## Learning Objectives

After this lesson, you will be able to:

1. Create unordered lists with `<ul>` and `<li>`.
2. Create ordered lists with `<ol>` and `<li>`.
3. Change list markers (bullets, numbers, letters).
4. Nest lists inside each other for sub-items.
5. Create description lists with `<dl>`, `<dt>`, and `<dd>`.
6. Use lists to build navigation menus.

---

## 1. Unordered Lists: `<ul>` and `<li>`

An unordered list is a list where order doesn't matter — the items are related but not sequential. Bullets are the default marker.

```html
<ul>
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ul>
```

Renders as:

- Coffee
- Tea
- Milk

The `<ul>` (unordered list) wraps the list. Each `<li>` (list item) is one item.

### Common Uses

- Navigation menus (Home, About, Contact — order is irrelevant).
- Bullet points in a presentation.
- Recipe ingredients.
- Features of a product.

---

## 2. Ordered Lists: `<ol>` and `<li>`

An ordered list is a list where order matters — step-by-step instructions, rankings, or any sequence. Numbers are the default marker.

```html
<ol>
  <li>Wake up</li>
  <li>Brush teeth</li>
  <li>Make coffee</li>
  <li>Check email</li>
</ol>
```

Renders as:

1. Wake up
2. Brush teeth
3. Make coffee
4. Check email

Same structure as `<ul>`, just the parent changes to `<ol>`.

### The `start` Attribute

Start counting from a specific number:

```html
<ol start="5">
  <li>Fifth item</li>
  <li>Sixth item</li>
</ol>
```

Renders as 5, 6.

### The `reversed` Attribute

Number items in reverse:

```html
<ol reversed>
  <li>Third</li>
  <li>Second</li>
  <li>First</li>
</ol>
```

Renders as 3, 2, 1.

### The `type` Attribute

Change the marker type:

```html
<ol type="A">  <!-- A, B, C -->
<ol type="a">  <!-- a, b, c -->
<ol type="I">  <!-- I, II, III (Roman) -->
<ol type="i">  <!-- i, ii, iii (lower Roman) -->
<ol type="1">  <!-- 1, 2, 3 (default) -->
```

Example:

```html
<ol type="A">
  <li>First</li>
  <li>Second</li>
</ol>
```

Renders as: A. First, B. Second.

### The `value` Attribute on `<li>`

Override the number of a specific item:

```html
<ol>
  <li>One</li>
  <li value="5">Five</li>
  <li>Six</li>
</ol>
```

Renders as: 1, 5, 6.

---

## 3. Description Lists: `<dl>`, `<dt>`, `<dd>`

A description list pairs terms with their descriptions — like a dictionary. Three tags work together:

- `<dl>` (description list) — the wrapper.
- `<dt>` (description term) — the term being defined.
- `<dd>` (description details) — the definition.

```html
<dl>
  <dt>HTML</dt>
  <dd>HyperText Markup Language — the standard markup language for web pages.</dd>

  <dt>CSS</dt>
  <dd>Cascading Style Sheets — used to style HTML documents.</dd>

  <dt>JavaScript</dt>
  <dd>A programming language for the web.</dd>
</dl>
```

Renders as:

```
HTML
  HyperText Markup Language — the standard markup language for web pages.
CSS
  Cascading Style Sheets — used to style HTML documents.
JavaScript
  A programming language for the web.
```

The browser indents the `<dd>` below the `<dt>`.

### When to Use Description Lists

- Glossaries.
- FAQ sections.
- Key-value displays (e.g., product specs).
- Anything that fits the "term: definition" pattern.

### A Term with Multiple Definitions

A single `<dt>` can have multiple `<dd>`s:

```html
<dl>
  <dt>Java</dt>
  <dd>A programming language.</dd>
  <dd>An island in Indonesia.</dd>
  <dd>A type of coffee.</dd>
</dl>
```

---

## 4. Nesting Lists

You can nest lists inside other lists to create sub-items. The browser indents nested lists automatically.

```html
<ul>
  <li>Fruits</li>
  <li>
    Vegetables
    <ul>
      <li>Carrot</li>
      <li>Spinach</li>
      <li>
        Potatoes
        <ul>
          <li>Sweet potato</li>
          <li>Regular potato</li>
        </ul>
      </li>
    </ul>
  </li>
  <li>Grains</li>
</ul>
```

Renders as:

- Fruits
- Vegetables
  - Carrot
  - Spinach
  - Potatoes
    - Sweet potato
    - Regular potato
- Grains

### Mixing List Types

You can nest an ordered list inside an unordered one:

```html
<ul>
  <li>
    Morning Routine
    <ol>
      <li>Wake up</li>
      <li>Brush teeth</li>
      <li>Make coffee</li>
    </ol>
  </li>
  <li>
    Evening Routine
    <ol>
      <li>Cook dinner</li>
      <li>Read</li>
      <li>Go to bed</li>
    </ol>
  </li>
</ul>
```

The unordered list shows two top-level items (routines), each containing an ordered sub-list (steps).

---

## 5. Lists for Navigation Menus

The most common use of unordered lists is for navigation menus. Here's the typical pattern:

```html
<nav>
  <ul>
    <li><a href="index.html">Home</a></li>
    <li><a href="about.html">About</a></li>
    <li><a href="blog.html">Blog</a></li>
    <li><a href="contact.html">Contact</a></li>
  </ul>
</nav>
```

The `<nav>` element marks this as a navigation section. Inside, an unordered list of links. The default rendering shows vertical bullets, but with a few lines of CSS, you can turn this into a horizontal menu bar.

A dropdown menu uses nested lists:

```html
<nav>
  <ul>
    <li>
      <a href="#">Products</a>
      <ul>
        <li><a href="phones.html">Phones</a></li>
        <li><a href="laptops.html">Laptops</a></li>
        <li><a href="tablets.html">Tablets</a></li>
      </ul>
    </li>
    <li><a href="about.html">About</a></li>
  </ul>
</nav>
```

---

## 6. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lists Demo</title>
  </head>
  <body>
    <h1>Lists Demo</h1>

    <h2>Shopping List (Unordered)</h2>
    <ul>
      <li>Bread</li>
      <li>Milk</li>
      <li>Eggs</li>
      <li>Coffee</li>
    </ul>

    <h2>Morning Routine (Ordered)</h2>
    <ol>
      <li>Wake up at 7 AM</li>
      <li>Drink a glass of water</li>
      <li>Stretch for 5 minutes</li>
      <li>Brush teeth</li>
      <li>Make coffee</li>
    </ol>

    <h2>Glossary (Description List)</h2>
    <dl>
      <dt>HTML</dt>
      <dd>The standard markup language for creating web pages.</dd>

      <dt>CSS</dt>
      <dd>The language used to describe the style of HTML documents.</dd>

      <dt>JavaScript</dt>
      <dd>A programming language that adds interactivity to web pages.</dd>
    </dl>

    <h2>Nested List (Categories)</h2>
    <ul>
      <li>
        Programming Languages
        <ol>
          <li>HTML</li>
          <li>CSS</li>
          <li>JavaScript</li>
        </ol>
      </li>
      <li>
        Tools
        <ul>
          <li>Visual Studio Code</li>
          <li>Git</li>
          <li>Chrome DevTools</li>
        </ul>
      </li>
    </ul>

    <h2>Navigation Menu</h2>
    <nav>
      <ul>
        <li><a href="#">Home</a></li>
        <li><a href="#">Blog</a></li>
        <li><a href="#">About</a></li>
        <li><a href="#">Contact</a></li>
      </ul>
    </nav>
  </body>
</html>
```

Save this as `lists.html` and open it. You'll see all four list types rendered.

---

## Try It Yourself

### Exercise 1: Recipe Page

Build a recipe page with:

- An `<h1>` with the recipe name.
- A short intro paragraph.
- An `<h2>` "Ingredients" with an unordered list.
- An `<h2>` "Instructions" with an ordered list (numbered steps).
- An `<h2>` "Variations" with another unordered list, possibly nested.

### Exercise 2: Glossary Page

Pick three to five technical terms (HTML, CSS, JavaScript, browser, server) and create a description list with their definitions.

### Exercise 3: Sitemap as Nested Lists

Model a small website's structure as a nested unordered list:

- Home
  - Dashboard
  - Profile
- Blog
  - Latest Posts
  - Archive
  - Categories
- About
- Contact

---

## Common Mistakes

### Mistake 1: Forgetting the `<li>` wrapper

```html
<!-- WRONG: list items not wrapped in <li> -->
<ul>
  Bread
  Milk
</ul>
```

Items must be inside `<li>` tags. The browser may render it weirdly if you skip the wrapper.

### Mistake 2: Putting text directly inside `<ul>`

```html
<!-- WRONG -->
<ul>
  Some intro text
  <li>Item 1</li>
</ul>
```

Only `<li>` elements should be direct children of `<ul>` or `<ol>`. If you want intro text, put it before the `<ul>`.

### Mistake 3: Mixing `<ul>` and `<ol>` incorrectly

```html
<!-- WRONG: <ol> cannot be a direct child of <ul> -->
<ul>
  <ol>
    <li>Item</li>
  </ol>
</ul>
```

A nested list must be inside an `<li>`, not as a direct child of another `<ul>` or `<ol>`.

### Mistake 4: Using `<br>` instead of a list

Beginners sometimes write:

```html
<p>
  - Item 1<br>
  - Item 2<br>
  - Item 3
</p>
```

This looks like a list, but it's not — it's just text with hyphens. Use proper `<ul>` and `<li>` so the browser, screen readers, and search engines understand it's a list.

### Mistake 5: Wrapping every menu in `<nav>`

`<nav>` should wrap **major navigation blocks** (the main menu, table of contents, breadcrumbs). It is not for every list of links. Use it for major site navigation.

---

## Summary

- Three list types: unordered (`<ul>`), ordered (`<ol>`), description (`<dl>`).
- All list items use `<li>` inside `<ul>` or `<ol>`.
- Description lists use `<dl>` containing `<dt>` (term) and `<dd>` (description).
- Lists can be nested to create sub-levels. Nested lists must be inside an `<li>`.
- Ordered lists support attributes: `start`, `reversed`, `type` (`1`, `A`, `a`, `I`, `i`).
- Use unordered lists for navigation menus wrapped in `<nav>`.
- Never write lists as plain text with `<br>` or hyphens — always use the proper list tags.

In the next lesson, we will learn how to display tabular data with rows and columns using HTML tables.

---

**Next:** [Lesson 08: Tables →](./08-tables.md)
