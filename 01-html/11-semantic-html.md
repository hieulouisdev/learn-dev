# Lesson 11: Semantic HTML

> Module: HTML · Lesson 11 of 13
> Estimated time: 45–60 minutes

In the early days of the web, developers used `<div>` for everything. Headers, footers, navigation, articles — all `<div>`s with classes like `class="header"`, `class="footer"`. This worked visually, but it told the browser nothing about what each piece of content actually was. **Semantic HTML** fixes that. It gives meaning to your structure, helping screen readers, search engines, and other developers understand your page at a glance.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain what "semantic HTML" means and why it matters.
2. Use structural semantic tags: `<header>`, `<nav>`, `<main>`, `<footer>`, `<section>`, `<article>`, `<aside>`.
3. Use content semantic tags: `<figure>`, `<figcaption>`, `<time>`, `<mark>`, `<details>`, `<summary>`.
4. Build a complete, accessible page structure.
5. Avoid "div soup" by choosing the right semantic tag for each piece of content.

---

## 1. What Does "Semantic" Mean?

**Semantics** is the study of meaning. A semantic HTML element **describes what its content is**, not how it should look.

| Non-Semantic | Semantic |
|--------------|----------|
| `<div class="header">` | `<header>` |
| `<div class="nav">` | `<nav>` |
| `<div class="article">` | `<article>` |
| `<div class="footer">` | `<footer>` |

Both rows produce the same visual output by default. The difference is invisible to sighted users but powerful for:

- **Screen readers**: they announce sections ("navigation", "main content", "footer"), letting blind users jump around the page.
- **Search engines**: they understand which parts of your page are the main content vs. navigation, improving SEO.
- **Other developers**: a future you reading the code can immediately understand the structure.

Semantic HTML is free. There's no downside. Make it a habit from day one.

---

## 2. The Document Structure Tags

These tags describe the **major sections** of a page.

### 2.1. `<header>` — Top of a Section

```html
<header>
  <h1>My Personal Blog</h1>
  <p>A blog about learning to code from scratch.</p>
</header>
```

Use `<header>` for the top of a page or the top of an article. It typically contains the title, a tagline, and maybe a logo or navigation.

You can have multiple `<header>`s on a page — one for the site, one for each article.

### 2.2. `<nav>` — Navigation

```html
<nav>
  <ul>
    <li><a href="index.html">Home</a></li>
    <li><a href="about.html">About</a></li>
    <li><a href="contact.html">Contact</a></li>
  </ul>
</nav>
```

Use `<nav>` for **major navigation blocks**. Not every list of links needs `<nav>` — only the primary navigation (main menu, table of contents, breadcrumbs). Footer link groups can also use `<nav>` if they're significant.

### 2.3. `<main>` — The Main Content

```html
<main>
  <h1>Welcome to my site</h1>
  <p>This is the main content of the page.</p>
</main>
```

Use `<main>` for the **main content of the page** — the unique content that distinguishes this page from other pages on the site. There should be **exactly one** `<main>` per page. It excludes the header, footer, and sidebars.

### 2.4. `<footer>` — Bottom of a Section

```html
<footer>
  <p>© 2026 My Site. All rights reserved.</p>
  <nav>
    <a href="privacy.html">Privacy</a>
    <a href="terms.html">Terms</a>
  </nav>
</footer>
```

Use `<footer>` for the bottom of a page or section. Typically contains copyright, contact info, related links, or "back to top" buttons. Like `<header>`, you can have multiple `<footer>`s — one per article if needed.

### 2.5. `<section>` — A Thematic Grouping

```html
<section>
  <h2>About Us</h2>
  <p>...</p>
</section>

<section>
  <h2>Our Services</h2>
  <p>...</p>
</section>
```

Use `<section>` to group related content with a heading. If you can't think of a heading for the section, you probably want a `<div>` instead. A `<section>` should always start with a heading (`<h2>` through `<h6>`).

