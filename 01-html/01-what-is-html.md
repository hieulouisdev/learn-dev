# Lesson 01: What is HTML?

> Module: HTML · Lesson 1 of 13
> Estimated time: 30–45 minutes

Welcome to your very first lesson. Today, you will write your first line of HTML, see it appear in a web browser, and understand exactly what HTML is and how it works. By the end of this lesson, you will have created your very first web page.

You do not need to know anything about programming. We are starting from zero. Take a deep breath, and let's begin.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain, in plain English, what HTML is and what it does.
2. Identify the three parts of an HTML tag: the opening tag, the content, and the closing tag.
3. Write a complete, valid HTML file and open it in a browser.
4. Understand why HTML uses tags at all (the alternative would be chaos).

---

## 1. What is HTML, Really?

Imagine you are writing a document in Microsoft Word. When you make a word bold, Word stores that information invisibly behind the word. The browser (or Word) reads those invisible notes and renders the word as bold.

HTML is the same idea, but **the notes are not invisible**. They are written right there in the text, surrounded by angle brackets `<like this>`. These notes are called **tags**.

For example, if you want to write a paragraph in HTML, you write:

```html
<p>This is a paragraph of text.</p>
```

The `<p>` at the start tells the browser "everything after this, until you see a matching closing tag, is a paragraph." The `</p>` at the end is the closing tag — it tells the browser "the paragraph ends here."

That's it. HTML is just text with little angle-bracket notes around it telling the browser what each piece of text is.

### A Real-World Analogy

Think of HTML as a label-maker. You have a big pile of stuff on your desk: a sticky note, a poster, a business card, a book. You want to organize them. So you write labels and stick them on each one:

- "Sticky note" goes on the sticky note.
- "Poster" goes on the poster.
- "Business card" goes on the card.
- "Book" goes on the book.

Now, anyone looking at the labels instantly understands what each item is. HTML does the exact same thing — except instead of physical labels, it uses `<p>` for paragraphs, `<h1>` for the biggest headings, `<a>` for links, and so on.

---

## 2. The Three Parts of a Tag

Every HTML element has three parts. Memorize these names because we will use them in every lesson:

1. **Opening tag** — `<p>` — Tells the browser "the element starts here."
2. **Content** — `This is a paragraph of text.` — The actual stuff inside the element (text, an image, more elements).
3. **Closing tag** — `</p>` — Tells the browser "the element ends here." The slash `/` is what makes it a closing tag.

Put together, the opening tag + content + closing tag is called an **element**.

```text
   <p>This is a paragraph.</p>
   ↑ ↑              ↑     ↑
   │ │              │     │
   │ └ opening tag  │     └ closing tag (note the slash)
   │                │
   │                └ the content
   │
   └ the < symbol that opens every tag
```

### Self-Closing Tags

A few HTML elements do not have any content, so they do not need a separate closing tag. They are called **self-closing** or **void** elements. The most common one you will use is the line-break tag, written as `<br>` (it stands for "break"). You can also see it written as `<br />` — both are valid.

---

## 3. Your First HTML Page

Let's write your very first HTML file. Open your code editor (Visual Studio Code is recommended, but any plain-text editor works — even Notepad on Windows or TextEdit on Mac).

### Step 1: Create a folder

On your computer, create a folder called `learn-dev` (or any name you like). Inside that folder, create another folder called `my-first-page`.

### Step 2: Create the HTML file

Inside `my-first-page`, create a file called `index.html`. The name `index` is special — web servers look for a file with this name by default when someone visits a folder.

### Step 3: Type this exact code

Type the following code into `index.html`. Do not copy and paste. Type it yourself so your fingers learn the keystrokes.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>My First Web Page</title>
  </head>
  <body>
    <h1>Hello, World!</h1>
    <p>This is my very first web page. I wrote it myself.</p>
  </body>
</html>
```

### Step 4: Save the file

Save the file with `Ctrl+S` (Windows/Linux) or `Cmd+S` (Mac).

### Step 5: Open it in a browser

Open your file explorer, find `index.html`, and double-click it. It will open in your default web browser. You should see a page that looks like this:

- A big bold title that says **Hello, World!**
- A paragraph underneath that says **This is my very first web page. I wrote it myself.**

**Congratulations. You just built your first web page.**

---

## 4. What Did You Just Write? (Line by Line)

Let's go through each line and explain what it does. Do not worry about memorizing every detail — you will see these lines hundreds of times in this course, and they will become second nature.

### Line 1: `<!DOCTYPE html>`

```html
<!DOCTYPE html>
```

This line tells the browser: "The following document is HTML5, the modern version of HTML." Without this line, browsers can fall back into an old, broken rendering mode called "quirks mode" that displays things wrong. Always include this line at the top of every HTML file.

### Line 2: `<html lang="en">`

```html
<html lang="en">
```

This is the **root element** of the page. Everything else in the document lives inside this `<html>` tag. The `lang="en"` part is an **attribute** — a small piece of extra information attached to the tag. `lang="en"` tells the browser and screen readers "this page is in English." We will cover attributes in detail in later lessons.

### Lines 3–6: The `<head>`

```html
<head>
  <meta charset="UTF-8">
  <title>My First Web Page</title>
