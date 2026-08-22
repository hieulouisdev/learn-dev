# Lesson 04: Colors

> Module: CSS · Lesson 4 of 16
> Estimated time: 30–45 minutes

Color is one of the first things you'll want to control in CSS, and one of the most fun to play with. In this lesson, you'll learn every way to specify a color in CSS, when to use each one, and how to choose colors that look great together.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use named colors, hex, RGB, RGBA, HSL, and HSLA.
2. Pick the right color format for each situation.
3. Control opacity with alpha channels.
4. Use `currentColor` and CSS custom properties for theming.
5. Choose color palettes that look professional.

---

## 1. Named Colors

CSS includes 140+ named colors. They're friendly but limited:

```css
body { background: white; color: black; }
h1   { color: navy; }
a    { color: cornflowerblue; }
button { background: tomato; color: white; }
```

Some common named colors: `red`, `green`, `blue`, `white`, `black`, `gray`, `silver`, `navy`, `maroon`, `teal`, `olive`, `purple`, `yellow`, `orange`, `pink`, `cyan`, `magenta`, `brown`, `coral`, `salmon`, `tomato`, `gold`.

### When to Use Named Colors

- Quick prototypes and demos.
- Common conventions like `red`, `green`, `white`, `black`.

### When Not to Use Named Colors

- Production code — you want consistent, brand-specific colors that don't have friendly names.
- When you need opacity or specific shades.

