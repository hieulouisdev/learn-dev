# Lesson 05: Links

> Module: HTML · Lesson 5 of 13
> Estimated time: 45–60 minutes

Links are what make the web a *web*. Without links, every page would be an island. With links, every page is connected to every other page, creating the interconnected network we call the World Wide Web. In this lesson, you will learn everything about creating links — to other pages, to sections of the same page, to email addresses, to phone numbers, and more.

---

## Learning Objectives

After this lesson, you will be able to:

1. Create a link to another web page using the `<a>` tag.
2. Distinguish between absolute URLs and relative URLs.
3. Link to a specific section of a page using `id` attributes.
4. Open links in new tabs using `target="_blank"`.
5. Create email, phone, and download links.
6. Build a navigation menu for a multi-page website.

---

## 1. The `<a>` Tag

The `<a>` tag (short for "anchor") creates a link. The text inside the tag is what the user clicks, and the `href` attribute says where the link goes.

```html
<a href="https://www.google.com">Go to Google</a>
```

When rendered, the words "Go to Google" appear as blue underlined text. Clicking them takes you to the URL in `href`.

The `<a>` tag has two parts:

- **`href`** — the destination URL. (Stands for "hypertext reference.")
- **The link text** — what the user sees and clicks.

### Best Practice: Write Descriptive Link Text

Beginners often write "click here":

```html
<!-- BAD -->
<p>To learn more, <a href="https://example.com">click here</a>.</p>
```

This is bad for two reasons:

1. Screen reader users often navigate by jumping from link to link. Hearing "click here" a hundred times tells them nothing.
2. Search engines use link text to understand the destination.

Always write descriptive link text:

```html
<!-- GOOD -->
<p>Read more in the <a href="https://example.com/docs">official documentation</a>.</p>
```

---

## 2. Absolute vs. Relative URLs

The `href` attribute can hold two kinds of URLs: absolute and relative.

### Absolute URLs

An absolute URL contains the full address, including the protocol (`https://`) and domain (`example.com`). Use absolute URLs to link to other websites.

```html
<a href="https://www.wikipedia.org">Wikipedia</a>
<a href="https://github.com/hieulouisdev/learn-dev">Learn Dev on GitHub</a>
```

### Relative URLs

A relative URL does not include the protocol or domain — it just gives the path relative to the current page. Use relative URLs to link between pages on your own site.

If your file structure is:

```
my-site/
├── index.html
├── about.html
└── contact.html
```

From `index.html`, you can link to the other pages like this:

```html
<a href="about.html">About</a>
<a href="contact.html">Contact</a>
```

The browser figures out the full URL by combining the current page's location with the relative path.

### Subfolders

If your files are organized into folders:

```
my-site/
├── index.html
├── pages/
│   ├── about.html
│   └── contact.html
```

From `index.html`:

```html
<a href="pages/about.html">About</a>
```

From `pages/about.html` back to `index.html`:

```html
<a href="../index.html">Home</a>
```

The `../` means "go up one folder level."

### Root-Relative URLs

A URL starting with `/` means "starting from the root of the website." If your site is `example.com` and you write `href="/about.html"`, the browser goes to `https://example.com/about.html` regardless of which page you're on. This is useful for big sites with deep folder structures.

---

## 3. Linking Within a Page

You can link to a specific section of the same page using `id` attributes. First, give an element an `id`:

```html
<h2 id="chapters">Chapters</h2>
```

Then link to it with a hash:

```html
<a href="#chapters">Jump to Chapters</a>
```

When the user clicks, the browser scrolls down to the heading with that id. This is how Wikipedia's table of contents works.

### Linking to a Section on Another Page

Combine a relative URL with a hash:

```html
<a href="about.html#team">Meet the team</a>
```

The browser loads `about.html` and scrolls down to the element with `id="team"`.

### Back to Top

A common pattern is a "Back to top" link at the bottom of a long page:

