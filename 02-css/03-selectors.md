# Lesson 03: Selectors

> Module: CSS · Lesson 3 of 16
> Estimated time: 60–75 minutes

Selectors are how you tell CSS **which elements** to style. There are dozens of selector types, and mastering them is essential. In this lesson, you will learn every common selector, the difference between them, and the critical concept of **specificity** — the rule that decides which selector wins when they conflict.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use type, class, ID, attribute, and pseudo-class selectors.
2. Combine selectors to target elements precisely.
3. Understand the difference between descendant, child, and sibling combinators.
4. Use the universal selector `*`.
5. Explain CSS specificity and calculate the winner of selector conflicts.
6. Group selectors to apply the same style to multiple elements.

---

## 1. The Basic Selector Types

### 1.1. Type (Element) Selector

Targets all elements of a given type:

```css
p { color: navy; }       /* all <p> */
h1 { color: red; }       /* all <h1> */
a { text-decoration: none; }  /* all <a> */
```

No punctuation — just the tag name. Simple but applies to every matching element.

### 1.2. Class Selector

Targets elements with a specific class. Use a dot `.`:

```css
.card { border: 1px solid #ccc; }     /* all class="card" */
.featured { background: gold; }       /* all class="featured" */
```

Classes are reusable — multiple elements can have the same class, and one element can have multiple classes:

```html
<div class="card featured">...</div>
<div class="card">...</div>
<div class="card">...</div>
```

This `<div>` has both `card` and `featured` — both rules apply.

### 1.3. ID Selector

Targets a single element with a specific ID. Use a hash `#`:

```css
#main-nav { background: black; }    /* the element with id="main-nav" */
#header { color: white; }          /* the element with id="header" */
```

IDs must be unique — only one element per page can have a given `id`. They're more specific than classes, which means an ID selector beats a class selector in conflicts.

**Best practice**: prefer classes over IDs for styling. IDs are mainly useful for JavaScript targeting and in-page anchors.

### 1.4. Universal Selector

Targets every element on the page. Use `*`:

```css
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
```

This rule applies to every element. Common use: CSS resets that strip default browser styles.

The universal selector has **very low specificity** — it loses to almost any other selector. That's why it's safe for resets.

### 1.5. Attribute Selector

Targets elements that have a specific attribute. Use square brackets `[...]`:

```css
[required] { border-color: red; }       /* any element with the required attribute */
[type="email"] { background: #f0f8ff; } /* any element with type="email" */
a[href^="https://"] { color: green; }   /* links starting with https:// */
a[href$=".pdf"] { color: red; }         /* links ending in .pdf */
a[href*="example.com"] { font-weight: bold; }  /* links containing "example.com" */
```

Attribute selectors let you target elements based on what's inside their attributes. Very powerful for forms, links, and structured data.

---

## 2. Combinators — Selecting Related Elements

### 2.1. Descendant Combinator (Space)

Targets an element that's a descendant of another element. Use a space:

```css
article p { color: #333; }       /* <p> anywhere inside <article> */
nav a { color: white; }          /* <a> anywhere inside <nav> */
```

Works at any depth — `article p` matches a `<p>` that's 10 levels deep inside `<article>`.

### 2.2. Child Combinator (`>`)

Targets an element that's a **direct child** of another. Use `>`:

```css
article > p { color: navy; }     /* <p> directly inside <article> */
```

Matches:

```html
<article>
  <p>This matches.</p>          <!-- direct child -->
  <section>
    <p>This does NOT match.</p>  <!-- grandchild, not direct child -->
  </section>
</article>
```

Use `>` when you want only direct children, not deeply nested descendants.

### 2.3. Adjacent Sibling Combinator (`+`)

Targets an element that immediately follows another. Use `+`:

```css
h2 + p { font-weight: bold; }    /* <p> right after an <h2> */
```

Matches:

```html
<h2>Section</h2>
<p>This is bold.</p>             <!-- matches -->
<p>This is not.</p>             <!-- not immediately after <h2> -->
```

Useful for "intro paragraph" effects — the paragraph immediately after a heading gets styled differently.

### 2.4. General Sibling Combinator (`~`)

Targets all siblings that follow another element. Use `~`:

```css
h2 ~ p { color: gray; }   /* all <p> after an <h2> at the same level */
```

Matches all following siblings, not just the immediate one.

---

## 3. Grouping Selectors

Apply the same style to multiple selectors by separating them with commas:

