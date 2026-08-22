# Lesson 04: Text Formatting

> Module: HTML · Lesson 4 of 13
> Estimated time: 30–45 minutes

Now that you know how to write headings and paragraphs, it is time to format the text inside them. In this lesson, you will learn how to make text bold, italic, underlined, marked as important, and more. You will also learn the crucial difference between **semantic** and **presentational** tags — a distinction that separates beginners from real developers.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use `<strong>`, `<em>`, `<mark>`, `<del>`, `<ins>`, `<sub>`, and `<sup>` correctly.
2. Distinguish semantic tags from presentational ones.
3. Understand why `<b>` and `<i>` still exist alongside `<strong>` and `<em>`.
4. Apply multiple formatting tags to the same text.
5. Format text inside headings, paragraphs, and lists.

---

## 1. Semantic vs. Presentational Tags

This is the single most important concept in this lesson. Read it twice.

There are two ways to make text bold in HTML:

- `<b>Bold text</b>` — presentational. Says "make this bold." Doesn't say why.
- `<strong>Bold text</strong>` — semantic. Says "this text is important." Browsers render it as bold by default.

Both look the same in the browser. So why does it matter?

It matters because of **screen readers** and **search engines**. A screen reader reading a page to a blind user will emphasize the word wrapped in `<strong>` (saying it louder, slower, or with a different tone). It will not emphasize `<b>`. Search engines give more weight to text inside `<strong>` than to `<b>`. So if you want to say "this word is important," use `<strong>`. If you want to say "this word is bold but not particularly important," use `<b>`.

The same logic applies to italics:

- `<i>Italic text</i>` — presentational.
- `<em>Italic text</em>` — semantic ("emphasized"). Browsers render it as italic.

**Rule of thumb:** Always prefer the semantic version. Only use `<b>` and `<i>` when there is no better semantic alternative.

---

## 2. The Semantic Formatting Tags

Let's go through each one with examples.

### 2.1. `<strong>` — Important Text

```html
<p><strong>Warning:</strong> Do not refresh the page while saving.</p>
```

The word "Warning:" will render in bold. Use `<strong>` for warnings, important notes, or any text you want the reader to pay extra attention to.

### 2.2. `<em>` — Emphasized Text

```html
<p>I <em>really</em> need you to read this.</p>
```

The word "really" will render in italic. Use `<em>` when the emphasis changes the meaning of the sentence. Compare:

- "I really need you to read this." (emphasis on the urgency)
- "I really need you to read this." (without emphasis, just a normal sentence)

### 2.3. `<mark>` — Highlighted Text

```html
<p>HTML is the <mark>skeleton</mark> of every web page.</p>
```

The word "skeleton" will have a yellow highlight, like a marker pen. Use `<mark>` to draw attention to a specific word or phrase in a block of text — like search results that highlight the search term.

### 2.4. `<del>` — Deleted Text

```html
<p>The price is <del>$99</del> $49 this week only.</p>
```

The original price `$99` will render with a strikethrough line through it. Use `<del>` for prices that have changed, or to show edits in a document.

### 2.5. `<ins>` — Inserted Text

```html
<p>The new price is <del>$99</del> <ins>$49</ins> this week only.</p>
```

The new price `$49` will render with an underline. Often used together with `<del>` to show edits — what was removed and what was added.

### 2.6. `<sub>` — Subscript

```html
<p>The chemical formula for water is H<sub>2</sub>O.</p>
```

Renders as: H₂O. The `2` drops below the baseline. Use `<sub>` for chemical formulas, footnotes, or mathematical subscripts.

### 2.7. `<sup>` — Superscript

```html
<p>The area of a circle is πr<sup>2</sup>.</p>
```

Renders as: πr². The `2` rises above the baseline. Use `<sup>` for exponents, ordinal numbers (1st, 2nd, 3rd), or footnote markers.

### 2.8. `<small>` — Side Comments

