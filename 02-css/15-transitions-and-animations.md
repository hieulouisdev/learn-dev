# Lesson 15: Transitions and Animations

> Module: CSS · Lesson 15 of 16
> Estimated time: 45–60 minutes

CSS can animate elements — smoothly transition colors, sizes, positions, and more. In this lesson, you'll learn about **transitions** (smooth changes between states) and **animations** (multi-step keyframe sequences).

---

## Learning Objectives

After this lesson, you will be able to:

1. Use the `transition` shorthand and individual properties.
2. Choose the right easing function (`ease`, `linear`, `ease-in`, etc.).
3. Create keyframe animations with `@keyframes`.
4. Use `transform`, `opacity`, and other performant properties.
5. Build common UI animations: hover effects, modals, loading spinners.
6. Respect user preferences with `prefers-reduced-motion`.

---

## 1. Transitions vs. Animations

- **Transition**: smoothly interpolates between two states when a property changes. Triggered by `:hover`, `:focus`, class changes, etc. One start state, one end state.
- **Animation**: a sequence of keyframes that plays over time. Can loop, pause, reverse, and have multiple stops.

Use transitions for hover effects and state changes. Use animations for things that should play independently — loading spinners, attention-grabbing effects.

---

## 2. The `transition` Property

```css
.btn {
  background: #3498db;
  transition: background 0.3s ease;
}

.btn:hover { background: #2980b9; }
```

When the user hovers, the background color smoothly changes from `#3498db` to `#2980b9` over 0.3 seconds.

### The Shorthand

```css
transition: <property> <duration> <timing-function> <delay>;
```

```css
transition: background 0.3s ease;
transition: background 0.3s ease 0.1s;          /* with 0.1s delay */
transition: background 0.3s, color 0.2s;        /* multiple properties */
transition: all 0.3s ease;                       /* all animatable properties */
```

### Individual Properties

```css
transition-property: background, color, transform;
transition-duration: 0.3s, 0.2s, 0.4s;
transition-timing-function: ease, ease-in, ease-out;
transition-delay: 0s, 0.1s, 0.2s;
```

Multiple values are comma-separated and align by position.

---

## 3. Timing Functions

```css
transition-timing-function: linear;        /* constant speed */
transition-timing-function: ease;           /* default - slow start, fast middle, slow end */
transition-timing-function: ease-in;        /* slow start */
transition-timing-function: ease-out;       /* slow end - looks natural */
transition-timing-function: ease-in-out;    /* slow start and end */
transition-timing-function: cubic-bezier(0.25, 0.1, 0.25, 1);  /* custom curve */
```

### Which to Use?

- **`ease`**: good default for most things.
- **`ease-out`**: best for things appearing (buttons growing, modals opening).
- **`ease-in`**: best for things disappearing (rare).
- **`linear`**: only for things that should feel mechanical (spinners, progress bars).
- **`cubic-bezier`**: custom curves for unique feels.

### Bezier Curves

