# Lesson 09: Backgrounds

> Module: CSS · Lesson 9 of 16
> Estimated time: 45–60 minutes

Backgrounds give life to your page. With CSS, you can set background colors, background images, gradients, control how they repeat, position them, and even use multiple backgrounds at once. In this lesson, you'll learn all of it.

---

## Learning Objectives

After this lesson, you will be able to:

1. Set background colors with `background-color`.
2. Set background images with `background-image`.
3. Control repetition with `background-repeat`.
4. Position backgrounds with `background-position`.
5. Size backgrounds with `background-size`.
6. Use the `background` shorthand.
7. Layer multiple backgrounds.
8. Use linear, radial, and conic gradients.
9. Apply backgrounds with `background-clip` and `background-origin`.

---

## 1. Background Color — `background-color`

The simplest background is a solid color:

```css
.box {
  background-color: #2c3e50;
  color: white;
  padding: 20px;
}
```

Color values can be named, hex, RGB, RGBA, HSL, HSLA — anything you learned in Lesson 04.

### The Shorthand `background`

You can also write `background: #2c3e50;`. This is the shorthand that resets all background properties and sets the color. Use `background-color` when you only want to change the color without affecting other background properties.

```css
.box { background-color: #2c3e50; }   /* changes only color */
.box { background: #2c3e50; }          /* resets everything, sets color */
```

---

## 2. Background Image — `background-image`

```css
.hero {
  background-image: url("hero.jpg");
}
```

The image fills the background. By default, it tiles (repeats) to fill the entire element. To control this, use `background-repeat`.

### URL Syntax

```css
background-image: url("hero.jpg");          /* same folder as CSS */
background-image: url("images/bg.jpg");     /* subfolder */
background-image: url("../images/bg.jpg");  /* parent folder */
background-image: url("https://example.com/bg.jpg");  /* absolute URL */
```

Always quote the URL — it works without quotes for simple URLs, but quoting is safer.

---

## 3. Background Repeat — `background-repeat`

```css
.box { background-repeat: repeat; }     /* default - tiles both directions */
.box { background-repeat: repeat-x; }    /* horizontal only */
.box { background-repeat: repeat-y; }    /* vertical only */
.box { background-repeat: no-repeat; }   /* show once */
.box { background-repeat: space; }       /* tiles without clipping, with even spacing */
.box { background-repeat: round; }      /* tiles, scaling to fit whole copies */
```

### Most Common Patterns

**Single image, no repeat (like a hero photo):**

```css
.hero {
  background-image: url("hero.jpg");
  background-repeat: no-repeat;
  background-size: cover;
}
```

**Horizontal repeat (like a striped background):**

```css
.stripes {
  background-image: url("stripe.png");
  background-repeat: repeat-x;
}
```

**Vertical pattern (like a sidebar):**

```css
.sidebar {
  background-image: url("pattern.png");
  background-repeat: repeat-y;
}
```

---

## 4. Background Position — `background-position`

Where to place the background image within the element (only matters if not repeating).

```css
.box { background-position: center; }            /* centered */
.box { background-position: top left; }         /* top-left corner */
.box { background-position: bottom right; }     /* bottom-right */
.box { background-position: 50% 50%; }          /* 50% from top, 50% from left = center */
.box { background-position: 20px 30px; }         /* 20px from left, 30px from top */
.box { background-position: right 20px center; } /* 20px from right, vertically centered */
```

### Keywords

`top`, `center`, `bottom` for vertical. `left`, `center`, `right` for horizontal.

### Percentages

`0%` = top/left, `50%` = center, `100%` = bottom/right.

### Pixel Values

Offset from top-left corner.

### Three-Value Syntax

```css
.box { background-position: right 20px center; }
/* anchor: right, then offset 20px from right, vertically centered */
```

---

## 5. Background Size — `background-size`

Controls how big the background image is.

