# Lesson 03: Headings and Paragraphs

> Module: HTML · Lesson 3 of 13
> Estimated time: 30–45 minutes

Headings and paragraphs are the bread and butter of any web page. Almost everything you write in HTML begins with these two tags. In this lesson, we will go deep into how they work, when to use each one, and how not to misuse them.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use all six heading levels (`<h1>` through `<h6>`) correctly.
2. Write paragraphs with the `<p>` tag.
3. Understand the difference between **semantic** and **visual** hierarchy.
4. Use line breaks (`<br>`) and horizontal rules (`<hr>`) appropriately.
5. Avoid the most common mistakes beginners make with headings.

---

## 1. Headings: `<h1>` to `<h6>`

HTML provides six levels of headings, from `<h1>` (most important) to `<h6>` (least important). Here they all are:

```html
<h1>Level 1 Heading — the page's main title</h1>
<h2>Level 2 Heading — a major section</h2>
<h3>Level 3 Heading — a subsection</h3>
<h4>Level 4 Heading — a sub-subsection</h4>
<h5>Level 5 Heading — a minor subsection</h5>
<h6>Level 6 Heading — the smallest heading</h6>
```

When rendered, the browser shows each level smaller than the one before:

- `<h1>` is the largest and boldest.
- `<h2>` is slightly smaller.
- `<h3>` smaller still.
- And so on, down to `<h6>`, which is the smallest.

### How to Use Heading Levels

Think of headings like a table of contents in a book. The book has a title (that is your `<h1>`). Each chapter is an `<h2>`. Each section inside a chapter is an `<h3>`. Subsections inside sections are `<h4>` and so on.

```html
<h1>My Personal Blog</h1>

  <h2>Travel Stories</h2>
    <h3>Japan, 2025</h3>
      <h4>Tokyo</h4>
      <h4>Kyoto</h4>
    <h3>Vietnam, 2025</h3>
      <h4>Hanoi</h4>
      <h4>Saigon</h4>

  <h2>Coding Notes</h2>
    <h3>HTML Basics</h3>
    <h3>CSS Basics</h3>
```

Notice the structure: an `<h3>` only appears inside an `<h2>`. An `<h4>` only appears inside an `<h3>`. **Headings describe a hierarchy**, not just a size. This is the most important rule of this lesson.

### The Rule: One `<h1>` Per Page

As a rule of thumb, every page should have **exactly one** `<h1>`. It is the page's main title. Multiple `<h1>` tags confuse search engines and screen readers.

### Do Not Skip Levels

Bad:

```html
<!-- WRONG: skipping from h2 to h4 -->
<h2>My Section</h2>
<h4>Subsection</h4>
```

Good:

```html
<!-- GOOD: levels go in order -->
<h2>My Section</h2>
<h3>Subsection</h3>
```

Skipping levels breaks the hierarchy. Even if you want a smaller visual size, do not skip the heading level — instead, use CSS to change the size of an `<h3>` to look like an `<h4>`. We will learn that in the CSS module.

### Do Not Use Headings for Styling

This is the most common mistake beginners make. They want big text, so they wrap it in an `<h1>`:

```html
<!-- WRONG: using h1 just to make text big -->
<h1>Buy now for $19.99!</h1>
```

Headings are not for making text big. Headings are for describing the **importance** of a section. If you want big text without it being a heading, use CSS on a regular `<p>` or `<span>`. We will see this in the CSS module.

---

## 2. Paragraphs: `<p>`

A paragraph is a block of text separated from other text by blank lines. In HTML:

```html
<p>This is the first paragraph. The browser will add some space above and below it automatically.</p>

<p>This is the second paragraph. The space between this and the previous paragraph is the browser's default paragraph spacing.</p>
```

The browser automatically adds vertical spacing before and after each paragraph. You do not need to add `<br>` tags between paragraphs — just use separate `<p>` tags.

### Multi-line Paragraphs

You can write a paragraph across multiple lines in your source code, and the browser will treat all the white space as a single space:

```html
<p>
  This paragraph
  is written
  across many
  lines, but the
  browser renders
  it as one line
  that wraps naturally.
</p>
```

White space inside HTML is collapsed by the browser. If you want a line break **inside** a paragraph (without starting a new paragraph), use the `<br>` tag.

---

## 3. Line Breaks: `<br>`

The `<br>` tag forces a line break. It is a **void element** — it has no closing tag.

```html
<p>
  Roses are red,<br>
  Violets are blue,<br>
  HTML is fun,<br>
  And so are you.
</p>
```

Without the `<br>` tags, all four lines would render on a single line, wrapped by the browser. With `<br>`, each line ends where you put the tag.

### When NOT to Use `<br>`

Beginners often abuse `<br>` to create vertical spacing:

