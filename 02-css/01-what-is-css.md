# Lesson 01: What is CSS?

> Module: CSS · Lesson 1 of 16
> Estimated time: 30–45 minutes

Welcome to CSS! In this lesson, you will write your very first style rule, see it change a web page instantly, and understand exactly what CSS is and how it works. By the end of this lesson, you will be able to take a plain HTML page and start making it look like a real website.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain what CSS is and what it does.
2. Identify the three parts of a CSS rule: selector, property, and value.
3. Write a basic CSS rule and apply it to an HTML page.
4. Understand why CSS is called "cascading."
5. See the difference CSS makes on a plain HTML page.

---

## 1. What is CSS, Really?

CSS stands for **Cascading Style Sheets**. Let's break down each word.

### Cascading

"Cascading" means that when multiple style rules apply to the same element, the browser uses a priority order — a **cascade** — to decide which rule wins. For example, if one rule says "make this paragraph red" and another says "make this paragraph blue," the cascade picks the winner based on rules we'll learn later (specificity, source order, importance).

### Style

"Style" means appearance. CSS does not change the structure or content of your page — only how that content looks. The HTML stays the same; the CSS controls colors, fonts, spacing, layout, and animations.

### Sheets

"Sheets" refers to the fact that CSS is written in separate files called "style sheets." While you *can* put CSS inside your HTML, the professional approach is to keep it in a separate `.css` file linked from your HTML. This separation lets you reuse the same styles across many pages, change the appearance of an entire site from one file, and keep your HTML clean and readable.

---

## 2. The Anatomy of a CSS Rule

Every CSS rule has the same shape. Memorize this.

```css
selector {
  property: value;
}
```

- **Selector** — which element(s) this rule applies to (e.g., `p` for all paragraphs).
- **Property** — what aspect of the element you want to change (e.g., `color`, `font-size`).
- **Value** — what you want that property to be (e.g., `red`, `16px`).
- **Declaration** — a single `property: value;` pair.
- **Declaration block** — the list of declarations inside the `{ }`.
- **Rule** — the full selector + declaration block.

Let's write a real one:

```css
p {
  color: navy;
  font-size: 18px;
  line-height: 1.6;
}
```

This says: "Every `<p>` element should have navy text color, a font size of 18 pixels, and a line height of 1.6 (which means 1.6 times the font size)."

### Formatting Conventions

The CSS above is formatted for readability:

- The selector is on its own line, followed by `{`.
- Each declaration is on its own line, indented.
- A colon `:` separates property and value.
- A semicolon `;` ends each declaration.
- The closing `}` is on its own line.

The browser doesn't care about formatting — this is also valid:

```css
p { color: navy; font-size: 18px; line-height: 1.6; }
```

But humans care. Always format for readability. Putting multiple declarations on one line is too crowded.

### The Semicolon Rule

Always put a semicolon after every declaration. The last one in a block is technically optional, but always include it — you'll add more declarations later, and forgetting the semicolon between two declarations breaks the second one.

---

## 3. Your First CSS Rule

Let's see CSS in action. Create a folder called `css-demo`. Inside it, create two files: `index.html` and `style.css`.

### `index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My First CSS</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <h1>Hello, CSS!</h1>
    <p>This is my very first page with CSS. The stylesheet is linked.</p>
    <p>Watch what happens when we add rules to <em>style.css</em>.</p>
  </body>
</html>
```

### `style.css` (start empty)

Open `style.css` in your editor and type:

```css
body {
  background-color: #f4f4f4;
  font-family: Arial, sans-serif;
  color: #333;
  margin: 40px;
}

h1 {
  color: #2c3e50;
}

p {
  font-size: 18px;
  line-height: 1.6;
}
```

Save both files. Open `index.html` in your browser. The page now has:

- A light gray background.
- A 40-pixel margin around the whole page.
- An Arial font (with a sans-serif fallback).
- A dark gray text color.
- A dark blue-gray `<h1>` heading.
- Larger paragraph text with comfortable line spacing.

That's the difference CSS makes. The HTML never changed — only the CSS did.

---

## 4. Why "Cascading"?

The "C" in CSS is the most important letter. Let's see the cascade in action. Add another rule to your `style.css`:

```css
p {
  color: navy;
}
```

Now you have two rules for `<p>`:

```css
p {
  font-size: 18px;
  line-height: 1.6;
}

p {
  color: navy;
}
```

The browser applies **both** rules — the paragraphs get the font size and line height from the first rule, and the color from the second. The cascade doesn't pick one rule over another; it merges them.

But what if two rules conflict?

```css
p {
  color: navy;
}