```css
.box { background-size: 200px 100px; }   /* exact width 200, height 100 */
.box { background-size: 50% 50%; }        /* 50% of element's width, 50% of height */
.box { background-size: cover; }          /* scales to cover entire element, may crop */
.box { background-size: contain; }        /* scales to fit entirely, may show background */
```

### `cover` vs `contain`

- **`cover`** — the image fills the entire element. The image keeps its aspect ratio, but parts may be cropped if the element's aspect ratio differs.
- **`contain`** — the entire image is visible. The image keeps its aspect ratio, but there may be empty space (background-color visible) if the element's aspect ratio differs.

For hero images and full-width banners: use `cover`.
For logos and avatars: use `contain`.

---

## 6. Background Attachment — `background-attachment`

```css
.hero {
  background-attachment: scroll;  /* default - scrolls with page */
  background-attachment: fixed;    /* stays in place while content scrolls */
  background-attachment: local;    /* scrolls with the element's content */
}
```

### The "Parallax" Effect

`background-attachment: fixed` creates a parallax-like effect — the background appears to stay still while the page scrolls. Use sparingly — it can hurt performance on mobile.

---

## 7. The `background` Shorthand

You can set all background properties at once:

```css
.hero {
  background: #2c3e50 url("hero.jpg") no-repeat center / cover;
}
```

Order: `color image repeat attachment position / size`. The `/` separates position from size.

```css
background: <color> <image> <repeat> <attachment> <position> / <size>;
```

All parts are optional. Examples:

```css
background: #fff;
background: url("bg.jpg") no-repeat;
background: #2c3e50 url("hero.jpg") no-repeat center / cover fixed;
background: linear-gradient(to right, red, blue);
```

Use the shorthand when you want to set many properties at once. Use individual properties when you only want to change one.

---

## 8. Multiple Backgrounds

You can layer multiple backgrounds:

```css
.box {
  background:
    url("logo.png") no-repeat center,
    linear-gradient(to bottom, #fff, #eee) repeat;
}
```

Each layer is separated by a comma. The first layer is on top; later layers are below. The bottom layer can be a color (no `url()`).

### Real Example: Hero with Image and Overlay

```css
.hero {
  background:
    linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)),  /* dark overlay */
    url("hero.jpg") center / cover;                      /* photo */
  color: white;
  padding: 100px 20px;
  text-align: center;
}
```

The gradient sits on top of the photo, creating a darkening overlay. Text on top is readable.

---

## 9. Gradients

Gradients are a special kind of background image generated by CSS.

### Linear Gradients

```css
.box { background: linear-gradient(to right, red, blue); }
.box { background: linear-gradient(to bottom, red, blue); }
.box { background: linear-gradient(45deg, red, blue); }
.box { background: linear-gradient(135deg, red, orange, yellow); }
```

Direction can be:
- A keyword: `to right`, `to left`, `to top`, `to bottom`, `to top right`, `to bottom left`, etc.
- An angle: `45deg`, `180deg`, `0deg` (pointing up), etc.

Multiple colors create a smooth transition through all of them.

### Color Stops

Specify where each color stops:

```css
.box {
  background: linear-gradient(to right, red 0%, orange 25%, yellow 50%, green 75%, blue 100%);
}
```

Or use pixels:

```css
.box {
  background: linear-gradient(to right, red 0px, blue 100px);
}
```

### Hard Stops (No Transition)

Use the same position for two colors:

```css
.box {
  background: linear-gradient(to right, red 50%, blue 50%);
}
```

The transition is instant — solid red half, solid blue half.

### Radial Gradients

```css
.box { background: radial-gradient(circle, white, black); }
.box { background: radial-gradient(circle at top left, white, black); }
.box { background: radial-gradient(ellipse, red, blue); }
.box { background: radial-gradient(circle 100px, red, blue); }
```

Color radiates outward from a center point.

### Conic Gradients