```css
h1, h2, h3 {
  color: navy;
  font-family: Georgia, serif;
}

.card, .sidebar, .widget {
  border: 1px solid #ccc;
  padding: 15px;
}
```

Each selector in the group is treated independently — `h1, h2, h3 { color: navy }` is the same as:

```css
h1 { color: navy; }
h2 { color: navy; }
h3 { color: navy; }
```

But grouped is shorter and easier to maintain.

---

## 4. Pseudo-Classes — State-Based Selectors

Pseudo-classes target elements based on their **state** rather than their identity. Use a colon `:`.

### 4.1. `:hover` — Mouse Over

```css
a:hover { color: red; text-decoration: underline; }
button:hover { background: #eee; }
```

Applies when the user hovers the mouse over the element. Doesn't work on touch devices, so don't make critical functionality hover-only.

### 4.2. `:focus` — Input Focused

```css
input:focus { border-color: blue; outline: none; }
```

Applies when the element has keyboard or click focus (e.g., when the user tabs to it). Always style `:focus` for accessibility.

### 4.3. `:active` — Being Clicked

```css
button:active { transform: scale(0.95); }
```

Applies while the element is being clicked (between mouse down and mouse up).

### 4.4. `:visited` — Visited Link

```css
a:visited { color: purple; }
```

Applies to links the user has already visited.

### 4.5. `:link` — Unvisited Link

```css
a:link { color: blue; }
```

Applies to links the user has not yet visited.

### 4.6. `:checked` — Checkbox/Radio Selected

```css
input:checked + label { font-weight: bold; }
```

Applies to checkboxes and radios that are checked. Often used with sibling selectors to style the corresponding label.

### 4.7. `:disabled` and `:enabled`

```css
input:disabled { background: #eee; cursor: not-allowed; }
```

### 4.8. `:first-child`, `:last-child`, `:only-child`

```css
li:first-child { font-weight: bold; }   /* first <li> in its parent */
li:last-child { border-bottom: none; }  /* last <li> in its parent */
```

### 4.9. `:nth-child(n)` — Position-Based

```css
li:nth-child(odd) { background: #f9f9f9; }    /* alternating rows */
li:nth-child(even) { background: white; }
li:nth-child(3) { color: red; }              /* the third <li> */
```

### 4.10. `:not()` — Negation

```css
a:not(.external) { color: blue; }   /* all links except those with class="external" */
```

### 4.11. `:nth-of-type(n)` — Of a Specific Type

```css
p:nth-of-type(2) { color: red; }   /* the second <p> inside its parent */
```

Like `:nth-child`, but only counts siblings of the same tag name.

### 4.12. `:empty` — No Children

```css
div:empty { display: none; }   /* <div> with no content or whitespace */
```

### LVHA Order for Links

When styling links, follow this order to avoid conflicts:

```css
a:link    { color: blue; }      /* unvisited */
a:visited { color: purple; }    /* visited */
a:hover   { color: red; }       /* hover (must come after :link and :visited) */
a:active  { color: orange; }    /* active (must come last) */
```

Remember: **L-V-H-A** — Link, Visited, Hover, Active.

---

## 5. Pseudo-Elements — Sub-Element Styling

Pseudo-elements target parts of an element, not the whole element. Use a double colon `::`. (Single colon works for backward compatibility, but `::` is the modern syntax.)

### 5.1. `::before` and `::after` — Generated Content

```css
.quote::before {
  content: "\201C";  /* opening curly quote " */
  font-size: 2em;
  color: gray;
}

.quote::after {
  content: "\201D";  /* closing curly quote " */
  font-size: 2em;
  color: gray;
}

.link-icon::after {
  content: " ↗";
}
```

The `content` property is required — it specifies what to insert. The pseudo-element appears as the first or last child of the targeted element.

Use cases: decorative quotation marks, icons, badges, clear-fixes.

### 5.2. `::first-letter` and `::first-line`

```css
p::first-letter {
  font-size: 2em;
  font-weight: bold;
  float: left;
}

p::first-line {
  font-weight: bold;
}
```

Style the first letter or first line of a paragraph. Useful for drop caps and intro effects.

### 5.3. `::selection` — Highlighted Text

```css
::selection {
  background: yellow;
  color: black;
}
```

Styles the part of the page the user has highlighted with their mouse.

---

## 6. CSS Specificity — The Cascade Conflict Resolver

When two rules apply to the same element and set the same property, the browser uses **specificity** to decide the winner. Specificity is a 3-number score (one slot per kind of selector):

