# CLS (Cumulative Layout Shift)

CLS measures visual stability - how much the page layout shifts unexpectedly during loading.

---

## Severity

**MEDIUM** - Affects user experience and Core Web Vitals score.

---

## Common Causes

### 1. Images Without Dimensions

```html
<!-- VULNERABLE -->
<img src="photo.jpg">

<!-- Page jumps when image loads! -->
```

### 2. Ads/Embeds Without Placeholders

```html
<!-- VULNERABLE -->
<div>
  <script async src="ad-script.js"></script>
  <!-- Ad loads and pushes content down! -->
</div>
```

### 3. Dynamic Content

```html
<!-- VULNERABLE -->
<div id="banner"></div>
<!-- Banner inserted via JS causes shift -->

<script>
  document.getElementById('banner').innerHTML = '<div>Welcome!</div>';
</script>
```

### 4. Web Fonts

```html
<!-- VULNERABLE -->
<link href="custom-font.woff2" rel="stylesheet">

<!-- Font swap causes layout shift -->
```

---

## Prevention

### 1. Always Set Image Dimensions

```html
<!-- GOOD -->
<img src="photo.jpg" width="800" height="600" alt="Photo">

<!-- GOOD - CSS aspect-ratio -->
<img src="photo.jpg" style="aspect-ratio: 4/3;" loading="lazy">
```

### 2. Use Placeholders

```html
<!-- GOOD - Placeholder for ads -->
<div style="min-height: 250px; background: #eee;">
  <!-- Ad loads here -->
</div>

<!-- GOOD - Skeleton -->
<div class="skeleton" style="height: 200px;"></div>
```

### 3. Reserve Space

```css
/* GOOD - Reserve space for dynamic content */
#banner {
  min-height: 50px;
}

/* GOOD - Use aspect-ratio */
.hero-image {
  aspect-ratio: 16/9;
  background: #eee;
}
```

### 4. Font Loading

```css
/* GOOD - Font display swap */
@font-face {
  font-family: 'CustomFont';
  src: url('font.woff2') format('woff2');
  font-display: swap; /* Shows fallback until loaded */
}
```

---

## Fix Example

```
BEFORE (CLS: 0.5):
━━━━━━━━━━━━━━━━━━━━

<img src="hero.jpg">

<div id="notifications"></div>

<script>
  // Content jumps in
  document.getElementById('notifications').innerHTML = '<div>New!</div>';
</script>

AFTER (CLS: 0):
━━━━━━━━━━━━━━━━━━

<img src="hero.jpg" width="1200" height="600" alt="Hero">

<div id="notifications" style="min-height: 40px;"></div>

<script>
  // Reserve space before inserting
  document.getElementById('notifications').innerHTML = '<div style="height:40px">New!</div>';
</script>
```

---

## Targets

```
CLS TARGETS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Good: < 0.1
Needs Improvement: 0.1 - 0.25
Poor: > 0.25
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