You can generate custom curves with online tools like [cubic-bezier.com](https://cubic-bezier.com/).

---

## 4. What Can Be Animated?

Not all CSS properties can be smoothly animated. Two categories:

### Performant Properties (Animate These)

These don't trigger re-layout, so they're butter-smooth:

- `transform` — `translate()`, `scale()`, `rotate()`, `skew()`
- `opacity` — fades in/out
- `filter` — `blur`, `brightness`, `grayscale`

### Layout-Triggering Properties (Use Sparingly)

These cause the browser to recalculate layout, which can be janky:

- `width`, `height`
- `margin`, `padding`
- `top`, `left`, `right`, `bottom`
- `font-size`

### Non-Animatable Properties

These can't be smoothly animated:

- `display`
- `position`
- Most `flex-*` and `grid-*` properties

For showing/hiding, animate `opacity` (with `visibility: hidden` to disable interaction) instead of `display: none`.

---

## 5. The `transform` Property

The most powerful animation property:

```css
.box:hover {
  transform: translate(10px, 0);   /* move 10px right */
  transform: translateX(10px);     /* same as above */
  transform: translateY(-10px);   /* move 10px up */
  transform: scale(1.1);           /* grow 10% */
  transform: scale(0.9);           /* shrink 10% */
  transform: rotate(45deg);        /* rotate 45 degrees */
  transform: skew(10deg, 5deg);    /* skew */
  transform: translate(10px, 0) scale(1.1) rotate(5deg);  /* combined */
}
```

`transform` doesn't affect layout — the element keeps its original space in the page. This makes it perfect for animations.

### Combining Transforms

```css
.card:hover {
  transform: translateY(-5px) scale(1.02);
  transition: transform 0.2s ease-out;
}
```

Lift the card slightly and grow it on hover.

---

## 6. Common Hover Effects

### Button Grow

```css
.btn { transition: transform 0.2s ease; }
.btn:hover { transform: scale(1.05); }
```

### Card Lift

```css
.card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px rgba(0,0,0,0.15);
}
```

### Color Fade

```css
.btn {
  background: #3498db;
  transition: background 0.3s ease;
}
.btn:hover { background: #2980b9; }
```

### Underline Slide-In

```css
.nav-link {
  position: relative;
  transition: color 0.3s ease;
}
.nav-link::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: -2px;
  width: 0;
  height: 2px;
  background: currentColor;
  transition: width 0.3s ease;
}
.nav-link:hover::after { width: 100%; }
```

---

## 7. Keyframe Animations

Multi-step animations with `@keyframes`:

```css
@keyframes spin {
  from { transform: rotate(0deg); }
  to   { transform: rotate(360deg); }
}

.spinner {
  animation: spin 1s linear infinite;
}
```

### Keyframe Syntax

```css
@keyframes name {
  0%   { ... }   /* same as 'from' */
  50%  { ... }
  100% { ... }   /* same as 'to' */
}

@keyframes name {
  from { ... }
  to   { ... }
}
```

You can use as many stops as you want (0%, 25%, 50%, 75%, 100%).

### The `animation` Shorthand

```css
animation: name duration timing-function delay iteration-count direction fill-mode play-state;
```

```css
animation: spin 1s linear infinite;
animation: bounce 0.5s ease-in-out 3;       /* 3 times */
animation: slide 2s ease-out 0.5s 1 forwards;  /* once, hold final state */
```

### Individual Properties

```css
animation-name: spin;
animation-duration: 1s;
animation-timing-function: linear;
animation-delay: 0s;
animation-iteration-count: infinite;   /* or a number */
animation-direction: normal;             /* or reverse, alternate, alternate-reverse */
animation-fill-mode: none;               /* or forwards, backwards, both */
animation-play-state: running;            /* or paused */
```

### `animation-fill-mode`

What happens before the animation starts and after it ends:

- `none` (default) — element returns to its original state.
- `forwards` — element keeps the final keyframe state.
- `backwards` — element takes on the first keyframe state immediately (during delay).
- `both` — applies both forwards and backwards.

```css
.fade-in {
  opacity: 0;
  animation: fadeIn 1s ease-out forwards;
}

@keyframes fadeIn {
  to { opacity: 1; }
}
```

### `animation-direction`

- `normal` — forward.
- `reverse` — backward.
- `alternate` — forward then backward (good for pulses).
- `alternate-reverse` — backward then forward.

---

## 8. Common Animations

### Loading Spinner

```css
@keyframes spin {
  to { transform: rotate(360deg); }
}

.spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #f0f0f0;
  border-top-color: #3498db;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}
```

### Bounce

```css
@keyframes bounce {
  0%, 20%, 50%, 80%, 100% { transform: translateY(0); }
  40% { transform: translateY(-30px); }
  60% { transform: translateY(-15px); }
}

.bounce { animation: bounce 2s ease-in-out infinite; }
```

### Pulse

```css
@keyframes pulse {
  0%, 100% { transform: scale(1); opacity: 1; }
  50% { transform: scale(1.1); opacity: 0.8; }
}

.pulse { animation: pulse 2s ease-in-out infinite; }
```

### Fade In

```css
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}

.fade-in {
  opacity: 0;
  animation: fadeIn 0.5s ease-out forwards;
}
```

### Slide In

```css
@keyframes slideIn {
  from { transform: translateX(-100%); }
  to   { transform: translateX(0); }
}

.slide-in { animation: slideIn 0.3s ease-out; }
```

### Shake (Error Effect)

```css
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  10%, 30%, 50%, 70%, 90% { transform: translateX(-5px); }
  20%, 40%, 60%, 80% { transform: translateX(5px); }
}

.shake { animation: shake 0.5s ease-in-out; }
```

---

## 9. Respecting `prefers-reduced-motion`

Some users have vestibular disorders. Honor their preference:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Including this snippet is a mark of a professional, accessible website.

---

## 10. A Complete Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Animation Demo</title>
    <link rel="stylesheet" href="animations.css">
  </head>
  <body>
    <h1>Animation Demo</h1>

    <h2>Hover Effects</h2>
    <div class="row">
      <button class="btn-grow">Grow</button>
      <button class="btn-color">Color</button>
      <button class="btn-underline">Underline</button>
    </div>

    <h2>Cards</h2>
    <div class="cards">
      <div class="card">Card 1</div>
      <div class="card">Card 2</div>
      <div class="card">Card 3</div>
    </div>

    <h2>Loading</h2>
    <div class="spinner"></div>

    <h2>Bounce</h2>
    <div class="bouncer"></div>

    <h2>Pulse</h2>
    <div class="pulser"></div>

    <h2>Fade In on Load</h2>
    <div class="fade-in">I appear smoothly!</div>
  </body>
</html>
```

```css
/* animations.css */

* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: Arial, sans-serif;
  padding: 30px;
  max-width: 900px;
  margin: 0 auto;
  line-height: 1.6;
  color: #333;
}