You can find the full list at [Wikipedia: Web colors](https://en.wikipedia.org/wiki/Web_colors) or [MDN: color](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value).

---

## 2. Hexadecimal (Hex) — The Web Standard

Hex colors use `#` followed by 6 hex digits (0-9, A-F):

```css
h1 { color: #2c3e50; }
p { color: #444; }
.bg { background: #ffffff; }  /* white */
```

Each pair of digits represents red, green, and blue, in that order:

```
#RRGGBB
  R = red intensity (00 = none, FF = full)
  G = green intensity
  B = blue intensity
```

So `#FF0000` is pure red (full red, no green, no blue). `#00FF00` is pure green. `#0000FF` is pure blue. `#FFFFFF` is white (all colors full). `#000000` is black (no colors).

### Shorthand Hex

If all three pairs have repeated digits, you can use 3-digit shorthand:

```css
#FFFFFF  →  #FFF
#000000  →  #000
#AABBCC  →  #ABC
#112233  →  #123
```

`#FFF` is the same as `#FFFFFF`. Both render white.

### 8-Digit Hex (with Alpha)

You can add a 4th pair for alpha (opacity):

```css
.bg { background: #FF000080; }  /* red at 50% opacity */
```

The alpha pair works the same way: `00` = fully transparent, `FF` = fully opaque.

### When to Use Hex

- Production code (the standard).
- When you want concise, copy-pasteable colors.
- When working with design tools (they output hex by default).

### When Not to Use Hex

- When you need to programmatically adjust lightness or hue (use HSL instead).
- When you need to make a color slightly transparent (use RGBA or HSLA — easier to read).

---

## 3. RGB and RGBA

RGB specifies red, green, and blue as numbers 0–255:

```css
h1 { color: rgb(44, 62, 80); }   /* same as #2c3e50 */
p { color: rgb(255, 0, 0); }     /* pure red */
```

Each number is the intensity of that color channel from 0 (none) to 255 (full).

### RGBA — With Alpha

Add a 4th value for opacity from `0` (transparent) to `1` (opaque):

```css
.overlay { background: rgba(0, 0, 0, 0.5); }  /* 50% black overlay */
```

`0.5` means 50% opacity. `0.8` means 80%. `0` means fully transparent. `1` means fully opaque.

### Modern RGB Syntax (CSS Color Level 4)

Modern CSS allows spaces instead of commas:

```css
h1 { color: rgb(44 62 80); }
.overlay { background: rgb(0 0 0 / 0.5); }  /* with alpha, separated by / */
```

This syntax is more readable but may not work in very old browsers.

### When to Use RGBA

- When you need a transparent color.
- When generating colors programmatically (easier to think in 0-255 than in hex).
- When you want a hover state that's slightly transparent.

---

## 4. HSL and HSLA — The Most Intuitive

HSL stands for **Hue, Saturation, Lightness**. It's the most human-friendly color format.

```css
h1 { color: hsl(210, 50%, 40%); }
```

- **Hue** — the color itself, on a 0-360 degree color wheel. 0 = red, 120 = green, 240 = blue, 360 = red again.
- **Saturation** — how vivid the color is. 0% = gray, 100% = pure color.
- **Lightness** — how light or dark. 0% = black, 50% = normal, 100% = white.

### The Hue Wheel

Common hue values:

| Hue | Color |
|-----|-------|
| 0 | Red |
| 30 | Orange |
| 60 | Yellow |
| 90 | Yellow-green |
| 120 | Green |
| 150 | Teal |
| 180 | Cyan |
| 210 | Sky blue |
| 240 | Blue |
| 270 | Indigo |
| 300 | Magenta |
| 330 | Pink |
| 360 | Red (back to 0) |

### HSLA — With Alpha

```css
.overlay { background: hsla(0, 100%, 50%, 0.5); }  /* semi-transparent red */
```

Or in modern syntax:

```css
.overlay { background: hsl(0 100% 50% / 0.5); }
```

### Why HSL is Best for Designers

HSL makes it trivial to:

- **Lighten a color**: increase lightness. `hsl(210, 50%, 40%)` → `hsl(210, 50%, 60%)`.
- **Darken a color**: decrease lightness.
- **Make a palette**: keep hue and saturation constant, vary lightness. All colors look cohesive.
- **Make a complementary color**: add 180 to hue.

Hex and RGB don't have such intuitive operations. Use HSL when designing color systems.

### Example Palette Using HSL

```css
:root {
  --primary-900: hsl(210, 60%, 20%);  /* darkest */
  --primary-700: hsl(210, 60%, 30%);
  --primary-500: hsl(210, 60%, 50%);  /* base */
  --primary-300: hsl(210, 60%, 70%);
  --primary-100: hsl(210, 60%, 90%);  /* lightest */
}
```

All five shades share the same hue and saturation — they look like a family. This is how professional design systems work.

---

## 5. `currentColor`

`currentColor` is a special keyword that means "use the value of the `color` property from this element":

```css
.card {
  color: navy;
  border: 1px solid currentColor;  /* navy border */
}

.card .icon {
  fill: currentColor;  /* SVG icons inherit navy */
}
```

Why is this useful? Because changing `color` automatically updates `currentColor` everywhere it's used. Set the color once, and borders, icons, and accents all follow.

---

## 6. CSS Custom Properties (Variables)

You can define your own color variables:

```css
:root {
  --brand-blue: #2c3e50;
  --brand-accent: #e74c3c;
  --text-color: #333;
  --background: #f8f9fa;
}

body {
  background: var(--background);
  color: var(--text-color);
}

h1 { color: var(--brand-blue); }
a { color: var(--brand-accent); }
```

Define once, use everywhere. Change one variable, the whole theme updates. We'll cover custom properties in depth later, but you should start using them for colors from day one.

---

## 7. Gradient Backgrounds

You can use gradients as background images:

### Linear Gradient

```css
.gradient {
  background: linear-gradient(to right, #2c3e50, #4ca1af);
}
```

- `to right` — direction (also: `to left`, `to top`, `to bottom`, `to bottom right`, angles like `45deg`).
- Then two or more color stops.

Multiple colors:

```css
.rainbow {
  background: linear-gradient(to right, red, orange, yellow, green, blue, indigo, violet);
}
```

### Radial Gradient

```css
.radial {
  background: radial-gradient(circle, white, black);
}
```

Color radiates outward from a center point.

### Conic Gradient

```css
.pie {
  background: conic-gradient(red 0% 25%, yellow 25% 50%, green 50% 75%, blue 75% 100%);
}
```

Color sweeps around a circle.

---

## 8. Color Properties Cheat Sheet

| Property | What It Sets |
|---------|--------------|
| `color` | Text color |
| `background-color` | Element's background color |
| `background` | Shorthand for background (incl. images, gradients) |
| `border-color` | Border color |
| `border` | Shorthand for border (incl. color) |
| `outline-color` | Outline color |
| `box-shadow` | Shadow color (rgba recommended) |
| `text-shadow` | Shadow under text |
| `fill` | SVG fill color |
| `stroke` | SVG stroke color |
| `accent-color` | Form controls accent (checkbox, radio) |
| `caret-color` | Text cursor color in inputs |

---

## 9. Choosing a Color Palette

A good palette has 5-7 colors:

- 1 primary color (the brand).
- 1-2 secondary colors (accents).
- 4-5 neutral shades (white, off-white, gray, dark gray, near-black).
- 1 success, 1 warning, 1 danger (for status indicators).

### Sample Palette (Professional Blue)

```css
:root {
  /* Primary */
  --primary: #2c3e50;
  --primary-light: #34495e;
  --primary-dark: #1a252f;

  /* Accents */
  --accent: #3498db;
  --accent-light: #5dade2;

  /* Neutrals */
  --white: #ffffff;
  --gray-100: #f8f9fa;
  --gray-300: #dee2e6;
  --gray-500: #adb5bd;
  --gray-700: #495057;
  --gray-900: #212529;

  /* Status */
  --success: #2ecc71;
  --warning: #f39c12;
  --danger: #e74c3c;
}
```

### Free Color Tools

- [coolors.co](https://coolors.co) — generate palettes.
- [paletton.com](http://paletton.com) — color scheme designer.
- [material.io/design/color](https://material.io/design/color) — Material Design colors.
- [Tailwind Colors](https://tailwindcss.com/docs/customizing-colors) — ready-to-use palette.

---

## 10. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Colors Demo</title>
    <link rel="stylesheet" href="colors.css">
  </head>
  <body>
    <header>
      <h1>Color Demo</h1>
      <p>Every way to specify a color in CSS.</p>
    </header>

    <main>
      <section class="named">
        <h2>Named Colors</h2>
        <div class="box red">Red</div>
        <div class="box cornflower">Cornflowerblue</div>
        <div class="box tomato">Tomato</div>
      </section>

      <section class="hex">
        <h2>Hex</h2>
        <div class="box hex1">#2c3e50</div>
        <div class="box hex2">#e74c3c</div>
        <div class="box hex3">#2ecc71</div>
      </section>

      <section class="rgb">
        <h2>RGB / RGBA</h2>
        <div class="box rgb1">rgb(155, 89, 182)</div>
        <div class="box rgb2">rgba(52, 152, 219, 0.5)</div>
      </section>

      <section class="hsl">
        <h2>HSL / HSLA</h2>
        <div class="box hsl1">hsl(120, 60%, 50%)</div>
        <div class="box hsl2">hsla(0, 100%, 50%, 0.4)</div>
      </section>

      <section class="gradient">
        <h2>Gradients</h2>
        <div class="box linear">linear-gradient</div>
        <div class="box radial">radial-gradient</div>
      </section>
    </main>
  </body>
</html>
```

```css
/* colors.css */
:root {
  --primary: #2c3e50;
  --gray: #95a5a6;
}

body {
  font-family: Arial, sans-serif;
  background: var(--gray-100, #f8f9fa);
  color: var(--primary);
  padding: 30px;
}

.box {
  display: inline-block;
  width: 150px;
  height: 80px;
  margin: 10px;
  text-align: center;
  line-height: 80px;
  color: white;
  font-weight: bold;
  border-radius: 8px;
}

.named .red { background: red; }
.named .cornflower { background: cornflowerblue; }
.named .tomato { background: tomato; }

.hex .hex1 { background: #2c3e50; }
.hex .hex2 { background: #e74c3c; }
.hex .hex3 { background: #2ecc71; }

.rgb .rgb1 { background: rgb(155, 89, 182); }
.rgb .rgb2 { background: rgba(52, 152, 219, 0.5); }

.hsl .hsl1 { background: hsl(120, 60%, 50%); }
.hsl .hsl2 { background: hsla(0, 100%, 50%, 0.4); }

.gradient .linear {
  background: linear-gradient(to right, #2c3e50, #4ca1af);
}

.gradient .radial {
  background: radial-gradient(circle, white, #2c3e50);
  color: white;
}
```

Save both files and open in a browser. You'll see all the color formats rendered side by side.

---

## Try It Yourself

### Exercise 1: HSL Palette

Pick any hue (e.g., 210 for blue). Build a palette with 5 shades: 10%, 30%, 50%, 70%, 90% lightness. Apply each to a different `<div>` as background.

### Exercise 2: Convert Formats

Convert these colors between formats:

- `#FF0000` → RGB → HSL
- `rgb(44, 62, 80)` → Hex → HSL
- `hsl(0, 100%, 50%)` → Hex → RGB

Use an online converter like [rgb.to](http://rgb.to) to check.

### Exercise 3: Build a Theme

Define CSS variables for a theme: primary, secondary, accent, four neutrals, success, warning, danger. Apply them to a sample page with headers, paragraphs, and buttons.

---

## Common Mistakes

### Mistake 1: Forgetting the `#` in hex

```css
/* WRONG */
h1 { color: 2c3e50; }
```

The browser treats `2c3e50` as an unknown color name. Always include `#`.

### Mistake 2: Wrong commas in RGB

```css
/* WRONG */
h1 { color: rgb(255 0 0); }   /* old syntax - missing commas */
```

In classic RGB syntax, values are comma-separated: `rgb(255, 0, 0)`. The space-separated modern syntax works in newer browsers.

### Mistake 3: Using HSL percentages without units

```css
/* WRONG */
h1 { color: hsl(210, 50, 40); }   /* 50 instead of 50% */
```

Saturation and lightness must include `%`. Only hue is a bare number.

### Mistake 4: Alpha > 1 or < 0

```css
/* WRONG */
h1 { color: rgba(255, 0, 0, 1.5); }
```

Alpha must be between 0 (transparent) and 1 (opaque).

### Mistake 5: Forgetting opacity vs. transparency

- `alpha: 1` = fully opaque (you can't see through it).
- `alpha: 0` = fully transparent (invisible).
- `alpha: 0.5` = 50% opacity (you can see through it).

Beginners sometimes confuse these — high alpha means **more** opaque, not less.

### Mistake 6: Hardcoded colors everywhere

Don't repeat the same hex code 50 times. Use CSS variables. Change one variable, all instances update.

---

## Summary

- Six color formats: named, hex (`#RRGGBB`), RGB (`rgb(r, g, b)`), RGBA (`rgba(r, g, b, a)`), HSL (`hsl(h, s%, l%)`), HSLA.
- **Hex** is the production standard (concise, widely used by design tools).
- **HSL** is the designer's choice (intuitive for building palettes).
- **RGBA / HSLA** add transparency (alpha 0 to 1).
- Use `currentColor` to inherit the text color.
- Use CSS custom properties (`--name: value; ... var(--name)`) for theming.
- Gradients can be linear, radial, or conic.
- Build palettes with 5-7 colors: primary, secondary, neutrals, status colors.

In the next lesson, we'll dive into typography — fonts, sizes, weights, and how to make text look beautiful.

---

**Next:** [Lesson 05: Text and Fonts →](./05-text-and-fonts.md)
