# User Documentation

Documentation written for human developers.

---

## Types of User Documentation

| Type | Purpose | Length |
|------|---------|--------|
| README | Quick overview | 1 page |
| Quick Start | Start in 5 minutes | 1-2 pages |
| Tutorial | Learn by doing | 30 min |
| Guide | Specific task | 5-10 pages |
| API Reference | Complete reference | Per endpoint |
| Troubleshooting | Solve problems | By error |

---

## The Progressive Disclosure Model

### Level 1: README (1 minute)

```
# Project Name

One sentence describing what this does.

## Quick Start

npm install
npm run dev
```

### Level 2: Quick Start (5 minutes)

```
# Quick Start

## Installation

npm install @org/package

## Basic Usage

import { function } from '@org/package'

function(input)
```

### Level 3: Tutorial (30 minutes)

```
# Tutorial: Building a Todo App

## Step 1: Setup
## Step 2: Create Components
## Step 3: Add Functionality
## Step 4: Style
```

### Level 4: Deep Dive (hours)

```
# Architecture

## Core Concepts
## Data Flow
## Advanced Usage
```

---

## Writing Guidelines

### Start with the Question

```
❌ BAD:
"Configuration"

✅ GOOD:
"How to configure the database"

❌ BAD:
"API Methods"

✅ GOOD:
"How to fetch user data"
```

### Show Working Examples

```typescript
// ❌ BAD: Incomplete
const db = database()

// ✅ GOOD: Complete and copy-paste ready
import { database } from '@org/db'

const db = database({
  connectionString: process.env.DATABASE_URL,
  ssl: true
})
```

### Explain the Why

```
❌ BAD:
"Use map() to transform data"

✅ GOOD:
"Use map() to transform database rows to UI models.
This separates concerns: database schema from UI needs."
```

### Be Honest About Limits

```
❌ BAD:
"Works perfectly"

✅ GOOD:
"Note: Large datasets (>10k rows) may be slow.
For pagination, see the query section."
```

---

## Template: README.md

```markdown
# Project Name

[One sentence describing what this project does]

## Quick Start

\`\`\`bash
npm install
npm run dev
\`\`\`

## Features

- Feature 1
- Feature 2
- Feature 3

## Basic Usage

\`\`\`typescript
import { thing } from '@org/package'

const result = thing({ option: value })
\`\`\`

## Configuration

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| option | string | "default" | What this does |

## Documentation

- [Quick Start](./quick-start.md)
- [Tutorial](./tutorial.md)
- [API Reference](./api/)

## License

MIT
```

---

## Template: Quick Start

```markdown
# Quick Start

Get started in 5 minutes.

## Installation

\`\`\`bash
npm install @org/package
\`\`\`

## Basic Usage

### Step 1: Import

\`\`\`typescript
import { feature } from '@org/package'
\`\`\`

### Step 2: Configure

\`\`\`typescript
const config = {
  apiKey: process.env.API_KEY
}
\`\`\`

### Step 3: Use

\`\`\`typescript
const result = feature(config, { input: 'value' })
\`\`\`

## Next Steps

- Read the [Tutorial](./tutorial.md)
- Explore the [API Reference](../api/)
- See [Examples](./examples/)
```

---

## Template: Guide

```markdown
# Guide: Specific Task

How to accomplish a specific goal.

## Prerequisites

- Thing 1 installed
- Thing 2 configured

## Steps

### Step 1: Do the first thing

Explain what to do:

\`\`\`typescript
// Code example
\`\`\`

### Step 2: Do the second thing

\`\`\`typescript
// Code example
\`\`\`

## Common Issues

### Problem: Error message

**Solution**: Explanation

## Related

- [Related feature](./other.md)
- [API Reference](../api/)
```

---

## Template: Troubleshooting

```markdown
# Troubleshooting

Solutions to common problems.

## Connection Issues

### "Cannot connect to database"

**Cause**: Missing DATABASE_URL

**Solution**:
\`\`\`bash
export DATABASE_URL="postgresql://..."
\`\`\`

### "Connection timeout"

**Cause**: Database server down

**Solution**: Check server status

## Authentication Errors

### "Invalid API key"

**Cause**: Wrong or expired key

**Solution**: Regenerate key in dashboard

## Performance

### "Slow queries"

**Cause**: Missing index

**Solution**: Add index:
\`\`\`sql
CREATE INDEX idx_column ON table(column)
\`\`\`
```

---

## Quality Checklist

Before publishing:

- [ ] Title matches content
- [ ] Every code example works
- [ ] Prerequisites listed
- [ ] Steps are in order
- [ ] Error cases covered
- [ ] Links work
- [ ] Reviewed by human

---

*Document version: 1.0*
*Last updated: 2026-03-06*