### 2.6. `<article>` — Self-Contained Content

```html
<article>
  <h2>How to Make Pancakes</h2>
  <p>...</p>
</article>
```

Use `<article>` for content that could stand alone — a blog post, a news article, a forum comment, a product card. The test: if you took this content out of the page and put it somewhere else, would it still make sense? If yes, use `<article>`.

`<article>` can be nested inside `<article>` — for example, a blog post with reader comments (each comment is an article inside the post article).

### 2.7. `<aside>` — Side Content

```html
<aside>
  <h3>Related Articles</h3>
  <ul>
    <li>...</li>
  </ul>
</aside>
```

Use `<aside>` for content that is related to the main content but not part of it — sidebars, "related posts", advertisements, pull quotes. The key is that the main content should still make sense if you removed the `<aside>`.

### 2.8. `<figure>` and `<figcaption>` — Captioned Content

```html
<figure>
  <img src="chart.png" alt="...">
  <figcaption>Figure 1: Sales growth in Q3.</figcaption>
</figure>
```

Already covered in Lesson 06. Used for any content that needs a caption — images, code blocks, quotes.

---

## 3. Putting It Together: A Semantic Page Structure

Here is a typical blog post page using all the structural semantic tags:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Blog — Learning HTML</title>
  </head>
  <body>
    <header>
      <h1>My Blog</h1>
      <nav>
        <ul>
          <li><a href="#">Home</a></li>
          <li><a href="#">Archive</a></li>
          <li><a href="#">About</a></li>
        </ul>
      </nav>
    </header>

    <main>
      <article>
        <header>
          <h2>Learning HTML: A Beginner's Journey</h2>
          <p>
            By <span>Hieu Louis</span> on
            <time datetime="2026-08-22">August 22, 2026</time>
          </p>
        </header>

        <section>
          <h3>Why HTML?</h3>
          <p>HTML is the foundation of every website...</p>
        </section>

        <section>
          <h3>My First Page</h3>
          <p>I started by writing a simple Hello, World...</p>
          <figure>
            <img src="first-page.png" alt="Screenshot of my first HTML page">
            <figcaption>My very first HTML page, August 2026.</figcaption>
          </figure>
        </section>

        <section>
          <h3>What's Next?</h3>
          <p>Next, I'll learn CSS to make my pages look beautiful...</p>
        </section>

        <footer>
          <p>Tagged: <a href="#">HTML</a>, <a href="#">Beginner</a></p>
        </footer>
      </article>

      <aside>
        <h3>Related Posts</h3>
        <ul>
          <li><a href="#">Why CSS Matters</a></li>
          <li><a href="#">JavaScript Basics</a></li>
        </ul>
      </aside>
    </main>

    <footer>
      <p>© 2026 My Blog. Follow me on Twitter.</p>
    </footer>
  </body>
</html>
```

Notice how readable this is. Even without rendering, you can see the page structure clearly. That is the power of semantic HTML.

---

## 4. Content Semantic Tags

Beyond structural tags, HTML5 added several tags for specific types of content.

### 4.1. `<time>` — Dates and Times

```html
<p>
  Published on <time datetime="2026-08-22">August 22, 2026</time>.
</p>
```

The `datetime` attribute is machine-readable (ISO 8601 format). Search engines, calendars, and other tools can read it. The text inside is human-readable.

Other examples:

```html
<time datetime="2026-08-22T13:45">August 22, 2026 at 1:45 PM</time>
<time datetime="2026-08">August 2026</time>
<time datetime="13:45">1:45 PM</time>
```

### 4.2. `<mark>` — Highlighted Text

```html
<p>The most important word is <mark>semantic</mark>.</p>
```

Renders with a yellow background like a highlighter.

### 4.3. `<details>` and `<summary>` — Collapsible Content

```html
<details>
  <summary>Click to reveal the answer</summary>
  <p>The answer is 42.</p>