```
A, B, C
```

- **A** = number of ID selectors (`#main-nav`)
- **B** = number of class selectors, attribute selectors, and pseudo-classes (`.card`, `[type="text"]`, `:hover`)
- **C** = number of type selectors and pseudo-elements (`p`, `h1`, `::before`)

The universal selector `*` and combinators (`,`, ` `, `>`, `+`, `~`) have **zero** specificity.

> **Note on `!important` and inline styles.** These are *not* part of specificity — they are separate cascade overrides that beat any selector. Inline styles (`style="..."`) win over every selector except `!important`, and `!important` wins over everything else. Use them sparingly: they make CSS harder to maintain because they cannot be overridden by more specific selectors.

### Calculating Specificity

| Selector | A (IDs) | B (classes/attr/pseudo) | C (types) | Score |
|----------|---------|--------------------------|-----------|-------|
| `*` | 0 | 0 | 0 | 0,0,0 |
| `p` | 0 | 0 | 1 | 0,0,1 |
| `p::before` | 0 | 0 | 2 | 0,0,2 |
| `.card` | 0 | 1 | 0 | 0,1,0 |
| `p.card` | 0 | 1 | 1 | 0,1,1 |
| `#main` | 1 | 0 | 0 | 1,0,0 |
| `div.card.featured` | 0 | 2 | 1 | 0,2,1 |
| `#main .card` | 1 | 1 | 0 | 1,1,0 |
| `nav#main .item:hover` | 1 | 2 | 1 | 1,2,1 |

Higher score wins. ID selectors (`#main`) beat class selectors (`.card`), which beat type selectors (`p`).

### Example Conflict

```css
.card { color: red; }       /* 0,0,1,0 */
#main .card { color: blue; } /* 0,1,1,0 - WINS */
```

A `<div class="card" id="...">` inside `#main` will be blue because the second rule has higher specificity.

### Source Order Tiebreaker

If two rules have the **same specificity**, the one declared later wins:

```css
.card { color: red; }
.card { color: blue; }   /* later — wins */
```

### `!important` Override

You can force a rule to win with `!important`:

```css
.card { color: red !important; }
#main .card { color: blue; }   /* loses to !important */
```

Avoid `!important` whenever possible — it overrides the cascade and is very hard to undo. Use it sparingly.

### Inline Styles Are Most Specific

```html
<p style="color: green">...</p>
```

Inline styles have a specificity higher than any selector (except `!important`). This is why you should rarely use inline styles — they're nearly impossible to override.

---

## 7. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Selectors Demo</title>
    <link rel="stylesheet" href="selectors.css">
  </head>
  <body>
    <header id="site-header">
      <h1>My Site</h1>
      <nav class="main-nav">
        <a href="#" class="nav-link">Home</a>
        <a href="#" class="nav-link active">About</a>
        <a href="#" class="nav-link">Contact</a>
      </nav>
    </header>

    <main id="content">
      <article class="post featured">
        <h2>Featured Post</h2>
        <p class="intro">This is the intro paragraph.</p>
        <p>This is a regular paragraph.</p>
        <p>This is another paragraph.</p>
      </article>

      <article class="post">
        <h2>Regular Post</h2>
        <p>Just some text.</p>
        <a href="https://example.com" target="_blank">External link</a>
      </article>
    </main>

    <input type="email" placeholder="Email">
    <input type="text" placeholder="Name" required>
  </body>
</html>
```

```css
/* selectors.css */

/* Universal reset */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Type selectors */
body {
  font-family: Arial, sans-serif;
  line-height: 1.6;
  color: #333;
  background: #f8f9fa;
  padding: 20px;
}

h1, h2 { color: #2c3e50; }

/* ID selector */
#site-header {
  background: #2c3e50;
  color: white;
  padding: 20px;
  margin-bottom: 30px;
}

#site-header h1 { color: white; }

/* Class selectors */
.main-nav { margin-top: 10px; }

.nav-link {
  color: white;
  margin-right: 15px;
  text-decoration: none;
}

.nav-link.active {
  font-weight: bold;
  border-bottom: 2px solid white;
}

.post {
  background: white;
  padding: 20px;
  margin-bottom: 20px;
  border-radius: 6px;
}

.post.featured {
  border: 2px solid gold;
  background: #fffef5;
}

.intro {
  font-size: 1.2em;
  font-style: italic;
}

