# Content Analyzer (LLM-Powered)

AI-powered SEO content analysis and optimization suggestions.

---

## Features

```
CAPABILITIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Semantic content analysis
* Title and meta suggestions
* Keyword density optimization
* Readability scoring
* Competitor content comparison
* Automated recommendations
```

---

## Implementation

```typescript
import { ContentAnalyzer } from '@nesalia/seo';

const analyzer = new ContentAnalyzer({
  model: 'claude-opus-4-6',
  language: 'en',
});

// Analyze content
const analysis = await analyzer.analyze({
  url: 'https://example.com/blog/seo-guide',
  content: `
    <h1>The Ultimate SEO Guide for 2024</h1>
    <p>Search engine optimization is...</p>
    ...
  `,
  targetKeyword: 'seo guide',
  competitors: [
    'https://competitor1.com/seo',
    'https://competitor2.com/seo-guide',
  ],
});

console.log(analysis);
```

---

## Analysis Output

```typescript
interface ContentAnalysis {
  overall: {
    score: number;
    grade: 'A' | 'B' | 'C' | 'D' | 'F';
  };

  title: {
    current: string;
    score: number;
    suggestions: TitleSuggestion[];
  };

  metaDescription: {
    current: string;
    score: number;
    suggestions: string[];
  };

  headings: {
    structure: HeadingAnalysis[];
    score: number;
    suggestions: string[];
  };

  content: {
    wordCount: number;
    readabilityScore: number;
    keywordDensity: number;
    score: number;
    suggestions: ContentSuggestion[];
  };

  keywords: {
    primary: KeywordAnalysis;
    secondary: KeywordAnalysis[];
    missing: string[];
  };

  competitors: {
    comparison: CompetitorComparison;
    opportunities: string[];
  };

  recommendations: Recommendation[];
}
```

---

## LLM-Powered Suggestions

```typescript
// Generate title suggestions
const titles = await analyzer.generateTitles({
  topic: 'SEO best practices',
  targetKeyword: 'seo best practices',
  count: 5,
  style: 'informative', // 'informative' | 'clickbait' | 'question'
});

console.log(titles);
// [
//   "The Ultimate SEO Best Practices Guide for 2024",
//   "10 SEO Best Practices That Actually Work in 2024",
//   "SEO Best Practices: Everything You Need to Know",
//   ...
// ]

// Generate content outline
const outline = await analyzer.generateOutline({
  topic: 'Technical SEO Guide',
  targetKeyword: 'technical seo',
  sections: 8,
});

console.log(outline);
// [
//   { title: 'What is Technical SEO?', keywords: [] },
//   { title: 'Site Speed Optimization', keywords: ['page speed', 'core web vitals'] },
//   { title: 'Mobile-First Indexing', keywords: ['mobile-first', 'responsive'] },
//   ...
// ]
```

---

## Batch Analysis

```typescript
// Analyze all blog posts
const posts = await db.posts.findMany({ where: { published: true }});

const results = await analyzer.analyzeBatch(posts.map(post => ({
  url: post.slug,
  content: post.content,
  targetKeyword: post.targetKeyword,
})));

// Find content needing updates
const needsUpdate = results.filter(r => r.overall.score < 70);
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
