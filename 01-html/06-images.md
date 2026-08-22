# Lesson 06: Images

> Module: HTML · Lesson 6 of 13
> Estimated time: 30–45 minutes

A web page without images is like a book without illustrations — possible, but far less engaging. In this lesson, you will learn how to add images to a web page, how to make them accessible to all users (including those who cannot see them), how to handle image loading failures, and how to choose the right image format.

---

## Learning Objectives

After this lesson, you will be able to:

1. Add images to a web page using the `<img>` tag.
2. Write meaningful `alt` text for accessibility.
3. Control image size with `width` and `height` attributes.
4. Use the `<figure>` and `<figcaption>` elements for captioned images.
5. Choose the right image format (JPG, PNG, SVG, GIF, WebP).
6. Add lazy-loading and responsive images.

---

## 1. The `<img>` Tag

The `<img>` tag inserts an image into a page. It is a **void element** — it has no closing tag.

```html
<img src="cat.jpg" alt="A fluffy orange cat sleeping on a windowsill">
```

Two attributes are essential:

- **`src`** — the URL or path to the image file. Works just like the `href` of a link.
- **`alt`** — a short text description of the image, used by screen readers and shown if the image fails to load.

### The `src` Attribute

Like links, `src` can be:

- A **relative path**: `src="images/photo.jpg"` (an image in a folder).
- An **absolute path**: `src="https://example.com/photo.jpg"` (an image hosted elsewhere).
- A **data URL**: `src="data:image/png;base64,..."` (the image data embedded directly — used for tiny icons).

### The `alt` Attribute — Critical for Accessibility

The `alt` text serves three purposes:

1. **Screen readers** read it aloud to blind users.
2. The browser displays it if the image fails to load (broken network, missing file).
3. Search engines use it to understand what the image shows.

Always write meaningful `alt` text. A good test: if you could not see the image, would the `alt` text alone give you enough information?

```html
<!-- GOOD -->
<img src="chart.png" alt="Bar chart showing sales doubled from 2024 to 2026">

<!-- BAD -->
<img src="chart.png" alt="image">
<img src="chart.png" alt="chart">
```

### When to Use Empty `alt`

If an image is purely decorative (like a decorative background flourish) and carries no meaning, use an empty `alt`:

```html
<img src="decorative-line.png" alt="">
```

This tells screen readers "skip this image, it adds nothing."

**Never** omit the `alt` attribute entirely. Empty `alt` is different from no `alt` — empty means "decorative", missing means "I forgot."

---

## 2. Image Size: `width` and `height`

You can specify the image's dimensions in pixels:

```html
<img src="cat.jpg" alt="A cat" width="400" height="300">
```

Setting width and height has two benefits:

1. The browser reserves space for the image before it loads, preventing the page from "jumping" when images arrive.
2. The image renders at the specified size regardless of the original file dimensions.

If you set only one (e.g., just `width`), the other scales proportionally. If you set both, the image may be stretched — be careful.

### Modern Best Practice: Use CSS

For most cases, control image size with CSS instead:

```html
<img src="cat.jpg" alt="A cat" class="hero-image">
```

```css
.hero-image {
  width: 100%;
  max-width: 600px;
  height: auto;
}
```

This is more flexible because it lets the image scale responsively. We will learn CSS in the next module.

The HTML `width` and `height` attributes are still useful for **aspect-ratio reservation** — they prevent the page from jumping during load. The modern recommendation is to specify both in HTML for layout stability, then override with CSS if needed.

---

## 3. The `<figure>` and `<figcaption>` Elements

When an image needs a caption, wrap it in `<figure>` and use `<figcaption>`:

```html
<figure>
  <img src="architecture.jpg" alt="Modern glass building">
  <figcaption>The Louvre Pyramid in Paris, designed by I. M. Pei.</figcaption>
</figure>
```

`<figure>` groups an image with its caption. `<figcaption>` describes it. The browser styles these reasonably by default.

You can also use `<figure>` for other types of content — a code snippet, a video, a quote — anything that needs a caption.

```html
<figure>
  <blockquote>
    <p>The only way to do great work is to love what you do.</p>
  </blockquote>
  <figcaption>Steve Jobs, Stanford Commencement 2005</figcaption>
</figure>
```

---

## 4. Choosing the Right Image Format

Different formats are best for different types of images. Here is a cheat sheet.

| Format | Best For | Transparency | Animation | Compression |
|--------|----------|--------------|-----------|-------------|
| **JPG / JPEG** | Photos of real-world scenes (people, landscapes) | No | No | Lossy |
| **PNG** | Graphics, logos, screenshots, text-heavy images | Yes | No | Lossless |
| **SVG** | Logos, icons, simple illustrations that scale infinitely | Yes | Yes | Vector (no loss) |
| **GIF** | Short animations | Yes (limited) | Yes | Lossless |
| **WebP** | Modern, smaller version of JPG/PNG — supports all features | Yes | Yes | Lossy or lossless |

### Guidelines

- **Photographs**: use JPG or WebP.
- **Logos, icons, illustrations**: use SVG when possible (infinite scaling, tiny file size).
- **Screenshots**: use PNG.
- **Animated images**: use WebP or a video format (MP4 is smaller and more efficient than GIF).
- **Modern websites**: prefer WebP for everything photographic; provide JPG/PNG as fallback for old browsers.

### SVG — Special

SVG (Scalable Vector Graphics) is unique — it is not a pixel image but a vector image described by XML. You can scale an SVG to any size without quality loss. Best for logos, icons, and simple graphics.

```html
<img src="logo.svg" alt="Company logo" width="200" height="50">
```