```html
<a href="#">Back to top</a>
```

The empty hash `#` represents the top of the page.

---

## 4. Opening Links in New Tabs

By default, clicking a link opens the new page in the same tab. To open it in a new tab (or new window, depending on browser settings), use the `target` attribute:

```html
<a href="https://example.com" target="_blank" rel="noopener noreferrer">
  Open in new tab
</a>
```

The `target="_blank"` opens the link in a new tab. The `rel="noopener noreferrer"` is important for security — it prevents the new page from accessing the original page's window object via JavaScript. **Always include `rel="noopener noreferrer"` when you use `target="_blank"`.**

### When to Open in a New Tab

Best practices:

- **External links** (to other websites): open in a new tab. Users don't want to lose their place on your site.
- **Internal links** (to your own pages): open in the same tab. Users expect to "go" somewhere within your site.
- **Download links** (PDFs, images): open in a new tab so users can come back easily.
- **Newsletter sign-ups or important actions**: same tab — users should focus on completing the action.

These are guidelines, not laws. Use your judgment.

---

## 5. Email Links

You can make a link that opens the user's email client with a pre-filled message:

```html
<a href="mailto:hello@example.com">Send me an email</a>
```

You can add a subject and body:

```html
<a href="mailto:hello@example.com?subject=Hello&body=I%20saw%20your%20site">
  Send me an email
</a>
```

Note the `%20` — that is the URL-encoded version of a space character. URLs cannot contain spaces, so spaces must be encoded as `%20`.

### When NOT to Use `mailto:`

Putting your email in a `mailto:` link makes it easy for spambots to harvest. Consider using a contact form instead, or write your email as `hello [at] example [dot] com` to confuse bots.

---

## 6. Phone Links

On mobile devices, a phone link opens the dialer:

```html
<a href="tel:+18005550100">Call us at 1-800-555-0100</a>
```

Always include the country code (the `+1` for the US, `+84` for Vietnam, etc.) so international callers can reach you.

---

## 7. Download Links

To link to a file that should be downloaded (like a PDF or ZIP), add the `download` attribute:

```html
<a href="report.pdf" download>Download the report (PDF)</a>
```

You can also suggest a filename:

```html
<a href="report.pdf" download="2026-annual-report.pdf">
  Download the report
</a>
```

The browser will download the file instead of navigating to it.

---

## 8. Image Links

You can wrap an image in an `<a>` tag to make it clickable:

```html
<a href="https://example.com">
  <img src="logo.png" alt="Company Logo">
</a>
```

The image becomes a clickable link to the URL. We will cover images in detail in the next lesson.

---

## 9. A Complete Example: A Multi-Page Website

Let's build a tiny three-page website. Create a folder called `mini-site` with these three files.

### File 1: `index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Mini Site — Home</title>
  </head>
  <body>
    <header>
      <h1>My Mini Site</h1>
      <nav>
        <a href="index.html">Home</a> |
        <a href="about.html">About</a> |
        <a href="contact.html">Contact</a>
      </nav>
    </header>

    <main>
      <h2>Welcome!</h2>
      <p>This is a small example site with three pages.</p>
      <p>
        Read more on the <a href="about.html">About page</a>, or
        <a href="contact.html">get in touch</a>.
      </p>
      <p>
        Visit <a href="https://www.wikipedia.org" target="_blank" rel="noopener noreferrer">Wikipedia</a>
        in a new tab.
      </p>
    </main>

    <footer>
      <p>© 2026 My Mini Site.</p>
    </footer>
  </body>
