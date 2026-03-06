# External Documentation (OSS)

Documentation for developers who use our packages.

---

## Purpose

**Question**: "How do I use this package?"

**Audience**: External developers who install and use our packages in their projects.

**Standard**: Must be as good as Next.js, React, Stripe documentation.

---

## Structure

Each package should have:

```
@nesalia/package-name/
├── docs/
│   ├── README.md              # Quick start + overview
│   ├── installation.md        # How to install
│   ├── quick-start.md         # Get started in 5 minutes
│   ├── guides/                # How to do specific things
│   │   ├── feature-a.md
│   │   └── feature-b.md
│   ├── api/                   # Complete API reference
│   │   ├── overview.md
│   │   └── functions.md
│   ├── examples/              # Working examples
│   │   └── basic-usage.ts
│   └── troubleshooting.md     # Common issues
```

---

## README.md Template

```markdown
# @nesalia/package-name

[One sentence describing what this package does]

## Features

- Feature 1
- Feature 2
- Feature 3

## Installation

```bash
npm install @nesalia/package-name
```

## Quick Start

```typescript
import { function } from '@nesalia/package-name'

const result = function({ option: 'value' })
```

## Documentation

- [Quick Start](./quick-start.md)
- [Guides](./guides/)
- [API Reference](./api/)
- [Examples](./examples/)

## Support

- GitHub Issues
- Discord

## License

MIT
```

---

## Quick Start Template

```markdown
# Quick Start

Get started in 5 minutes.

## Installation

```bash
npm install @nesalia/package-name
```

## Basic Usage

### Step 1: Import

```typescript
import { feature } from '@nesalia/package-name'
```

### Step 2: Configure

```typescript
const config = {
  apiKey: process.env.API_KEY
}
```

### Step 3: Use

```typescript
const result = feature(config, { input: 'value' })
```

## Next Steps

- Read the [Guides](./guides/)
- Explore the [API Reference](./api/)
```

---

## API Reference Template

```markdown
# API Reference

## functionName

Description of what this function does.

### Signature

```typescript
function functionName(input: InputType): Promise<Result<OutputType, ErrorType>>
```

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| param1 | string | Yes | Description |
| param2 | number | No | Description (default: 10) |

### Returns

`Promise<Result<OutputType, ErrorType>>`

### Errors

| Error Type | Description |
|------------|-------------|
| error_one | When this happens |
| error_two | When that happens |

### Example

```typescript
const result = await functionName({
  param1: 'value',
  param2: 5
})

if (result.ok) {
  console.log(result.value)
} else {
  console.error(result.error)
}
```

---

## Quality Checklist

- [ ] Title clearly describes what the package does
- [ ] Installation instructions work
- [ ] Every code example is tested and works
- [ ] Quick start completes in 5 minutes
- [ ] API reference covers every export
- [ ] Examples are copy-paste ready
- [ ] Troubleshooting section exists
- [ ] Links work
- [ ] No broken images

---

## Writing Guidelines

### Be Action-Oriented

```
❌ BAD:
"The authentication system supports OAuth2."

✅ GOOD:
"Add OAuth2 login in 3 steps:"
1. Configure provider
2. Add callback route
3. Use the auth() helper
```

### Show Working Code

Every example must work when copied:

```typescript
// ✅ GOOD: Complete example
import { authenticate } from '@nesalia/auth'

const result = await authenticate({
  email: 'user@example.com',
  password: 'password123'
})

if (result.ok) {
  console.log('Logged in:', result.value)
}

// ❌ BAD: Incomplete
const result = authenticate(...)
```

### Explain the Why

```
❌ BAD:
"Use map() to transform data"

✅ GOOD:
"Use map() to transform database rows to UI models.
This separates concerns: your database schema can evolve
independently from your UI needs."
```

### Be Honest About Limits

```
❌ BAD:
"Works perfectly"

✅ GOOD:
"Known issue: Slow on Firefox. See workaround in troubleshooting."
```

---

*Document version: 1.0*
*Last updated: 2026-03-06*