h1, h2 { color: #2c3e50; margin: 30px 0 15px; }
.row { display: flex; gap: 15px; margin-bottom: 30px; }

/* Button: grow on hover */
.btn-grow {
  padding: 10px 20px;
  background: #3498db;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1rem;
  transition: transform 0.2s ease;
}

.btn-grow:hover { transform: scale(1.1); }

/* Button: color fade */
.btn-color {
  padding: 10px 20px;
  background: #3498db;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1rem;
  transition: background 0.3s ease;
}

.btn-color:hover { background: #2980b9; }

/* Button: underline slide-in */
.btn-underline {
  padding: 10px 20px;
  background: white;
  color: #3498db;
  border: 2px solid #3498db;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1rem;
  position: relative;
  overflow: hidden;
}

.btn-underline::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 0;
  height: 100%;
  background: #3498db;
  transition: width 0.3s ease;
  z-index: -1;
}

.btn-underline:hover::after { width: 100%; }
.btn-underline:hover { color: white; }

/* Cards: lift on hover */
.cards {
  display: flex;
  gap: 20px;
  margin-bottom: 30px;
}

.card {
  flex: 1;
  background: white;
  padding: 30px;
  border: 1px solid #ddd;
  border-radius: 8px;
  text-align: center;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px rgba(0,0,0,0.1);
}

/* Spinner */
@keyframes spin {
  to { transform: rotate(360deg); }
}

.spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #f0f0f0;
  border-top-color: #3498db;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin: 20px 0;
}

/* Bounce */
@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50%      { transform: translateY(-30px); }
}

.bouncer {
  width: 40px;
  height: 40px;
  background: #e74c3c;
  border-radius: 50%;
  animation: bounce 1s ease-in-out infinite;
  margin: 20px 0;
}

/* Pulse */
@keyframes pulse {
  0%, 100% { transform: scale(1); opacity: 1; }
  50%      { transform: scale(1.2); opacity: 0.7; }
}

.pulser {
  width: 40px;
  height: 40px;
  background: #2ecc71;
  border-radius: 50%;
  animation: pulse 2s ease-in-out infinite;
  margin: 20px 0;
}

/* Fade in */
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}

.fade-in {
  padding: 20px;
  background: #f0f4f8;
  border-left: 4px solid #3498db;
  opacity: 0;
  animation: fadeInUp 0.6s ease-out forwards;
}

