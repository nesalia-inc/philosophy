# Image Optimization

Unoptimized images are often the largest resources on a page, significantly impacting load times.

---

## Severity

**HIGH** - Images often account for >50% of page weight.

---

## Common Issues

### 1. Wrong Format

```html
<!-- VULNERABLE - Using PNG for photo -->
<img src="photo.png"> <!-- 500KB -->

<!-- Should use WebP/AVIF: ~50KB -->
```

### 2. No Lazy Loading

```html
<!-- VULNERABLE - Loads all images immediately -->
<img src="hero.jpg">
<img src="photo1.jpg">
<img src="photo2.jpg">
<!-- ... 50 more images -->
```

### 3. No Responsive Images

```html
<!-- VULNERABLE - Desktop image on mobile -->
<img src="hero-large.jpg">
<!-- Mobile downloads 2000px image! -->
```

---

## Prevention

### 1. Use Modern Formats

```html
<!-- GOOD - Modern formats with fallback -->
<picture>
  <source srcset="photo.avif" type="image/avif">
  <source srcset="photo.webp" type="image/webp">
  <img src="photo.jpg" alt="Photo">
</picture>
```

### 2. Lazy Loading

```html
<!-- GOOD - Native lazy loading -->
<img src="photo.jpg" loading="lazy" alt="Photo">

<!-- GOOD - Below the fold -->
<img src="photo.jpg" loading="lazy" alt="Photo">

<!-- BAD - Above the fold -->
<img src="hero.jpg" loading="eager" alt="Hero">
```

### 3. Responsive Images

```html
<!-- GOOD - srcset for different sizes -->
<img
  srcset="photo-400.jpg 400w,
          photo-800.jpg 800w,
          photo-1200.jpg 1200w"
  src="photo-800.jpg"
  sizes="(max-width: 600px) 400px, 800px"
  alt="Photo"
>
```

### 4. Next.js Image

```tsx
// GOOD - Next.js automatic optimization
import Image from 'next/image';

<Image
  src="/photo.jpg"
  alt="Photo"
  width={800}
  height={600}
  placeholder="blur"
  blurDataURL="data:image/..."
/>
```

---

## Tools

```bash
# ImageMagick
convert input.jpg -resize 800x600 -quality 80 output.jpg

# Sharp (Node.js)
import sharp from 'sharp';

await sharp('input.jpg')
  .resize(800, 600)
  .webp({ quality: 80 })
  .toFile('output.webp');

# Squoosh.app (online)
```

---

## Fix Example

```
BEFORE (2MB page weight):
━━━━━━━━━━━━━━━━━━━━━━━━━━

<img src="hero.png">
<img src="photo1.png">
<img src="photo2.png">

Total: ~2MB images

AFTER (200KB page weight):
━━━━━━━━━━━━━━━━━━━━━━━━━━

<picture>
  <source srcset="hero.avif" type="image/avif">
  <source srcset="hero.webp" type="image/webp">
  <img src="hero.jpg" width="1200" height="600" alt="Hero">
</picture>

<img src="photo1.jpg" loading="lazy" width="400" height="300">
<img src="photo2.jpg" loading="lazy" width="400" height="300">

Total: ~200KB images
```

---

## Targets

```
IMAGE TARGETS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Format: WebP or AVIF
Size: Compressed to < 200KB for hero images
Responsive: Appropriate srcset for device
Lazy: All below-fold images
Dimensions: Always set width/height
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
