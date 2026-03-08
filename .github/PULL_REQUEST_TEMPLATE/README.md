# Pull Request Templates

Choose the appropriate template for your pull request based on the type of change.

## Available Templates

| Template | Usage | Review Level |
|----------|-------|--------------|
| **Feature** | New features and functionality | Standard |
| **Bug Fix** | Bug fixes in production code | Standard |
| **Refactor** | Code quality improvements | Standard |
| **Documentation** | Docs only changes | Light |
| **Security** | Security fixes | Strict |
| **Performance** | Performance optimizations | Standard |
| **Hotfix** | Emergency production fixes | Expedited |
| **Breaking Change** | Breaking API changes | Strict + Tech Lead |
| **Quick Fix** | Trivial fixes (< 50 lines) | Light |

## How to Choose

1. **Feature** - Adding new functionality, API endpoints, or features
2. **Bug Fix** - Fixing a bug or unexpected behavior
3. **Refactor** - Restructuring code without changing behavior
4. **Documentation** - Updating README, API docs, guides
5. **Security** - Fixing security vulnerabilities
6. **Performance** - Optimizing performance, caching, queries
7. **Hotfix** - CRITICAL production issues requiring immediate deployment
8. **Breaking Change** - Changes that break existing API
9. **Quick Fix** - Tiny trivial fixes (typos, formatting)

## PR Title Format

Follow conventional commit format:
```
feat: add user authentication
fix: resolve database connection leak
refactor: migrate to Result<T, E> pattern
docs: update API documentation
security: fix SQL injection vulnerability
perf: optimize database queries
```

## Required Sections

All PRs must include:
- Clear description of changes
- Type of change selected
- Files changed listed
- Test plan included
- Code quality checklist completed

## Philosophy Alignment

All code must follow:
- `Result<T, E>` pattern for error handling
- `Option<T>` pattern for optional values
- No `any` types
- Functions under 50 lines
- Real tests, not mocks