/* Respect reduced motion */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

Save and open. Try hovering the buttons and cards. Watch the spinner spin, the bouncer bounce, the pulse pulse, and the fade-in slide up.

---

## Try It Yourself

### Exercise 1: Hover Gallery

Build a photo gallery where each photo:
- Scales up slightly on hover.
- Shows a dark overlay with a caption.
- Has a smooth transition.

### Exercise 2: Loading Spinner

Build a button that shows a spinner when clicked (just CSS — no real loading). The spinner should be visible for 2 seconds, then hide.

### Exercise 3: Animated Card

Build a card that:
- Slides in from below on page load.
- Lifts up on hover.
- Has a shadow that grows on hover.

### Exercise 4: Respect Reduced Motion

Add the `prefers-reduced-motion` snippet to your CSS. Enable "Reduce motion" in your OS settings (System Settings > Accessibility > Display on macOS, Settings > Accessibility > Remove animations on Windows) and reload. Animations should stop.

---

## Common Mistakes

### Mistake 1: Forgetting the transition declaration

```css
/* BAD: no transition, jumps instantly */
.btn:hover { background: #2980b9; }
```

Without a `transition` declaration, the change is instant. Add `transition: background 0.3s ease;` on the base (not the hover) state.

### Mistake 2: Putting `transition` on `:hover`

```css
/* WRONG: transition only applies during hover, snap when leaving */
.btn:hover {
  background: #2980b9;
  transition: background 0.3s ease;
}
```

Put the `transition` on the **base state**, not `:hover`:

```css
.btn { transition: background 0.3s ease; }
.btn:hover { background: #2980b9; }
```

Now the transition plays both when hovering AND when leaving.

### Mistake 3: Animating layout-triggering properties

```css
/* BAD: animating width causes layout recalculation - janky */
.box:hover { width: 200px; transition: width 0.3s; }
```

Use `transform: scale(1.5)` instead. It doesn't trigger layout.

### Mistake 4: Overusing animations

Animations should be subtle and intentional. Too many animations make a page feel chaotic and amateur. Use animations to enhance, not to show off.

### Mistake 5: Animations that don't respect reduced motion

Always include the `prefers-reduced-motion` media query. Some users can't tolerate motion.

### Mistake 6: Forgetting `forwards` on fill-mode

```css
.fade-in { animation: fadeIn 0.5s ease-out; }
```

Without `forwards`, the element returns to its original state after the animation ends. If you want the final state to stick, use `forwards`:

```css
.fade-in { animation: fadeIn 0.5s ease-out forwards; }
```

### Mistake 7: Animating `display: none`

```css
/* WON'T WORK: display can't be animated */
.modal { display: none; transition: opacity 0.3s; }
.modal.open { display: block; opacity: 1; }
```

Use `opacity` + `visibility` for fade-in/out:

```css
.modal { opacity: 0; visibility: hidden; transition: opacity 0.3s, visibility 0.3s; }
.modal.open { opacity: 1; visibility: visible; }
```

---

## Summary

- **Transitions** smoothly interpolate between two states. Use for hover/focus effects.
- **Animations** play a sequence of keyframes. Use for independent motion like spinners.
- `transition: property duration timing-function delay`.
- `animation: name duration timing-function delay iteration direction fill-mode`.
- Timing functions: `ease`, `ease-in`, `ease-out`, `ease-in-out`, `linear`, `cubic-bezier(...)`.
- Animate performant properties (`transform`, `opacity`, `filter`) — avoid animating layout properties.
- Use `transform` for translate, scale, rotate, skew — doesn't trigger layout.
- Put `transition` on the **base state**, not the `:hover` state.
- `@keyframes name { from { ... } to { ... } }` or with percentages.
- Use `animation-fill-mode: forwards` to keep the final state.
- Respect `prefers-reduced-motion: reduce`.

In the next (and final) lesson, we'll create a printable cheatsheet of every CSS property you've learned.

---

**Next:** [Lesson 16: CSS Cheatsheet →](./16-css-cheatsheet.md)
