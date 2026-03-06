# Documentation Templates

Ready-to-use templates for common documentation types.

---

## User Documentation Templates

### README.md

```markdown
# [Project Name]

[One sentence describing what this project does]

## Quick Start

\`\`\`bash
npm install
npm run dev
\`\`\`

## Features

- Feature 1
- Feature 2

## Basic Usage

\`\`\`typescript
import { feature } from '@org/package'

const result = feature({ option: 'value' })
\`\`\`

## Documentation

- [Quick Start](./quick-start.md)
- [Tutorial](./tutorial.md)
- [API Reference](./api/)

## License

MIT
```

### Quick Start.md

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

- [Tutorial](./tutorial.md)
- [API Reference](../api/)
```

### Troubleshooting.md

```markdown
# Troubleshooting

Solutions to common problems.

## [Error Name]

**Cause**: [What causes this]

**Solution**:
\`\`\`bash
# Command or code
\`\`\`

## [Another Error]

**Cause**: [What causes this]

**Solution**:
\`\`\`bash
# Command or code
\`\`\`
```

---

## Agent Configuration Templates

### .ai/rules.md

```markdown
# Coding Rules

## 1. No any Types

Never use \`any\`. Use specific types.

\`\`\`typescript
// ✅ GOOD
type User = { id: string; name: string }

// ❌ BAD
const data: any = getData()
\`\`\`

## 2. Errors Are Not Thrown

Use Result<T, E> for errors.

\`\`\`typescript
// ✅ GOOD
if (!user) return Result.err({ type: 'not_found' })

// ❌ BAD
if (!user) throw new Error('not found')
\`\`\`

## 3. Single Responsibility

One function = one thing.

\`\`\`typescript
// ✅ GOOD
const validate = (input: Input): ValidationResult => ...
const process = (validated: ValidInput): Processed => ...

// ❌ BAD
const handle = (input) => {
  // validates
  // processes
  // saves
  // responds
}
\`\`\`
```

### .ai/context.md

```markdown
# Project Context

## Name
[Project name]

## What This Does
[One sentence describing the project]

## Tech Stack
- Frontend: [Next.js, React, TypeScript]
- Backend: [Next.js API / FastAPI]
- Database: [PostgreSQL + Drizzle]

## Key Patterns
- Collections pattern for database
- Result<T, E> for error handling
- Feature-based directory structure

## Important Directories
- \`src/features/\` - Feature modules
- \`src/lib/\` - Utilities
- \`src/db/\` - Database configuration

## Naming Conventions
- Files: kebab-case (user-service.ts)
- Types: PascalCase (UserService)
- Functions: camelCase (getUser)

## Testing
- Framework: Vitest
- Location: \`tests/\`
- Pattern: [pattern used]
```

### .ai/prompts/new-feature.md

```markdown
# New Feature Workflow

Follow this workflow for new features.

## 1. Read Context (Required)
- Read \`.ai/rules.md\` ← MUST understand
- Read \`.ai/context.md\` ← Project overview
- Find similar existing features

## 2. Plan
Write an implementation plan:
- Files to create
- Files to modify
- Tests to add

Share plan before coding.

## 3. Implement
- Follow \`.ai/rules.md\` exactly
- Use specific types, never \`any\`
- Return Result for errors
- Keep functions single-purpose

## 4. Test
- Write unit tests
- Write integration tests if needed
- Test error cases

## 5. Verify
Run \`npm run ci\`. All checks must pass.

## 6. Create PR
- Fill the PR template
- Link to the issue
- Explain what was changed
```

---

## Issue Templates

### 0000-feature.md

```markdown
---
name: Feature Request
about: Suggest a new feature
title: '[FEATURE] '
labels: 'enhancement'
---

## Description
[One sentence about what you want]

## Problem
[Explain the pain point]

## Solution
[Describe your solution]

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
```

---

## PR Template

```markdown
## Summary
[What this PR does]

## Changes
- [List specific changes]

## Test Plan
- [ ] Tests pass
- [ ] Manual testing done

## Checklist
- [ ] Follows .ai/rules.md
- [ ] Tests included
- [ ] Documentation updated
```

---

*Document version: 1.0*
*Last updated: 2026-03-06*
