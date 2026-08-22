# Lesson 02: HTML Document Structure

> Module: HTML · Lesson 2 of 13
> Estimated time: 30–45 minutes

Every HTML document ever written — from a tiny "Hello, World" page to the homepage of YouTube — has the same skeleton. In this lesson, we will examine that skeleton piece by piece, so you understand exactly what every line does and why it must be there.

---

## Learning Objectives

After this lesson, you will be able to:

1. Write the minimum required HTML document structure from memory.
2. Explain the purpose of `<!DOCTYPE>`, `<html>`, `<head>`, and `<body>`.
3. Use common metadata tags: `<meta charset>`, `<meta viewport>`, `<title>`, and `<link>`.
4. Validate an HTML document to make sure it has no structural errors.

---

## 1. The Minimum Required Structure

Every HTML document must contain this exact skeleton. Memorize it. You will type it dozens of times in your career, and most code editors have a built-in shortcut for it (in VS Code, type `!` and press `Tab`).

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
  </head>
  <body>
    <!-- Everything visible goes here -->
  </body>
</html>
```

Let's examine each piece.

---

## 2. `<!DOCTYPE html>`

```html
<!DOCTYPE html>
```

This is technically not a tag — it is a **declaration**. It tells the browser two things:

1. This is an HTML document (not XML, not something else).
2. The document uses HTML5, the modern standard.

In the dark ages of the web (the late 1990s and 2000s), there were several different HTML versions, and the DOCTYPE was a long, ugly URL pointing to a document type definition. Today it is short and simple. Always use exactly `<!DOCTYPE html>` at the very top of every file.

It is case-insensitive — `<!doctype html>` also works — but the all-uppercase convention is most common.

---

## 3. The Root Element: `<html>`

```html
<html lang="en">
  ... everything else ...
</html>
```

The `<html>` element is the **root** of the document. Every other element in the page must be a descendant of `<html>`. If you put content outside of `<html>`, the browser will try to move it inside, but this is fragile behavior you should never rely on.

The `lang` attribute is important. It tells browsers, screen readers, search engines, and translation tools what language the page is written in. Common values:

- `en` — English
- `vi` — Vietnamese
- `fr` — French
- `es` — Spanish
- `ja` — Japanese

This is not just for accessibility — search engines like Google use it to serve pages to the right audience. Always set it.

---

## 4. The `<head>` Section

The `<head>` element contains **metadata** — information about the page that is not part of the visible content. The most common things inside `<head>`:

### 4.1. Character Encoding: `<meta charset>`

```html
<meta charset="UTF-8">
```

This tells the browser which character encoding to use. UTF-8 supports every character in every language — English, Vietnamese, Chinese, Arabic, emoji, mathematical symbols. Without this line, your browser might misinterpret accented characters and show "garbage text" like `â€™` instead of `'`.

Best practice: **always include this line, and put it first** inside `<head>`. The browser needs to know the encoding before it reads anything else.

### 4.2. Viewport: `<meta name="viewport">`

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

This line is critical for mobile devices. Without it, phones will display your page at desktop width and shrink it down, making it tiny and unreadable. With it, the page renders at the device's actual width, which makes responsive design possible.

- `width=device-width` — set the viewport width to the device's width.
- `initial-scale=1.0` — start at 100% zoom.

If you forget this line, your website will look broken on phones. Always include it.

### 4.3. The Page Title: `<title>`

```html
<title>My Personal Blog</title>
```

The text inside `<title>` appears in three places:

1. The browser tab.
2. The browser's title bar (on browsers that have one).
3. Search engine results as the clickable blue link.

A good title is short, descriptive, and unique per page. "Home" is bad. "Hieu Louis — Personal Blog" is good.

### 4.4. Description: `<meta name="description">`

```html
<meta name="description" content="A blog about learning web development from scratch.">
```

Search engines often use this as the gray snippet under your link in search results. Keep it under 160 characters.

### 4.5. Favicon: `<link rel="icon">`

```html
<link rel="icon" href="favicon.ico" type="image/x-icon">
```

A favicon is the tiny icon that appears in the browser tab next to the title. Without one, the browser shows a default blank or globe icon. Favicons should be 16x16 or 32x32 pixels. The file can be `.ico`, `.png`, or `.svg`.

### 4.6. External CSS: `<link rel="stylesheet">`

```html
<link rel="stylesheet" href="styles.css">
```

This is how you tell the browser to load a CSS file (which we will create in the next module). The `rel` attribute stands for "relationship" — `stylesheet` means "this linked file is a stylesheet." The `href` attribute is the path to the file.

### 4.7. Scripts: `<script src>`

```html
<script src="script.js" defer></script>
```

This loads a JavaScript file. The `defer` attribute tells the browser to download the file but execute it only after the page has finished parsing. We will cover scripts in a later module.

---

## 5. The `<body>` Section