```html
<p>Free shipping on all orders. <small>Some restrictions apply.</small></p>
```

Renders the small text in a smaller size. Use `<small>` for disclaimers, copyright, or side comments — text that should be less prominent than the main text.

### 2.9. `<abbr>` — Abbreviations

```html
<p>My favorite browser is <abbr title="Mozilla Firefox">Firefox</abbr>.</p>
```

When the user hovers over "Firefox", the title "Mozilla Firefox" appears in a tooltip. Use `<abbr>` for acronyms and abbreviations, with the full meaning in the `title` attribute.

### 2.10. `<code>` — Inline Code

```html
<p>Use the <code>&lt;p&gt;</code> tag to create a paragraph.</p>
```

Renders the text in a monospace font (like Courier). Use `<code>` to show a snippet of code inside a sentence. Note that we had to write `&lt;` and `&gt;` to display actual angle brackets — we will cover HTML entities in a later section.

### 2.11. `<kbd>` — Keyboard Input

```html
<p>Press <kbd>Ctrl</kbd> + <kbd>S</kbd> to save your file.</p>
```

Renders the keys in a stylized way that looks like keyboard keys. Use `<kbd>` when instructing users to press keys.

### 2.12. `<blockquote>` — Block Quotation

```html
<blockquote cite="https://example.com">
  <p>The only way to do great work is to love what you do.</p>
  <footer>— Steve Jobs</footer>
</blockquote>
```

For longer quotes that should be displayed as their own block. Browsers usually indent blockquotes. The `cite` attribute holds the URL of the source.

### 2.13. `<q>` — Inline Quotation

```html
<p>My teacher said, <q>You will be a great developer one day.</q></p>
```

For short inline quotes. Browsers automatically add quotation marks around the content.

### 2.14. `<cite>` — Citation

```html
<p>According to <cite>MDN Web Docs</cite>, HTML5 is the latest standard.</p>
```

For citing the title of a work — a book, article, movie, or website. Renders as italic in most browsers.

---

## 3. The Presentational Tags (Use Sparingly)

These exist for legacy reasons and should be used rarely.

### 3.1. `<b>` — Bold

```html
<p>Keywords: <b>HTML</b>, <b>CSS</b>, <b>JavaScript</b>.</p>
```

Bold, but not "important." Use for product names in summaries, keywords, or other text that should stand out visually without semantic importance.

### 3.2. `<i>` — Italic

```html
<p>The <i>Star Wars</i> movies are classics.</p>
```

Italic, but not "emphasized." Use for the title of a work (book, movie, ship name), foreign words, or technical terms.

### 3.3. `<u>` — Underline

```html
<p>This word is <u>underlined</u>.</p>
```

