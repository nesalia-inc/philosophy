# Render Blocking

Render blocking resources prevent the page from rendering until they're fully loaded.

---

## Severity

**HIGH** - Increases First Contentful Paint (FCP) and LCP.

---

## Types

### 1. CSS Render Blocking

```html
<!-- VULNERABLE - Blocks rendering -->
<head>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <h1>Hello</h1> <!-- Can't render until CSS loads! -->
</body>
```

### 2. JavaScript Render Blocking

```html
<!-- VULNERABLE - Blocks HTML parsing -->
<head>
  <script src="analytics.js"></script>
  <script src="widget.js"></script>
</head>
<body>
  <h1>Hello</h1> <!-- Can't render until JS downloads and executes! -->
</body>
```

---

## Prevention

### 1. Inline Critical CSS

```html
<!-- GOOD - Inline critical CSS -->
<head>
  <style>
    /* Critical CSS only */
    body { margin: 0; font-family: system-ui; }
    .header { background: blue; }
  </style>
  <!-- Non-critical CSS loaded async -->
  <link rel="preload" href="styles.css" as="style"
        onload="this.onload=null;this.rel='stylesheet'">
</head>
```

### 2. Defer JavaScript

```html
<!-- GOOD - Defer JS -->
<script src="app.js" defer></script>

<!-- GOOD - Async JS -->
<script src="analytics.js" async></script>
```

### 3. Use link rel="preload"

```html
<!-- GOOD - Preload critical resources -->
<link rel="preload" href="critical.js" as="script">
<link rel="preload" href="font.woff2" as="font" crossorigin>
```

### 4. Next.js Specific

```tsx
// GOOD - Next.js handles this automatically
// Use next/script with strategy
import Script from 'next/script';

<Script
  src="https://analytics.example.com/script.js"
  strategy="lazyOnload" // Loads during idle time
/>
```

---

## Fix Example

```
BEFORE (Render blocking):
━━━━━━━━━━━━━━━━━━━━━━━

<head>
  <link rel="stylesheet" href="styles.css">
  <script src="app.js"></script>
  <script src="analytics.js"></script>
  <script src="widget.js"></script>
</head>

AFTER (Optimized):
━━━━━━━━━━━━━━━━━━

<head>
  <!-- Critical CSS inlined -->
  <style>/* body, header, nav styles */</style>

  <!-- Non-critical CSS loaded async -->
  <link rel="preload" href="styles.css" as="style"
        onload="this.onload=null;this.rel='stylesheet'">

  <!-- Deferred JS -->
  <script src="app.js" defer></script>

  <!-- Analytics loaded during idle -->
  <script src="analytics.js" async></script>
</head>
```

---

## Metrics

```
TARGETS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

FCP (First Contentful Paint):
* < 1.8 seconds = Good
* > 3.0 seconds = Poor

LCP (Largest Contentful Paint):
* < 2.5 seconds = Good
* > 4.0 seconds = Poor
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