</details>
```

The user can click the summary to expand or collapse the content. No JavaScript required. Add `open` to start expanded:

```html
<details open>
  <summary>Frequently Asked Questions</summary>
  <p>...</p>
</details>
```

### 4.4. `<progress>` — Progress Bar

```html
<label for="file">Download progress:</label>
<progress id="file" value="70" max="100">70%</progress>
```

Shows a native progress bar. Pair with JavaScript to update the `value` dynamically.

### 4.5. `<meter>` — A Scalar Measurement

```html
<label for="disk">Disk usage:</label>
<meter id="disk" value="0.6">60%</meter>
```

Like `<progress>` but for static values, like disk space or ratings.

### 4.6. `<address>` — Contact Information

```html
<address>
  Written by <a href="mailto:hieu@example.com">Hieu Louis</a>.<br>
  Visit us at:<br>
  123 Main Street<br>
  Springfield, USA
</address>
```

Use `<address>` for contact info about the page or article author. It renders as italic by default. Don't use it for arbitrary addresses (use a `<p>` with `<br>` for that).

### 4.7. `<blockquote>` — Block Quote (Recap)

```html
<blockquote cite="https://example.com">
  <p>To be or not to be...</p>
  <footer>— <cite>Shakespeare</cite></footer>
</blockquote>
```

### 4.8. `<abbr>` — Abbreviation (Recap)

```html
<abbr title="HyperText Markup Language">HTML</abbr>
```

### 4.9. `<cite>` — Citation (Recap)

```html
<p>According to <cite>MDN Web Docs</cite>...</p>
```

### 4.10. `<q>` — Inline Quote (Recap)

```html
<p>She said, <q>Hello there!</q></p>
```

### 4.11. `<code>` — Inline Code (Recap)

```html
<p>Use <code>console.log()</code> to debug.</p>
```

For multi-line code blocks, use `<pre><code>`:

```html
<pre><code>
function greet() {
  console.log("Hello!");
}
</code></pre>
```

The `<pre>` tag preserves whitespace and uses a monospace font. Without `<pre>`, line breaks and indentation would collapse.

---

## 5. A Note on Old Tags to Avoid

Some HTML tags exist for historical reasons but are obsolete. Don't use them:

- `<center>` — center content. Use CSS.
- `<font>` — font styling. Use CSS.
- `<marquee>` — scrolling text. Use CSS animations.
- `<blink>` — blinking text. Just don't.
- `<frame>`, `<frameset>` — old layout system. Dead. Use CSS.

These are deprecated and should never appear in modern code.

---

## 6. A Complete Semantic Page

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Portfolio</title>
  </head>
  <body>
    <header>
      <h1>Hieu Louis — Web Developer</h1>
      <nav>
        <a href="#about">About</a>
        <a href="#projects">Projects</a>
        <a href="#contact">Contact</a>
      </nav>
    </header>

    <main>
      <section id="about">
        <h2>About Me</h2>
        <p>
          Hi! I'm <strong>Hieu</strong>, a self-taught web developer
          based in Vietnam since <time datetime="2026">2026</time>.
          I love building clean, accessible websites.
        </p>

        <details>
          <summary>My Background</summary>
          <p>
            I started learning HTML in August 2026. Before that,
            I had never written a single line of code.
          </p>
        </details>
      </section>

      <section id="projects">
        <h2>Projects</h2>

        <article>
          <h3>Personal Blog</h3>
          <p>A blog about my coding journey, built with HTML.</p>
          <figure>
            <img src="blog.png" alt="Screenshot of the blog homepage">
            <figcaption>The blog homepage, August 2026.</figcaption>
          </figure>
        </article>

        <article>
          <h3>Portfolio Site</h3>
          <p>This very website! Built from scratch.</p>
        </article>
      </section>

      <section id="contact">
        <h2>Get in Touch</h2>
        <address>
          Email: <a href="mailto:hieu@example.com">hieu@example.com</a><br>
          Phone: <a href="tel:+841234567890">+84 123 456 7890</a>
        </address>
      </section>
    </main>

    <aside>
      <h3>Skills</h3>
      <ul>
        <li>HTML <meter value="0.5">50%</meter></li>
        <li>CSS <meter value="0.1">10%</meter></li>
        <li>JavaScript <meter value="0">0%</meter></li>
      </ul>
    </aside>

    <footer>
      <p>© 2026 Hieu Louis. Built with semantic HTML.</p>
    </footer>
  </body>
</html>
```

