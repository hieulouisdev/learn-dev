# Lesson 12: Comments

> Module: HTML · Lesson 12 of 13
> Estimated time: 15–20 minutes

Comments are notes you write inside your HTML that the browser completely ignores. They are useful for leaving reminders for yourself, marking sections, explaining tricky code, or temporarily disabling a piece of code. In this short lesson, you will learn everything you need to know about HTML comments.

---

## Learning Objectives

After this lesson, you will be able to:

1. Write HTML comments using the `<!-- -->` syntax.
2. Use comments to mark sections of a long page.
3. Use comments to leave reminders for yourself or other developers.
4. Comment out code temporarily during debugging.
5. Know when **not** to use comments.

---

## 1. Comment Syntax

An HTML comment looks like this:

```html
<!-- This is a comment. The browser ignores it. -->
```

Everything between `<!--` (open) and `-->` (close) is invisible to the user but visible to anyone reading the source code. The browser does not render it.

### Multi-line Comments

Comments can span multiple lines:

```html
<!--
  This is a multi-line comment.
  You can write notes spanning several lines.
  Useful for explaining complex sections.
-->
```

### Comments Inside Other Elements

You can place comments anywhere in an HTML document:

```html
<div>
  <!-- This content was intentionally left blank -->
</div>
```

---

## 2. Common Uses

### 2.1. Marking Sections

In long HTML files, comments help you find sections quickly:

```html
<!-- ============================ -->
<!-- HEADER                       -->
<!-- ============================ -->
<header>...</header>

<!-- ============================ -->
<!-- NAVIGATION                   -->
<!-- ============================ -->
<nav>...</nav>

<!-- ============================ -->
<!-- MAIN CONTENT                 -->
<!-- ============================ -->
<main>...</main>
```

The `=====` bars are just decoration — they make the comments visually stand out when scanning the file.

### 2.2. Explanations

Explain tricky or non-obvious code:

```html
<!-- The data-id attribute is read by main.js to identify this row -->
<tr data-id="42">
  <td>Alice</td>
</tr>
```

Don't explain obvious code. Only comment the "why" — the "what" should be clear from the code itself.

### 2.3. TODOs

Leave reminders for yourself:

```html
<!-- TODO: Add proper alt text -->
<img src="hero.jpg" alt="">

<!-- FIXME: This layout breaks on mobile -->
<div class="hero">...</div>
```

Many code editors highlight `TODO:` and `FIXME:` specially, and tools can collect them into a list.

### 2.4. Authoring Notes

```html
<!--
  Author: Hieu Louis
  Created: 2026-08-22
  Last modified: 2026-08-23
-->
```

Useful at the top of large files for tracking ownership and history. (Better long-term: use Git commits, but comments are still useful in practice.)

### 2.5. Commenting Out Code

Temporarily disable code during debugging:

```html
<!-- <p>This paragraph is temporarily disabled.</p> -->
```

The browser treats this as a comment and doesn't render the paragraph. Useful when you want to test how the page looks without something, but you don't want to delete the code.

---

## 3. Conditional Comments (IE Only — Historical)

In the bad old days of Internet Explorer, developers used a special syntax called **conditional comments** to target IE versions:

```html
<!--[if lt IE 9]>
  <script src="html5shiv.js"></script>
<![endif]-->
```

These are obsolete. Modern browsers (Chrome, Firefox, Safari, Edge) treat them as regular comments and ignore them. Don't use them in new code.

---

## 4. When NOT to Comment

Comments are tools, not obligations. Bad comments are worse than no comments.

### Don't State the Obvious

```html
<!-- BAD -->
<!-- This is a paragraph -->
<p>Hello</p>

<!-- This is a heading -->
<h1>Title</h1>
```

If the code is self-explanatory, don't comment. This is noise.

### Don't Leave Old Code as Comments

```html
<!-- BAD: leftover old code -->
<!-- <p>Old version</p> -->
<p>New version</p>
```

Old code belongs in version control (Git), not in your HTML. Delete it; you can always get it back from Git history.

### Don't Write Essays

Comments should be brief. If you need to write a paragraph to explain something, the code is probably too complex. Refactor it.

---

## 5. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Comments Demo</title>
    <!--
      Author: Hieu Louis
      Created: 2026-08-22
      Purpose: Demonstrate HTML comments
    -->
  </head>
  <body>
    <!-- ========== HEADER ========== -->
    <header>
      <h1>My Page</h1>
    </header>

    <!-- ========== MAIN ========== -->
    <main>
      <!-- TODO: Replace with real content -->
      <p>Coming soon.</p>

      <!-- <p>This paragraph was disabled for testing</p> -->
    </main>

    <!-- ========== FOOTER ========== -->
    <footer>
      <p>© 2026</p>
    </footer>
  </body>
</html>
```

Save and open in a browser — you won't see any comments. Then view source (`Ctrl+U` in most browsers) to see them.

---

## Try It Yourself

### Exercise 1: Annotate Your Page

Take any HTML page you've built so far. Add at least five useful comments — section markers, TODOs, or explanations of non-obvious code.

### Exercise 2: Comment Out Code

In a page with multiple paragraphs, comment out two of them. Refresh the browser. They should disappear. Uncomment them. They should reappear.

### Exercise 3: Find Bad Comments

Look at this code. Which comment is bad?

```html
<!-- This is the body tag -->
<body>
  <!-- TODO: Fix the broken link -->
  <a href="page.html">Page</a>
  <!-- <p>Disabled content</p> -->
</body>
```

Answer: The first comment ("This is the body tag") is bad — it's obvious and adds no value. The other two are useful (a TODO and a temporarily disabled element).

---

## Common Mistakes

### Mistake 1: Forgetting the closing `-->`

```html
<!-- This comment never ends...
<p>And this paragraph is also "commented out" by accident</p>
```

If you forget `-->`, everything after is treated as a comment. Always close every comment.

### Mistake 2: Nesting comments

```html
<!-- outer <!-- inner --> still outer -->
```

HTML comments don't nest. The first `-->` ends the comment, even if it was meant to be inner. Avoid nesting.

### Mistake 3: Commenting inside tag attributes

```html
<!-- BAD: cannot comment inside a tag -->
<a <!-- href="#">Link</a>
```

Comments can only go between tags, not inside tag definitions.

### Mistake 4: Over-commenting

```html
<!-- BAD: noise -->
<!-- Title -->
<h1>Title</h1>
<!-- Paragraph -->
<p>Text</p>
<!-- Another paragraph -->
<p>More text</p>
```

Don't comment every line. Only comment where the meaning isn't obvious.

---

## Summary

- HTML comments are written between `<!--` and `-->`.
- The browser ignores them; they're only for humans reading the source.
- Use comments for: section markers, explanations, TODOs, temporarily disabled code.
- Don't use comments for: obvious things, leftover old code, essays.
- Comments cannot be nested.
- The `TODO:` and `FIXME:` prefixes are conventions that many editors highlight.

You've reached the second-to-last lesson of the HTML module! In the next (and final) lesson, you'll get a printable cheatsheet of every HTML tag you've learned.

---

**Next:** [Lesson 13: HTML Cheatsheet →](./13-html-cheatsheet.md)