/* Descendant combinator */
.post p { color: #444; }
.post h2 { margin-bottom: 10px; }

/* Child combinator */
#content > article { margin-bottom: 20px; }

/* Adjacent sibling */
h2 + p { font-weight: bold; }

/* Attribute selector */
a[target="_blank"]::after {
  content: " ↗";
  color: gray;
}

input[type="email"] {
  background: #f0f8ff;
}

[required] {
  border: 2px solid #c0392b;
}

/* Pseudo-classes */
a:hover { text-decoration: underline; }
.nav-link:hover { color: #ecf0f1; }

input:focus {
  outline: none;
  border-color: #3498db;
}

/* nth-child */
.post p:nth-child(2) {
  background: #fafafa;
  padding: 10px;
}

/* not() */
.nav-link:not(.active) {
  opacity: 0.7;
}

/* ::first-letter */
.intro::first-letter {
  font-size: 2em;
  color: #c0392b;
  font-weight: bold;
}

/* ::selection */
::selection {
  background: #f1c40f;
  color: #2c3e50;
}
```

Save both files. Open the HTML in your browser. Experiment — try changing selectors and see what gets styled.

---

## Try It Yourself

### Exercise 1: Style Specific Sections

Using the HTML above, write CSS that:

- Makes `.intro` italic.
- Adds a gold border only to `.post.featured`.
- Styles only the second `<p>` inside each `.post` (using `:nth-child`).
- Changes the link color when hovered.

### Exercise 2: Form Styling

Create a form with text, email, and password inputs. Write CSS that:

- Highlights focused inputs with a blue border.
- Shows a red border on `required` fields.
- Disables fields have a gray background.
- The placeholder text is gray and italic.

### Exercise 3: Specificity Battle

Predict the color of the paragraph:

```html
<div id="main" class="container">
  <p class="text">Hello</p>
</div>
```

```css
#main .text { color: red; }
.container .text { color: blue; }
.text { color: green; }
p.text { color: orange; }
```

Answer: **red** — `#main .text` has higher specificity (one ID) than the others (which have zero IDs).

---

## Common Mistakes

### Mistake 1: Spacing between selector and pseudo-class

```css
/* WRONG: space between element and pseudo-class */
p : hover { color: red; }

/* CORRECT */
p:hover { color: red; }
```

The space turns this into "any element that's hovered, inside a `<p>`" — completely different!

### Mistake 2: Confusing class and ID syntax

```css
/* WRONG */
#main.card { }   /* treats as ID, not class */
.main#card { }   /* treats as class, not ID */
```

- `.` is for classes.
- `#` is for IDs.

### Mistake 3: Over-qualifying selectors

```css
/* BAD: redundant tag prefix */
div.card { }
p.intro { }
```

If `.card` is only ever applied to `<div>`, you don't need the `div` prefix. It just makes the selector harder to override. Prefer `.card` alone unless the tag distinction matters.

### Mistake 4: Overusing IDs for styling

```css
/* BAD */
#header { }
#footer { }
#main-content { }
```

IDs are too specific — they can't be reused and override class selectors. Use classes instead.

### Mistake 5: Forgetting the `content` property on `::before`/`::after`

```css
/* BAD: no content */
.quote::before { color: gray; }
```

Without `content`, the pseudo-element doesn't appear. Always include `content: "";` even if it's empty.

### Mistake 6: Wrong LVHA order

```css
a:hover { color: red; }
a:visited { color: purple; }   /* overrides hover */
```

Order matters. Use L-V-H-A: link, visited, hover, active.

---

## Summary

- Basic selectors: type (`p`), class (`.card`), ID (`#main`), attribute (`[type="text"]`), universal (`*`).
- Combinators: descendant (space), child (`>`), adjacent sibling (`+`), general sibling (`~`).
- Group selectors with commas to apply the same style to multiple.
- Pseudo-classes target state: `:hover`, `:focus`, `:active`, `:first-child`, `:nth-child(n)`, `:not()`, `:checked`.
- Pseudo-elements target sub-parts: `::before`, `::after`, `::first-letter`, `::first-line`, `::selection`.
- Specificity decides conflicts: ID > class > type. Higher specificity wins.
- Source order breaks ties: later wins.
- `!important` overrides specificity — avoid using it.
- Inline styles have the highest specificity — avoid them too.

In the next lesson, we will learn about colors — hex, RGB, HSL, alpha, and how to choose beautiful color palettes.

---

**Next:** [Lesson 04: Colors →](./04-colors.md)
