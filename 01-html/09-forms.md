# Lesson 09: Forms

> Module: HTML · Lesson 9 of 13
> Estimated time: 60–90 minutes

Forms are how the web collects information from users. Every search box, login screen, signup form, and checkout page is built with HTML forms. In this lesson, you will learn every input type, how to label them, and how to structure them for both users and developers.

---

## Learning Objectives

After this lesson, you will be able to:

1. Build a form using the `<form>` element.
2. Use all common input types: text, email, password, number, date, etc.
3. Label inputs properly with `<label>` and the `for` attribute.
4. Use textareas, select dropdowns, radios, and checkboxes.
5. Group related fields with `<fieldset>` and `<legend>`.
6. Add the `required` attribute and basic validation.
7. Submit a form (we'll just look at the HTML — handling submissions needs a backend).

---

## 1. The `<form>` Element

The `<form>` element wraps everything that collects input. Two attributes matter most:

```html
<form action="/submit" method="POST">
  <!-- inputs go here -->
</form>
```

- **`action`** — the URL where the form data is sent.
- **`method`** — the HTTP method: `GET` (for safe, idempotent requests like search) or `POST` (for actions that change data, like signup or checkout).

For now, you can leave `action` empty or use `action="#"` so the form does nothing destructive while you practice.

---

## 2. The `<label>` Element — Critical for Accessibility

Every input should have a label. The label tells the user what to enter and tells screen readers what the input is for. The label uses the `for` attribute matching the input's `id`:

```html
<label for="name">Your Name:</label>
<input type="text" id="name" name="name">
```

Click the label text — the input gets focus. This is a usability win that also helps screen reader users.

### Alternative: Wrapping the Input

You can also wrap the input inside the label, in which case you don't need the `for`/`id` pair:

```html
<label>
  Your Name:
  <input type="text" name="name">
</label>
```

Both patterns work. Pick one and use it consistently. The `for`/`id` pattern is more common because it gives you more layout flexibility.

---

## 3. The `<input>` Element — Many Types

The `<input>` element is the most versatile form element. Its `type` attribute determines what kind of input it is.

### 3.1. Text Input

```html
<label for="username">Username:</label>
<input type="text" id="username" name="username">
```

Single-line text input. The default type if you omit `type`.

### 3.2. Email Input

```html
<label for="email">Email:</label>
<input type="email" id="email" name="email">
```

Looks like text, but the browser validates that the input is a valid email format before submitting. On mobile, the keyboard shows the `@` and `.` keys prominently.

### 3.3. Password Input

```html
<label for="password">Password:</label>
<input type="password" id="password" name="password">
```

Characters are masked (shown as dots or asterisks) as the user types.

### 3.4. Number Input

```html
<label for="age">Age:</label>
<input type="number" id="age" name="age" min="0" max="120" step="1">
```

Shows number spinner arrows. The browser validates that the input is a number. Use `min`, `max`, and `step` to restrict the range.

### 3.5. Date Input

```html
<label for="birthday">Birthday:</label>
<input type="date" id="birthday" name="birthday">
```

Shows a native date picker calendar.

### 3.6. Time, Datetime, Month, Week

```html
<input type="time">
<input type="datetime-local">
<input type="month">
<input type="week">
```

Each shows a different native picker.

### 3.7. Color Input

```html
<label for="favorite-color">Favorite Color:</label>
<input type="color" id="favorite-color" name="favorite-color" value="#ff0000">
```

Shows a color picker.

### 3.8. Range Input (Slider)

```html
<label for="volume">Volume:</label>
<input type="range" id="volume" name="volume" min="0" max="100" value="50">
```

Shows a slider. Pair it with JavaScript (later) to display the current value.

### 3.9. Checkbox

```html
<label>
  <input type="checkbox" name="subscribe" value="yes">
  Subscribe to the newsletter
</label>
```

A box that can be checked or unchecked. Use `checked` to start checked:

```html
<input type="checkbox" name="subscribe" value="yes" checked>
```

Multiple checkboxes use an array-style name (e.g. `name="subscribe[]"`) so the browser submits all selected values as a list. If several checkboxes share a plain `name="subscribe"` without the `[]`, only the last checked value reaches the server.

### 3.10. Radio Buttons

```html
<p>Select your plan:</p>
<label>
  <input type="radio" name="plan" value="free" checked> Free
</label>
<label>
  <input type="radio" name="plan" value="pro"> Pro
</label>
<label>
  <input type="radio" name="plan" value="enterprise"> Enterprise
</label>
```

Radios with the same `name` form a group — only one can be selected at a time. Use `checked` to start one selected.

### 3.11. File Input

```html
<label for="avatar">Upload your photo:</label>
<input type="file" id="avatar" name="avatar" accept="image/*">
```

The `accept` attribute restricts file types. `image/*` accepts any image. The form must use `enctype="multipart/form-data"`:

```html
<form action="/upload" method="POST" enctype="multipart/form-data">
  ...
</form>
```

### 3.12. Hidden Input

```html
<input type="hidden" name="user_id" value="12345">
```

Invisible to the user but submitted with the form. Used for internal data like user IDs or CSRF tokens.

### 3.13. Submit Button (as input)

```html
<input type="submit" value="Send Form">
```

Renders a button. Clicking submits the form. The `value` is the button text. (The `<button>` element is generally preferred — see section 5.)

### 3.14. Other Input Types

```html
<input type="tel">      <!-- phone number, mobile keyboard shows dial pad -->
<input type="url">      <!-- website URL, browser validates URL format -->
<input type="search">   <!-- search field, may show clear button -->
<input type="reset">    <!-- button that resets the form to defaults -->
<input type="button">   <!-- generic button, needs JavaScript to do anything -->
```

---

## 4. The `<textarea>` Element — Multi-line Text

For multi-line text (comments, messages, descriptions), use `<textarea>`:

```html
<label for="message">Your message:</label>
<textarea id="message" name="message" rows="4" cols="40">
Default text inside the textarea.
</textarea>
```

Note: unlike `<input>`, the default value goes **between** the opening and closing tags, not in a `value` attribute. The `rows` and `cols` attributes set the visible size. Use CSS for finer control.

---

## 5. The `<button>` Element

The `<button>` element creates a clickable button:

```html
<button type="submit">Send Form</button>
<button type="reset">Clear</button>
<button type="button" onclick="alert('Hi')">Click Me</button>
```

- `type="submit"` — submits the form (default if inside a `<form>`).
- `type="reset"` — resets the form fields to their defaults.
- `type="button"` — does nothing by itself; you attach JavaScript.

Prefer `<button>` over `<input type="submit">` because `<button>` can contain HTML (icons, formatted text), while `<input>` only shows plain text.

---

## 6. The `<select>` and `<option>` Elements — Dropdowns

Dropdowns use `<select>` containing `<option>`s:

```html
<label for="country">Country:</label>
<select id="country" name="country">
  <option value="">-- Choose --</option>
  <option value="us">United States</option>
  <option value="vn">Vietnam</option>
  <option value="jp">Japan</option>
</select>
```

The `value` attribute is what gets submitted. The text inside `<option>` is what the user sees. Use `selected` to mark the default:

```html
<option value="vn" selected>Vietnam</option>
```

### Optgroups

Group options with `<optgroup>`:

```html
<select name="car">
  <optgroup label="Swedish">
    <option value="volvo">Volvo</option>
    <option value="saab">Saab</option>
  </optgroup>
  <optgroup label="German">
    <option value="audi">Audi</option>
    <option value="bmw">BMW</option>
  </optgroup>
</select>
```

### Multiple Selections

Add `multiple` to allow more than one option:

```html
<select name="skills" multiple size="4">
  <option value="html">HTML</option>
  <option value="css">CSS</option>
  <option value="js">JavaScript</option>
</select>
```

Users Ctrl+click (Cmd+click on Mac) to select multiple. The `size` attribute shows multiple rows at once.

---

## 7. Grouping Fields: `<fieldset>` and `<legend>`

For long forms, group related fields:

```html
<form>
  <fieldset>
    <legend>Personal Information</legend>

    <label for="name">Name:</label>
    <input type="text" id="name" name="name"><br>

    <label for="email">Email:</label>
    <input type="email" id="email" name="email">
  </fieldset>

  <fieldset>
    <legend>Payment</legend>

    <label for="card">Card Number:</label>
    <input type="text" id="card" name="card"><br>

    <label for="cvc">CVC:</label>
    <input type="text" id="cvc" name="cvc">
  </fieldset>
</form>
```

The browser draws a border around each `<fieldset>` and shows the `<legend>` as the section title. This helps users understand the form structure.

---

## 8. Validation Attributes

### `required`

```html
<input type="text" name="name" required>
```

The form will not submit until this field is filled.

### `min` and `max`

For numbers, dates:

```html
<input type="number" min="0" max="100">
<input type="date" min="2026-01-01" max="2026-12-31">
```

### `minlength` and `maxlength`

For text:

```html
<input type="text" minlength="3" maxlength="20">
```

### `pattern`

A regular expression the input must match:

```html
<input type="text" pattern="[A-Za-z]{3}" title="Three letters only">
```

The `title` shows in the error message if the pattern fails.

### `placeholder`

Hint text shown inside an empty input:

```html
<input type="text" placeholder="e.g., John Smith">
```

Don't use `placeholder` as a replacement for `<label>` — placeholders disappear once the user starts typing, which is bad UX.

---

## 9. A Complete Example: Signup Form

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Signup Form</title>
  </head>
  <body>
    <h1>Create Your Account</h1>

    <form action="/signup" method="POST">
      <fieldset>
        <legend>Account Information</legend>

        <p>
          <label for="username">Username:</label><br>
          <input
            type="text"
            id="username"
            name="username"
            required
            minlength="3"
            maxlength="20"
            placeholder="Choose a username">
        </p>

        <p>
          <label for="email">Email:</label><br>
          <input
            type="email"
            id="email"
            name="email"
            required
            placeholder="you@example.com">
        </p>

        <p>
          <label for="password">Password:</label><br>
          <input
            type="password"
            id="password"
            name="password"
            required
            minlength="8">
        </p>
      </fieldset>

      <fieldset>
        <legend>Personal Information</legend>

        <p>
          <label for="fullname">Full Name:</label><br>
          <input type="text" id="fullname" name="fullname">
        </p>

        <p>
          <label for="birthday">Birthday:</label><br>
          <input type="date" id="birthday" name="birthday">
        </p>

        <p>
          <label for="country">Country:</label><br>
          <select id="country" name="country">
            <option value="">-- Choose --</option>
            <option value="us">United States</option>
            <option value="vn">Vietnam</option>
            <option value="jp">Japan</option>
          </select>
        </p>

        <p>
          <label for="bio">About You:</label><br>
          <textarea id="bio" name="bio" rows="4" cols="40" placeholder="Tell us a bit about yourself..."></textarea>
        </p>
      </fieldset>

      <fieldset>
        <legend>Preferences</legend>

        <p>
          <label>Subscription Plan:</label><br>
          <label><input type="radio" name="plan" value="free" checked> Free</label><br>
          <label><input type="radio" name="plan" value="pro"> Pro ($5/month)</label><br>
          <label><input type="radio" name="plan" value="enterprise"> Enterprise</label>
        </p>

        <p>
          <label>Interests (select all that apply):</label><br>
          <label><input type="checkbox" name="interests[]" value="frontend"> Frontend</label><br>
          <label><input type="checkbox" name="interests[]" value="backend"> Backend</label><br>
          <label><input type="checkbox" name="interests[]" value="design"> Design</label><br>
          <label><input type="checkbox" name="interests[]" value="devops"> DevOps</label>
        </p>

        <p>
          <label>
            <input type="checkbox" name="newsletter" value="yes">
            Send me the weekly newsletter
          </label>
        </p>
      </fieldset>

      <p>
        <button type="submit">Create Account</button>
        <button type="reset">Clear Form</button>
      </p>
    </form>
  </body>
</html>
```

Save as `signup.html`. Fill it out and submit. The browser will validate fields before submitting. Since we set `action="/signup"`, the form won't actually go anywhere — you'll see an error or a blank page. That's fine; in real life, a backend handles submission.

---

## Try It Yourself

### Exercise 1: Login Form

Build a simple login form with:

- Email field (required).
- Password field (required, min 8 chars).
- "Remember me" checkbox.
- Submit button.

### Exercise 2: Contact Form

Create a contact form with:

- Name, email, subject (text input).
- A `<select>` for topic (general, support, billing).
- A `<textarea>` for the message (required).
- A submit button.

### Exercise 3: Pizza Order Form

Build a fun pizza order form:

- Size (radio: small, medium, large).
- Crust (radio: thin, regular, thick).
- Toppings (checkboxes: pepperoni, mushrooms, peppers, onions, olives).
- Quantity (number input, min 1, max 20).
- Delivery or pickup (radio).
- Address (textarea, only if delivery — don't worry about showing/hiding it without JS).
- Submit button.

---

## Common Mistakes

### Mistake 1: Forgetting the `<label>`

```html
<!-- BAD: no label, just a placeholder -->
<input type="text" name="name" placeholder="Name">
```

Screen reader users hear "text input" with no context. Always use `<label>` with `for` matching the input's `id`.

### Mistake 2: Using placeholder as label

Placeholders disappear when typing. Use them for hints, not for the field's name.

### Mistake 3: Not setting `name`

Without `name`, the input is not submitted with the form. Every form field needs a `name`.

### Mistake 4: Same `id` for multiple inputs

`id`s must be unique on the page. Reusing `id="name"` for two inputs breaks label association and CSS targeting.

### Mistake 5: Forgetting `type="submit"` or `type="button"` on `<button>`

The default `type` for `<button>` inside a `<form>` is `submit`. If you want a button that doesn't submit (e.g., "Check Availability"), set `type="button"`.

### Mistake 6: Not using `<fieldset>` for grouped fields

Long forms without grouping are overwhelming. Use `<fieldset>` + `<legend>` to organize.

### Mistake 7: Forgetting `enctype` for file uploads

If your form has `<input type="file">`, the `<form>` must have `enctype="multipart/form-data"`.

---

## Summary

- The `<form>` element wraps form fields. Set `action` and `method`.
- Always pair `<label for="...">` with `<input id="...">`. Critical for accessibility.
- The `<input>` element has many types: text, email, password, number, date, color, range, checkbox, radio, file, hidden, submit, etc.
- Use `<textarea>` for multi-line text.
- Use `<select>` with `<option>` for dropdowns; use `<optgroup>` for groups.
- Use `<button type="submit|reset|button">` for buttons.
- Use `<fieldset>` with `<legend>` to group related fields.
- Validation attributes: `required`, `min`, `max`, `minlength`, `maxlength`, `pattern`.
- Use `placeholder` for hints, never as a replacement for labels.

Forms are one of the most important topics in HTML. Take your time with this lesson — re-read it, build every example, do every exercise. In the next lesson, we will look at two generic container elements: `<div>` and `<span>`.

---

**Next:** [Lesson 10: `<div>` and `<span>` →](./10-div-and-span.md)