p {
  color: red;
}
```

Now both rules try to set the `color`. The cascade picks the **last one that applies** — so the paragraphs are red. Order matters.

We will explore the full cascade rules (specificity, importance, source order) in later lessons. For now, remember: **when rules conflict, the browser uses a defined priority system to choose the winner.**

---

## 5. Before and After CSS

Let's look at a real example of what CSS can do. Here's a plain HTML page:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Plain Page</title>
  </head>
  <body>
    <h1>My Blog</h1>
    <nav>
      <a href="#">Home</a>
      <a href="#">About</a>
      <a href="#">Contact</a>
    </nav>
    <article>
      <h2>Hello, World</h2>
      <p>Welcome to my blog. This is a plain HTML page.</p>
    </article>
  </body>
</html>
```

Without CSS, this page looks like a 1995 website. Black text, blue underlined links, default Times New Roman font, no spacing, content stretched across the whole screen.

With CSS, the same HTML becomes a modern, beautiful, readable page. Here's a stylesheet that transforms it:

```css
body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  background: #fafafa;
  color: #222;
  max-width: 800px;
  margin: 0 auto;
  padding: 40px 20px;
  line-height: 1.6;
}

h1 {
  color: #1a1a1a;
  border-bottom: 3px solid #444;
  padding-bottom: 10px;
}

nav {
  margin: 20px 0;
}

nav a {
  margin-right: 15px;
  color: #0066cc;
  text-decoration: none;
}

nav a:hover {
  text-decoration: underline;
}

article {
  background: white;
  padding: 20px 30px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
```

Add this CSS, save, refresh. The transformation is dramatic — and all we changed was the stylesheet.

---

## Try It Yourself

### Exercise 1: Modify the Demo

Take the `css-demo` files from this lesson. Change at least three values in `style.css`:

- Change the background color.
- Change the heading color.
- Change the margin around the body.

Save and refresh. See how each change affects the page.

### Exercise 2: Add a New Rule

Add a rule that styles the `<em>` element inside the paragraph:

```css
em {
  color: orange;
  font-weight: bold;
}
```

Save and refresh. The italic word "style.css" should now be bold and orange.

### Exercise 3: Write Your Own Stylesheet

Take any HTML page you built in Module 01. Create a `style.css` file, link it from the HTML, and write at least five rules to make the page look nicer. Don't worry about making it perfect — just experiment.

---

## Common Mistakes

### Mistake 1: Forgetting the semicolon

```css
/* BAD: missing semicolon */
p {
  color: navy
  font-size: 18px;
}
```

The browser will read this as `color: navy font-size: 18px;` — which is invalid. Always put a semicolon after each declaration.

### Mistake 2: Forgetting the closing `}`

```css
/* BAD: unclosed rule */
p {
  color: navy;

h1 {
  color: red;
}
```

The browser will keep adding declarations to the `<p>` rule. Always close `{}` for every rule.

### Mistake 3: Forgetting to link the stylesheet

If your CSS isn't applying, check the HTML `<head>` for `<link rel="stylesheet" href="style.css">`. The path must be correct — `style.css` if it's in the same folder as the HTML, `../style.css` if it's one folder up.

### Mistake 4: Wrong property name

CSS property names are specific. `font-color` doesn't exist; the property is `color`. `text-size` doesn't exist; it's `font-size`. Typos won't error — the browser silently ignores unknown properties. Always double-check the property name.

### Mistake 5: Wrong value format

```css
/* BAD */
p {
  font-size: 18;  /* missing unit */
  color: navy blue;  /* too many words */
}
```

`font-size` needs a unit (`18px`, `1.2rem`, etc.). `color` takes one color value.

### Mistake 6: Writing CSS inside HTML by mistake

```html
<!-- BAD: this is not how CSS is written -->
<p style="font-size: 18px; color: navy">Hello</p>
```

This is **inline CSS** — valid but discouraged. We'll cover it in Lesson 02.

---

## Summary

- CSS stands for Cascading Style Sheets.
- CSS describes the appearance of HTML — colors, fonts, spacing, layout.
- A CSS rule has the form: `selector { property: value; }`.
- The browser ignores formatting, but humans need it — always format for readability.
- Always use a colon `:` after each property and a semicolon `;` after each value.
- The "cascade" merges rules from different sources and resolves conflicts using a defined priority system.
- Always link your stylesheet from your HTML using `<link rel="stylesheet" href="...">`.

In the next lesson, we'll go deeper into the three ways to add CSS to a page and why external stylesheets are the professional choice.

---

**Next:** [Lesson 02: How to Add CSS →](./02-how-to-add-css.md)