</head>
```

The `<head>` section contains **information about the page that is not visible on the page itself.** Think of it as the metadata — settings, configurations, the page title that appears in the browser tab. The `<meta charset="UTF-8">` tells the browser to use UTF-8 character encoding, which allows you to use any character from any language. The `<title>` is what appears in your browser's tab bar.

### Lines 7–10: The `<body>`

```html
<body>
  <h1>Hello, World!</h1>
  <p>This is my very first web page. I wrote it myself.</p>
</body>
```

The `<body>` section contains **everything that is visible on the page.** Your headings, paragraphs, images, links, buttons, everything. The `<h1>` is a "level 1 heading" — the biggest, most important heading on the page. The `<p>` is a paragraph of normal text.

### Line 11: `</html>`

The closing `</html>` tag marks the end of the document. Everything in the page lives between `<html>` and `</html>`.

---

## 5. Why Does HTML Use Tags at All?

You might wonder: why doesn't the browser just figure out what's a heading and what's a paragraph from how the text looks?

The answer: **because text appearance is unreliable.** What if you want a heading that looks small? What if you want a paragraph that looks bold? If the browser guessed the structure from appearance, you would have no way to control them independently.

By separating **structure** (HTML) from **appearance** (CSS — which we will learn in the next module), you can change how things look without rewriting the content. A heading stays a heading even if you make it pink and tiny. This separation of concerns is one of the foundational ideas of the modern web.

There is also an accessibility reason: screen readers for blind users need to know what's a heading and what's a paragraph so they can read the page meaningfully. Tags carry that meaning.

---

## Try It Yourself

Before moving on, do these exercises. They will take 5–10 minutes each.

### Exercise 1: Change the Greeting

Open your `index.html` and change `<h1>Hello, World!</h1>` to:

```html
<h1>Hello, my name is [Your Name]!</h1>
```

Save and refresh the browser. You should see your name in the heading.

### Exercise 2: Add Another Paragraph

Right below the first paragraph, add a second one:

```html
<p>I am learning how to build websites from scratch.</p>
```

Save and refresh. You should now see two paragraphs.

### Exercise 3: Break the Page on Purpose

Try removing the closing `</body>` tag, save, and refresh. What happens? Probably nothing visible — browsers are forgiving. But this is a bad habit. Always close every tag you open. Now put the `</body>` back.

---

## Common Mistakes

### Mistake 1: Forgetting the slash in the closing tag

```html
<!-- WRONG: this is not a closing tag, it's another opening tag -->
<p>Hello<p>
```

If you write `<p>Hello<p>`, the browser thinks you opened two paragraphs and never closed the first one. Always include the slash: `</p>`.

### Mistake 2: Using the wrong kind of brackets

HTML uses angle brackets `< >`. Curly braces `{ }` and parentheses `( )` will not work. Double-check your keyboard.

### Mistake 3: Saving the file as `.txt` instead of `.html`

If your file is named `index.html.txt`, the browser will show you the code, not the rendered page. In Windows, make sure "File name extensions" is turned on in File Explorer's View menu so you can see the full name.

### Mistake 4: Forgetting `<!DOCTYPE html>`

The page might still display, but in quirks mode — which means your CSS will behave weirdly later. Always start with `<!DOCTYPE html>`.

---

## Summary

- HTML stands for HyperText Markup Language.
- HTML describes the **structure** of a web page using **tags**.
- Every element has three parts: opening tag, content, closing tag.
- The minimum HTML page has `<!DOCTYPE html>`, `<html>`, `<head>`, and `<body>`.
- The `<body>` contains everything visible; the `<head>` contains metadata.
- HTML is not a programming language — it cannot calculate or make decisions.

You wrote your first web page. That is huge. In the next lesson, we will go deeper into the required structure of every HTML document so you understand every single piece of the skeleton.

---

**Next:** [Lesson 02: HTML Document Structure →](./02-html-document-structure.md)