```html
<!-- WRONG: using <br> for spacing -->
<p>Hello</p>
<br><br><br>
<p>World</p>
```

If you want vertical space between elements, use CSS `margin`. `<br>` should only be used when the line break is **part of the content** — like in a poem or a postal address.

```html
<!-- GOOD: <br> for an address -->
<p>
  John Smith<br>
  123 Main Street<br>
  Springfield, IL 62701
</p>
```

---

## 4. Horizontal Rules: `<hr>`

The `<hr>` tag creates a horizontal line across the page. Like `<br>`, it is a void element.

```html
<h2>Chapter 1</h2>
<p>It was the best of times...</p>

<hr>

<h2>Chapter 2</h2>
<p>It was the worst of times...</p>
```

Semantically, `<hr>` represents a **thematic break** — a change in topic. Do not use it just to draw a line for decoration. If you want a decorative line, use CSS `border-bottom` on a `<div>`.

---

## 5. A Complete Example

Let's put everything together. Here is a short article using headings, paragraphs, line breaks, and a horizontal rule:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My First Article</title>
  </head>
  <body>
    <h1>My First Article</h1>
    <p>By Hieu Louis · Published on August 22, 2026</p>

    <hr>

    <h2>Introduction</h2>
    <p>
      Welcome to my very first article on the web. I am writing this article
      to practice everything I have learned so far about HTML. So far, I can
      write headings, paragraphs, line breaks, and horizontal rules. That is
      already enough to write a simple blog post.
    </p>

    <h2>Why I Am Learning HTML</h2>
    <p>
      I want to build websites. Not just any websites — beautiful, fast,
      accessible websites that anyone in the world can visit. HTML is the
      first step on that journey.
    </p>

    <h3>My Goals</h3>
    <p>
      By the end of this year, I want to:<br>
      1. Build a personal portfolio website.<br>
      2. Learn CSS well enough to style any layout.<br>
      3. Start learning JavaScript.
    </p>

    <hr>

    <p>Thank you for reading.</p>
  </body>
</html>
```

Save this as `article.html` and open it in your browser. You will see a properly structured article with clear hierarchy.

---

## Try It Yourself

### Exercise 1: Write a Short Autobiography

Create a file called `about-me.html` with the following structure:

- One `<h1>` with your name.
- An `<h2>` called "Where I'm From" with a paragraph.
- An `<h2>` called "What I Love" with a paragraph.
- An `<h3>` called "Top 3 Hobbies" with a paragraph (use `<br>` to separate them).
- An `<h2>` called "My Goals" with a paragraph.
- End with an `<hr>` and a closing paragraph.

### Exercise 2: Headings Hierarchy Practice

Take this list of headings and rewrite them with correct nesting (no skipping):

```
My Resume
Work Experience
Job 1: Junior Developer
Job 2: Senior Developer
Education
University
High School
Skills
Programming
Soft Skills
```

Use `<h1>` for "My Resume", `<h2>` for major sections, `<h3>` for items inside major sections.

### Exercise 3: Misuse Detection

Look at this code. What is wrong?

```html
<h1>My Page</h1>
<h4>About Me</h4>
<p>I am a developer.</p>
<h1>Contact Me</h1>
<p>email@example.com</p>
```

Answer: There are two `<h1>` tags, and the hierarchy skips from `<h1>` directly to `<h4>`. The fix: use one `<h1>` and turn "About Me" and "Contact Me" into `<h2>`.

---

## Common Mistakes

### Mistake 1: Multiple `<h1>` tags

Use only one `<h1>` per page. The page's main title is the only thing that deserves the top-level heading.

### Mistake 2: Skipping levels

```html
<h2>Section</h2>
<h4>Subsection</h4>  <!-- skipped h3 -->
```

Don't skip. Go in order: `<h2>` → `<h3>` → `<h4>`.

### Mistake 3: Using `<br>` for spacing

Use CSS for spacing. `<br>` is only for line breaks inside content where the break itself is meaningful (poems, addresses).

### Mistake 4: Using headings to make text big

Want big text? Use CSS. Headings describe structure, not appearance.

### Mistake 5: Forgetting to close `<p>` tags

Browsers will often "auto-close" paragraphs when they hit another block element, but this is fragile. Always close every `<p>` you open.

---

## Summary

- HTML provides six heading levels: `<h1>` (most important) to `<h6>` (least important).
- Use exactly one `<h1>` per page.
- Do not skip heading levels.
- Do not use headings just to make text big — use CSS for that.
- Paragraphs use `<p>`; the browser adds default spacing around them.
- Use `<br>` for line breaks **inside** a paragraph (like in an address or poem).
- Use `<hr>` for thematic breaks between sections.

In the next lesson, we will learn how to format text inside paragraphs: bold, italic, and more.

---

**Next:** [Lesson 04: Text Formatting →](./04-text-formatting.md)