Or embed SVG directly in HTML:

```html
<svg width="100" height="100" viewBox="0 0 100 100">
  <circle cx="50" cy="50" r="40" fill="red" />
</svg>
```

---

## 5. Lazy Loading

For pages with many images, you can delay loading images that are off-screen:

```html
<img src="large-photo.jpg" alt="..." loading="lazy">
```

The `loading="lazy"` attribute tells the browser: "don't download this image until the user scrolls near it." This dramatically improves initial page load on image-heavy pages like blogs.

Use `lazy` for images below the fold. Do not use `lazy` for the hero image at the top of the page — you want that to load immediately.

---

## 6. Responsive Images

Different screens need different image sizes — a phone doesn't need a 4K image. The `srcset` attribute lets you provide multiple sizes:

```html
<img
  src="photo-800.jpg"
  srcset="photo-400.jpg 400w, photo-800.jpg 800w, photo-1200.jpg 1200w"
  sizes="(max-width: 600px) 400px, (max-width: 1200px) 800px, 1200px"
  alt="A photo"
>
```

The browser picks the best image based on the screen size and pixel density. This saves bandwidth on mobile and improves load time.

This is an advanced feature. Don't worry about mastering it yet — most beginners start with a single `src` and learn responsive images later.

---

## 7. Image as a Link

Wrap an image in an `<a>` tag to make it clickable:

```html
<a href="https://example.com">
  <img src="banner.jpg" alt="Click to visit Example">
</a>
```

A blue border may appear around the image — you can remove it with CSS later.

---

## 8. A Complete Example

Create a folder called `images-demo` with this structure:

```
images-demo/
├── index.html
└── images/
    ├── cat.jpg
    ├── logo.svg
    └── chart.png
```

Create `index.html`:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Images Demo</title>
  </head>
  <body>
    <h1>Images Demo</h1>

    <h2>Basic Image</h2>
    <img src="images/cat.jpg" alt="A fluffy orange cat" width="400" height="300">

    <h2>Captioned Image</h2>
    <figure>
      <img src="images/chart.png" alt="Bar chart of sales growth" width="500">
      <figcaption>Quarterly sales growth from Q1 to Q4 2026.</figcaption>
    </figure>

    <h2>SVG Logo</h2>
    <img src="images/logo.svg" alt="Company logo" width="150">

    <h2>Lazy-Loaded Image</h2>
    <img src="images/cat.jpg" alt="Same cat, lazy loaded" loading="lazy" width="400" height="300">

    <h2>Inline SVG</h2>
    <svg width="100" height="100" viewBox="0 0 100 100">
      <circle cx="50" cy="50" r="40" fill="red" />
    </svg>

    <h2>Image as a Link</h2>
    <a href="https://en.wikipedia.org/wiki/Cat" target="_blank" rel="noopener noreferrer">
      <img src="images/cat.jpg" alt="Read about cats on Wikipedia" width="200">
    </a>
  </body>
</html>
```

You can use any image files you have — just rename them to match. Or use placeholder image services like [placehold.co](https://placehold.co) for testing.

---

## Try It Yourself

### Exercise 1: Captioned Photo Gallery

Create a page with three images, each in a `<figure>` with a `<figcaption>`. Use any photos you have on your computer.

### Exercise 2: Practice `alt` Text

Find an image online. Write three different versions of `alt` text for it: one terrible ("image"), one OK ("a building"), one excellent ("The Louvre Pyramid glowing at sunset, photographed in 2026"). Reflect on the difference.

### Exercise 3: SVG Inline

Embed an inline SVG circle, square, and triangle on a page. Try changing the colors by editing the `fill` attribute.

---

## Common Mistakes

### Mistake 1: No `alt` attribute

```html
<!-- WRONG -->
<img src="photo.jpg">
```

Always include `alt`. If decorative, use `alt=""`.

### Mistake 2: Useless `alt` text

```html
<!-- BAD -->
<img src="photo.jpg" alt="image">
<img src="photo.jpg" alt="photo">
```

Describe what's in the image. A screen reader user should be able to "see" it through your words.

### Mistake 3: Enormous image files

Don't upload a 5MB photo straight from your camera. Resize and compress to a reasonable size before putting it on a web page. Large images slow down your site dramatically.

### Mistake 4: Wrong format for the content

Using JPG for a logo (visible artifacts around text edges) or PNG for a full-screen photo (huge file size). Pick the right format.

### Mistake 5: Distorting aspect ratio

```html
<!-- Stretches the image -->
<img src="photo.jpg" width="400" height="400">
```

If you set both width and height to fixed values that don't match the original aspect ratio, the image will stretch. Use CSS `height: auto` or only specify one dimension.

### Mistake 6: Forgetting that the image is content, not decoration

Background images (added via CSS) are decoration. Inline images (added with `<img>`) are content. Content images need `alt`; decorative images don't. If removing the image would change the meaning of the page, it's content.

---

## Summary

- The `<img>` tag inserts an image. It's a void element (no closing tag).
- The `src` attribute points to the image file. The `alt` attribute describes it.
- Always write meaningful `alt` text. Use empty `alt=""` only for purely decorative images.
- Use `<figure>` and `<figcaption>` for captioned images.
- Choose the right format: JPG for photos, PNG for graphics, SVG for logos/icons, WebP for modern high-quality.
- Use `loading="lazy"` to defer off-screen images.
- Use `width` and `height` to prevent layout shifts during load.
- Wrap an image in `<a>` to make it clickable.

In the next lesson, we will learn how to create lists — a simple but powerful HTML structure you'll use everywhere.

---

**Next:** [Lesson 07: Lists →](./07-lists.md)