</html>
```

### File 2: `about.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Mini Site — About</title>
  </head>
  <body>
    <header>
      <h1>My Mini Site</h1>
      <nav>
        <a href="index.html">Home</a> |
        <a href="about.html">About</a> |
        <a href="contact.html">Contact</a>
      </nav>
    </header>

    <main>
      <h2>About Me</h2>
      <p>I am learning HTML one lesson at a time.</p>

      <h3 id="goals">My Goals</h3>
      <p>By the end of this year, I want to build a personal portfolio.</p>

      <h3 id="tools">Tools I Use</h3>
      <p>Visual Studio Code and a web browser.</p>

      <p><a href="#goals">Jump to Goals</a> | <a href="#tools">Jump to Tools</a></p>
    </main>

    <footer>
      <p>© 2026 My Mini Site. <a href="#">Back to top</a></p>
    </footer>
  </body>
</html>
```

### File 3: `contact.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Mini Site — Contact</title>
  </head>
  <body>
    <header>
      <h1>My Mini Site</h1>
      <nav>
        <a href="index.html">Home</a> |
        <a href="about.html">About</a> |
        <a href="contact.html">Contact</a>
      </nav>
    </header>

    <main>
      <h2>Contact Me</h2>
      <p>
        Email: <a href="mailto:hello@example.com?subject=Hello">hello@example.com</a><br>
        Phone: <a href="tel:+18005550100">1-800-555-0100</a>
      </p>
    </main>

    <footer>
      <p>© 2026 My Mini Site.</p>
    </footer>
  </body>
</html>
```

Save all three files in the same folder. Open `index.html` in your browser. Click around — the navigation links work between pages, the in-page links jump to sections, and the external link opens Wikipedia in a new tab.

---

## Try It Yourself

### Exercise 1: Build Your Own Three-Page Site

Create a folder called `my-portfolio` with three files:

- `index.html` — a home page introducing yourself.
- `projects.html` — a list of (made-up) projects you'd like to build.
- `contact.html` — your email and social links.

Add a navigation bar to every page so users can move between them. Make the email link a `mailto:` link.

### Exercise 2: Wikipedia-Style Table of Contents

Create a long page with several `<h2>` sections. At the top, add a table of contents that links to each section. At the bottom of each section, add a "Back to top" link.

### Exercise 3: Identify the Mistake

What's wrong with this link?

```html
<a href="https://example.com" target="_blank">External link</a>
```

Answer: Missing `rel="noopener noreferrer"`. Add it to prevent security issues.

---

## Common Mistakes

### Mistake 1: Forgetting the `href`

```html
<!-- WRONG: no destination -->
<a>Click here</a>
```

Without `href`, the `<a>` tag is not clickable as a link. Always include `href`.

### Mistake 2: Forgetting `rel="noopener noreferrer"` with `target="_blank"`

This is a security issue. Always add it.

### Mistake 3: Linking to `index.html` in the same folder

If you write `<a href="index.html">` from inside `index.html`, you create a link to the current page. It works but is redundant. Use `<a href="#">` or `<a href="index.html">` thoughtfully.

### Mistake 4: Using "click here" as link text

Use descriptive text that tells users where they're going.

### Mistake 5: Forgetting to encode spaces in mailto URLs

```html
<!-- WRONG: raw space -->
<a href="mailto:hi@example.com?subject=Hello there">Email</a>
```

Replace spaces with `%20`:

```html
<a href="mailto:hi@example.com?subject=Hello%20there">Email</a>
```

### Mistake 6: Broken relative paths

If a link doesn't work, check your relative path. From `pages/about.html`, `index.html` is at `../index.html`, not `index.html`.

---

## Summary

- The `<a>` tag creates links. The `href` attribute specifies the destination.
- Use **absolute URLs** (`https://...`) to link to other sites, **relative URLs** (`about.html`) to link within your site.
- Link to a section of a page by giving an element an `id` and using `href="#id"`.
- Use `target="_blank"` with `rel="noopener noreferrer"` to open links in new tabs.
- Use `mailto:` for email links, `tel:` for phone links, `download` for file downloads.
- Always use descriptive link text, not "click here."
- The `../` in a relative path means "go up one folder level."

In the next lesson, we will learn how to add images to a web page.

---

**Next:** [Lesson 06: Images →](./06-images.md)