Avoid using `<u>` for emphasis — underlined text looks like a link, which confuses users. Use only for unarticulated annotations (rare) or spelling errors (like in a text editor's spellcheck).

### 3.4. `<s>` — Strikethrough

```html
<p><s>Old version</s> New version</p>
```

Strikethrough for content no longer accurate. Prefer `<del>` if the deletion is part of an edit.

---

## 4. Nesting Multiple Tags

You can apply multiple tags to the same text. Just nest them properly:

```html
<p><strong><em>Read this carefully!</em></strong></p>
```

Renders as bold italic. The order of opening and closing should be symmetric (last opened, first closed) — like nested parentheses.

### Wrong

```html
<!-- WRONG: tags overlap -->
<p><strong><em>Read this!</strong></em></p>
```

Browsers may render this "correctly" by accident, but it is invalid HTML. Always close the inner tag before the outer tag.

---

## 5. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Text Formatting Example</title>
  </head>
  <body>
    <h1>Text Formatting <em>Demo</em></h1>

    <p>
      Welcome to the <strong>text formatting</strong> demo. This page shows
      every common formatting tag in action. Read <em>carefully</em> — these
      tags appear <em>everywhere</em> on the web.
    </p>

    <h2>Quotes and Citations</h2>
    <blockquote cite="https://www.w3.org">
      <p>The web is the most transformative invention of our time.</p>
      <footer>— Tim Berners-Lee, <cite>W3C</cite></footer>
    </blockquote>

    <h2>Science and Math</h2>
    <p>
      Water is H<sub>2</sub>O. The area of a circle is πr<sup>2</sup>.
      The speed of light is approximately 3 × 10<sup>8</sup> m/s.
    </p>

    <h2>Edits and Updates</h2>
    <p>
      The price is <del>$99</del> <ins>$49</ins> this week only.
      <small>Offer ends Sunday.</small>
    </p>

    <h2>Code and Keyboard</h2>
    <p>
      To save your work, press <kbd>Ctrl</kbd> + <kbd>S</kbd>.
      The <code>&lt;p&gt;</code> tag creates a paragraph.
    </p>

    <h2>Abbreviations</h2>
    <p>
      My favorite organization is the
      <abbr title="World Wide Web Consortium">W3C</abbr>.
      Hover your mouse over "W3C" to see the full name.
    </p>
  </body>
</html>
```

Save this as `formatting.html` and open it in your browser. You will see every formatting tag in action. Hover over the abbreviation to see the tooltip.

---

## Try It Yourself

### Exercise 1: Format a Recipe

Write an HTML page that displays a simple recipe. Use:

- `<h1>` for the recipe name.
- `<p>` with `<em>` for an introductory note.
- An unordered list (we will learn lists in Lesson 07, but try to figure out `<ul>` and `<li>` on your own — you can do it!).
- `<strong>` for emphasizing an important step like "Stir constantly."
- `<mark>` to highlight a critical instruction.

### Exercise 2: Format a Quote

Find a famous quote you like. Write it inside a `<blockquote>`, attribute it to the author using `<cite>`, and add a `<small>` link to the source.

### Exercise 3: Science Page

Create a small page about a chemical element. Use `<sub>` for the atomic number and `<sup>` for the mass number. Example: Carbon-14 is written as <sup>14</sup>C.

---

## Common Mistakes

### Mistake 1: Using `<b>` when you mean `<strong>`

If you want to emphasize importance, use `<strong>`. Only use `<b>` when bold is purely visual.

### Mistake 2: Overlapping tags

```html
<!-- WRONG -->
<strong><em>text</strong></em>
```

Always close inner tags before outer tags.

### Mistake 3: Underlining non-link text

Users assume underlined text is a link. Use `<u>` only when nothing else fits.

### Mistake 4: Using `<mark>` for general highlighting

`<mark>` semantically means "this text is relevant to the user's current task" (like a search match). For decorative highlights, use CSS `background-color`.

### Mistake 5: Writing raw `<` or `>` in code examples

If you type `<p>` literally in your HTML, the browser will interpret it as a tag. To display angle brackets as text, write `&lt;` (less than) and `&gt;` (greater than).

---

## Summary

- HTML formatting tags fall into two groups: **semantic** (`<strong>`, `<em>`, `<mark>`, `<del>`, `<ins>`, `<sub>`, `<sup>`) and **presentational** (`<b>`, `<i>`, `<u>`, `<s>`).
- Always prefer semantic tags — they convey meaning that screen readers and search engines understand.
- Use `<strong>` for important text, `<em>` for emphasized text, `<mark>` for highlighted text.
- Use `<sub>` for subscripts (H₂O) and `<sup>` for superscripts (πr²).
- Use `<blockquote>` for long quotes, `<q>` for short inline quotes, `<cite>` for citations.
- Use `<code>` for inline code, `<kbd>` for keyboard keys, `<abbr>` for abbreviations.
- Tags can be nested, but must be closed in reverse order (last opened, first closed).

In the next lesson, we will learn one of the most powerful features of the web: links.

---

**Next:** [Lesson 05: Links →](./05-links.md)