Read this top to bottom. Notice how every tag describes what it is. There's no `<div>` anywhere — every piece has a semantic home. This is the kind of HTML real developers aspire to write.

---

## Try It Yourself

### Exercise 1: Convert Div Soup to Semantic

Take this "div soup" code and rewrite it with semantic tags:

```html
<div class="page">
  <div class="top">
    <div class="logo">My Site</div>
    <div class="menu">
      <a href="#">Home</a>
      <a href="#">About</a>
    </div>
  </div>
  <div class="content">
    <div class="post">
      <div class="post-title">Hello</div>
      <p>...</p>
    </div>
    <div class="sidebar">
      <div class="related">...</div>
    </div>
  </div>
  <div class="bottom">
    <p>© 2026</p>
  </div>
</div>
```

Use: `<body>`, `<header>`, `<nav>`, `<main>`, `<article>`, `<aside>`, `<footer>`.

### Exercise 2: Build a Resume Page

Build a one-page resume using semantic tags. Sections:

- `<header>` with your name and contact info.
- `<main>` containing `<section>`s for Summary, Experience, Education, Skills.
- Each job in `<article>` with a `<header>` (job title, company, dates using `<time>`).
- `<aside>` with skills (use `<meter>`).
- `<footer>` with references.

### Exercise 3: FAQ Accordion

Build a small FAQ page using only `<details>` and `<summary>` (no JavaScript needed!). At least 5 questions.

---

## Common Mistakes

### Mistake 1: Using `<article>` for every section

`<article>` is for self-contained content. If removing the content would leave a gap in the page narrative, it's a `<section>`, not an `<article>`.

### Mistake 2: Wrapping everything in `<section>` instead of using `<article>`/`<aside>`

Pick the right tag. Don't use `<section>` as a generic fallback — `<div>` is the right fallback when no semantic tag fits.

### Mistake 3: Forgetting `<main>` per page

Every page should have exactly one `<main>`. Don't forget it.

### Mistake 4: Multiple `<h1>`s

Use one `<h1>` per page (typically inside `<header>`).

### Mistake 5: Misusing `<address>`

`<address>` is for contact info about the document/page author. Don't use it for arbitrary addresses — those are just paragraphs.

### Mistake 6: Forgetting `<time datetime>`

When you write a date, wrap it in `<time datetime="YYYY-MM-DD">`. The visible text can be anything; the `datetime` attribute is the machine-readable version.

---

## Summary

- **Semantic HTML** uses tags that describe the meaning of content, not just its appearance.
- Structural semantic tags: `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<aside>`, `<footer>`, `<figure>`, `<figcaption>`.
- Content semantic tags: `<time>`, `<mark>`, `<details>`, `<summary>`, `<progress>`, `<meter>`, `<address>`, `<blockquote>`, `<abbr>`, `<cite>`, `<q>`, `<code>`, `<pre>`.
- Always prefer semantic tags over `<div>`/`<span>` when they fit.
- Use exactly one `<main>` and one `<h1>` per page.
- Avoid deprecated tags: `<center>`, `<font>`, `<marquee>`, `<blink>`, `<frame>`.

In the next lesson, we'll learn how to write comments — invisible notes that help you and other developers understand your code.

---

**Next:** [Lesson 12: Comments →](./12-comments.md)
