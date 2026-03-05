# SEO Crawler

Automated crawling and analysis of web pages for SEO optimization.

---

## Features

```
CAPABILITIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Concurrent crawling with rate limiting
* Page analysis (meta, content, links)
* Image analysis (alt tags, sizes)
* Internal/external link tracking
* JavaScript rendering support
* Custom extraction rules
```

---

## Implementation

```typescript
import { crawlPage, analyzeSEO } from '@nesalia/seo';

const crawler = new SEOCrawler({
  concurrency: 5,
  delay: 1000,
  maxPages: 1000,
  renderJS: true,
});

// Crawl website
const pages = await crawler.crawl('https://example.com');

// Analyze each page
for (const page of pages) {
  const analysis = analyzeSEO(page);

  console.log({
    url: page.url,
    title: analysis.title,
    metaDescription: analysis.metaDescription,
    h1: analysis.headings.h1,
    imagesWithoutAlt: analysis.images.withoutAlt,
    internalLinks: analysis.links.internal.length,
    externalLinks: analysis.links.external.length,
    wordCount: analysis.content.wordCount,
    score: analysis.score,
  });
}
```

---

## Analysis Output

```typescript
interface SEOAnalysis {
  url: string;
  title: {
    text: string;
    length: number;
    score: number;
  };
  metaDescription: {
    text: string;
    length: number;
    score: number;
  };
  headings: {
    h1: string[];
    h2: string[];
    h3: string[];
  };
  images: {
    total: number;
    withAlt: number;
    withoutAlt: number;
    score: number;
  };
  links: {
    internal: string[];
    external: string[];
    broken: string[];
  };
  content: {
    wordCount: number;
    paragraphCount: number;
    score: number;
  };
  score: number;
  issues: SEOIssue[];
}

interface SEOIssue {
  type: 'error' | 'warning' | 'info';
  category: string;
  message: string;
  element?: string;
}
```

---

## Usage with CLI

```bash
# CLI usage
npx @nesalia/seo crawl https://example.com --output report.json

# With custom rules
npx @nesalia/seo crawl https://example.com \
  --rules custom-rules.json \
  --concurrency 10 \
  --delay 500
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