```css
.pie {
  background: conic-gradient(red 0deg 90deg, yellow 90deg 180deg, green 180deg 270deg, blue 270deg 360deg);
}
```

Color sweeps around a circle. Useful for pie charts and color wheels.

### Repeating Gradients

```css
.stripes {
  background: repeating-linear-gradient(
    45deg,
    #fff 0px,
    #fff 10px,
    #eee 10px,
    #eee 20px
  );
}
```

Creates a striped pattern that repeats infinitely.

---

## 10. Background Clip — `background-clip`

Controls how far the background extends.

```css
.box {
  background-clip: border-box;    /* extends under the border (default) */
  background-clip: padding-box;   /* extends to the inside of the border */
  background-clip: content-box;   /* only on the content area */
  background-clip: text;          /* clips to the text - shows background through text! */
}
```

### Text Background Effect

```css
h1 {
  background: linear-gradient(to right, red, blue);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  -webkit-text-fill-color: transparent;
}
```

This fills text with a gradient. The `-webkit-` prefix is needed for some browsers.

---

## 11. Background Origin — `background-origin`

Where the background's coordinate system starts:

```css
.box {
  background-origin: padding-box;  /* default - from padding edge */
  background-origin: border-box;   /* from border edge */
  background-origin: content-box;  /* from content edge */
}
```

Useful when positioning backgrounds precisely.

---

## 12. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Backgrounds Demo</title>
    <link rel="stylesheet" href="backgrounds.css">
  </head>
  <body>
    <section class="hero">
      <h1>Welcome to My Site</h1>
      <p>A beautiful hero with overlay.</p>
    </section>

    <section class="content">
      <h2>Gradient Cards</h2>
      <div class="cards">
        <div class="card gradient-1">Card 1</div>
        <div class="card gradient-2">Card 2</div>
        <div class="card gradient-3">Card 3</div>
      </div>

      <h2>Stripes</h2>
      <div class="stripes">Diagonal stripes</div>

      <h2>Text with Gradient</h2>
      <h1 class="gradient-text">Gradient Text</h1>
    </section>
  </body>
</html>
```

```css
/* backgrounds.css */

* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: Arial, sans-serif;
  background: #f8f9fa;
  color: #333;
}