The `<body>` element contains **everything visible on the page**. Headings, paragraphs, images, lists, tables, forms, buttons — all of it goes here.

```html
<body>
  <header>
    <h1>My Blog</h1>
    <nav>
      <a href="/">Home</a>
      <a href="/about">About</a>
    </nav>
  </header>

  <main>
    <article>
      <h2>My First Post</h2>
      <p>This is the start of something great.</p>
    </article>
  </main>

  <footer>
    <p>© 2026 My Blog</p>
  </footer>
</body>
```

These specific tags (`<header>`, `<nav>`, `<main>`, `<article>`, `<footer>`) are called **semantic** tags and we will cover them in Lesson 11. For now, just know they all belong inside `<body>`.

---

## 6. White Space and Indentation

Look at the indentation in the example above — each nested element is indented two spaces from its parent. Browsers do not care about indentation. They would render this:

```html
<!DOCTYPE html><html><head><meta charset="UTF-8"><title>X</title></head><body><h1>Hi</h1></body></html>
```

…exactly the same as a beautifully indented version. But humans are not browsers. **Always indent your code.** Indentation is how you and other developers can see the structure at a glance. The convention is two spaces per nesting level (HTML convention) or four spaces (some prefer this). Pick one and stick with it.

---

## 7. Comments

HTML comments are notes to yourself that the browser ignores completely. They look like this:

```html
<!-- This is a comment. The browser will not render it. -->
```

Everything between `<!--` and `-->` is a comment. You can write notes, mark sections, temporarily disable code, or explain why you wrote something. We will cover comments in detail in Lesson 12.

---

## 8. Putting It All Together: A Complete Example

Here is a complete, real-world HTML document with all the pieces we covered:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="A complete example HTML document for beginners.">
    <title>Complete Example — Learn Dev</title>
    <link rel="icon" href="favicon.ico" type="image/x-icon">
    <link rel="stylesheet" href="styles.css">
  </head>
  <body>
    <header>
      <h1>My Personal Website</h1>
      <p>Welcome to my little corner of the internet.</p>
    </header>

    <main>
      <article>
        <h2>About Me</h2>
        <p>My name is Hieu. I am learning how to build websites.</p>
      </article>
    </main>

    <footer>
      <p>© 2026 Hieu Louis. All rights reserved.</p>
    </footer>

    <script src="script.js" defer></script>
  </body>
</html>
```

Read through this line by line. Make sure you understand what each piece does before moving on.

---

## Try It Yourself

### Exercise 1: Build the Skeleton From Memory

Close this lesson. Open an empty file in your code editor. Try to type the complete HTML skeleton from memory. Open it in a browser. If it works, you have internalized the structure. If you got stuck, come back, read again, and try again. Do this until you can do it without looking.

### Exercise 2: Add Metadata

Take your `index.html` from Lesson 01. Add these elements inside the `<head>`:

- A description meta tag.
- A favicon link (you can use any `.png` file you have, just rename the `href` to point to it).
- A viewport meta tag if you forgot it.

Save and refresh. The page should look the same, but the browser tab should now show your favicon, and the title should match your `<title>`.

### Exercise 3: Validate Your Page

Open [validator.w3.org](https://validator.w3.org/), paste your HTML, and click "Check." If you see any warnings or errors, fix them. This is a great habit — professional developers validate their HTML regularly.

---

## Common Mistakes

### Mistake 1: Putting content inside `<head>`

```html
<!-- WRONG -->
<head>
  <title>My Page</title>
  <p>This is visible content</p>  <!-- ← bad, this belongs in <body> -->
</head>
```

The `<head>` should only contain metadata. Visible content goes in `<body>`. Some browsers will silently move misplaced elements to `<body>`, but do not rely on this — your code will be confusing and error-prone.

### Mistake 2: Forgetting the viewport meta tag

Your page will look broken on phones. Always include `<meta name="viewport" content="width=device-width, initial-scale=1.0">`.

### Mistake 3: Multiple `<html>` tags

There can only be one `<html>`, one `<head>`, and one `<body>` per document.

### Mistake 4: Self-closing the root

```html
<!-- WRONG -->
<html lang="en" />
```

`<html>` must have a separate closing `</html>` tag.

---

## Summary

- Every HTML document has the same skeleton: `<!DOCTYPE html>` → `<html>` → `<head>` + `<body>`.
- `<!DOCTYPE html>` declares the document is HTML5.
- `<html>` is the root element; set its `lang` attribute.
- `<head>` contains metadata: charset, viewport, title, description, favicon, stylesheet links.
- `<body>` contains everything visible.
- Indentation is for humans, not browsers, but always indent.
- HTML comments are written between `<!--` and `-->`.

You now understand the structure of every HTML document. In the next lesson, we will start putting real content inside `<body>` — headings and paragraphs.

---

**Next:** [Lesson 03: Headings and Paragraphs →](./03-headings-and-paragraphs.md)