.hero {
  background:
    linear-gradient(rgba(44, 62, 80, 0.7), rgba(44, 62, 80, 0.7)),
    url("https://images.unsplash.com/photo-1503264116259-4a1f8e8d8c1e") center / cover;
  /* Replace the URL above with any image */
  background:
    linear-gradient(rgba(44, 62, 80, 0.7), rgba(44, 62, 80, 0.7)),
    linear-gradient(135deg, #667eea, #764ba2);
  color: white;
  text-align: center;
  padding: 80px 20px;
}

.hero h1 { font-size: 3rem; margin-bottom: 10px; }
.hero p { font-size: 1.2rem; opacity: 0.9; }

.content {
  max-width: 900px;
  margin: 40px auto;
  padding: 0 20px;
}

h2 { margin: 30px 0 15px; color: #2c3e50; }

.cards {
  display: flex;
  gap: 15px;
  margin-bottom: 20px;
}

.card {
  flex: 1;
  padding: 30px;
  border-radius: 8px;
  color: white;
  text-align: center;
  font-weight: bold;
  font-size: 1.2rem;
}

.gradient-1 {
  background: linear-gradient(135deg, #667eea, #764ba2);
}

.gradient-2 {
  background: linear-gradient(135deg, #f093fb, #f5576c);
}

.gradient-3 {
  background: linear-gradient(135deg, #4facfe, #00f2fe);
}

.stripes {
  background: repeating-linear-gradient(
    45deg,
    #fff,
    #fff 15px,
    #f0f0f0 15px,
    #f0f0f0 30px
  );
  padding: 30px;
  border: 1px solid #ddd;
  border-radius: 8px;
  text-align: center;
  color: #555;
}

.gradient-text {
  background: linear-gradient(to right, #667eea, #764ba2, #f093fb);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  color: transparent;
  font-size: 3rem;
  text-align: center;
}
```

Save and open. Note the hero uses a gradient as a stand-in for an image (replace the URL with any image). The cards use three different gradients. The stripes use a repeating gradient. The heading uses `background-clip: text` for gradient text.

---

## Try It Yourself

### Exercise 1: Hero Section

Build a hero section with a background image and a dark overlay. Make the text white and centered. Use `background-size: cover` so the image fills the section.

### Exercise 2: Gradient Buttons

Build three buttons, each with a different gradient background. Add a hover effect that brightens the gradient.

### Exercise 3: Striped Pattern

Use `repeating-linear-gradient` to create:
- Vertical stripes (alternating two colors).
- Diagonal stripes at 45 degrees.
- A checkerboard pattern.

### Exercise 4: Gradient Text

Make an `<h1>` with text filled by a gradient using `background-clip: text`.

---

## Common Mistakes

### Mistake 1: Forgetting `no-repeat` on background images

```css
/* BAD: image tiles by default */
.hero { background-image: url("hero.jpg"); }
```

For single hero images, always set `background-repeat: no-repeat`. Otherwise, the image tiles across the entire element.

### Mistake 2: Wrong path to image

```css
/* Path relative to the CSS file, not the HTML */
background-image: url("images/bg.jpg");
```

If your CSS is in `css/style.css` and the image is at `images/bg.jpg`, the path is `../images/bg.jpg` (up one folder, then into images).

### Mistake 3: Forgetting `background-size` for full-bleed images

```css
/* Image may not fill the element */
.hero { background: url("hero.jpg") no-repeat center; }
```

Without `background-size: cover`, the image renders at its natural size, which may be too small or too large. Always use `cover` for full-bleed backgrounds.

### Mistake 4: Trying to make text color = background gradient

```css
/* WRONG: color doesn't take gradients */
h1 {
  color: linear-gradient(red, blue);
}
```

`color` accepts only solid colors. For gradient text, use `background-clip: text` with `color: transparent`.

### Mistake 5: `background` shorthand resetting properties

```css
.hero { background-image: url("hero.jpg"); background-size: cover; }
.hero { background: #2c3e50; }   /* Oops — this resets background-image to none */
```

The `background` shorthand resets ALL background properties. Use `background-color` to change only the color, or use the full shorthand.

### Mistake 6: Forgetting `no-repeat` for icon backgrounds

If you use an icon as a background image on a button, it tiles by default. Always set `no-repeat`.

### Mistake 7: `cover` cropping content

For hero images where part of the image must be visible, `cover` may crop important parts. Use `background-position` to control which part stays visible:

```css
.hero {
  background: url("hero.jpg") center top / cover no-repeat;
}
```

---

## Summary

- `background-color` sets a solid color.
- `background-image: url(...)` sets an image.
- `background-repeat` controls tiling: `repeat`, `repeat-x`, `repeat-y`, `no-repeat`.
- `background-position` places the image: `center`, `top left`, `50% 50%`, `20px 30px`.
- `background-size` scales the image: `cover` (fills, may crop), `contain` (fits, may show empty space).
- `background-attachment: fixed` creates a parallax effect.
- Use the `background` shorthand to set everything at once.
- Multiple backgrounds: separate layers with commas; first layer on top.
- Gradients: `linear-gradient`, `radial-gradient`, `conic-gradient`, `repeating-linear-gradient`.
- `background-clip: text` shows backgrounds through text (with `color: transparent`).
- `background-clip` controls how far background extends (border-box, padding-box, content-box, text).

In the next lesson, we'll cover the `display` property — the master switch that controls how elements flow on the page.

---

**Next:** [Lesson 10: The `display` Property →](./10-display-property.md)
