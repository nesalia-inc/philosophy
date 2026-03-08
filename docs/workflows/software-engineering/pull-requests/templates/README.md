# Pull Request Templates

Ready-to-use GitHub pull request templates that can be directly copied into any project repository.

## Available Templates

| Template | Usage | Review Level |
|----------|-------|--------------|
| **[feature.md](./feature.md)** | New features and functionality | Standard |
| **[bugfix.md](./bugfix.md)** | Bug fixes in production code | Standard |
| **[refactor.md](./refactor.md)** | Code quality improvements | Standard |
| **[documentation.md](./documentation.md)** | Docs only changes | Light |
| **[security.md](./security.md)** | Security fixes | Strict |
| **[performance.md](./performance.md)** | Performance optimizations | Standard |
| **[hotfix.md](./hotfix.md)** | Emergency production fixes | Expedited |
| **[breaking-change.md](./breaking-change.md)** | Breaking API changes | Strict + Tech Lead |
| **[quick-fix.md](./quick-fix.md)** | Trivial fixes (< 50 lines) | Light |

---

## How to Use

### Option 1: Copy All Templates

```bash
# Copy the entire template directory to your project
cp -r docs/workflows/software-engineering/pull-requests/templates/* /path/to/project/.github/PULL_REQUEST_TEMPLATE/
```

### Option 2: Copy Individual Templates

```bash
# Copy only the templates you need
cp docs/workflows/software-engineering/pull-requests/templates/feature.md /path/to/project/.github/PULL_REQUEST_TEMPLATE/
cp docs/workflows/software-engineering/pull-requests/templates/bugfix.md /path/to/project/.github/PULL_REQUEST_TEMPLATE/
```

---

## Template Features

Each template includes:

### GitHub Frontmatter
Auto-fills PR metadata:
```yaml
---
name: Feature Pull Request
about: Template for new features
title: 'feat: '
labels: feature
assignees: ''
---
```

### Philosophy-Aligned Checklists
- **Result<T, E>** pattern for error handling
- **Option<T>** pattern for optional values
- No `any` types
- Functions under 50 lines
- Real tests, not mocks

### Complete Sections
- Files changed tracking
- Test plan requirements
- Code quality checklist
- Security review
- Performance considerations
- Deployment steps
- Rollback plan

---

## PR Title Format

Templates auto-fill with conventional commit prefixes:

```
feat: add user authentication
fix: resolve database connection leak
refactor: migrate to Result<T, E> pattern
docs: update API documentation
security: fix SQL injection vulnerability
perf: optimize database queries
```

---

## How They Work in GitHub

1. **Create a PR** on GitHub
2. **Select Template** from dropdown
3. **Auto-fill** happens automatically:
   - Title prefix (feat:, fix:, etc.)
   - Labels assigned
   - Template loads
4. **Fill out** the template
5. **Submit** PR

---

## Customization

### Modify Template Frontmatter

Edit the YAML frontmatter in any template:

```yaml
---
name: Your Custom Name
about: Your description
title: 'custom: '
labels: custom-label
assignees: '@default-reviewer'
---
```

### Add Project-Specific Sections

Add your custom sections to templates:

```markdown
## Project Specific Checklist
- [ ] Follows our coding standards
- [ ] Meets our performance requirements
- [ ] Passes our custom lint rules
```

---

## Philosophy Alignment

All templates enforce the project philosophy:

### Result<T, E> Pattern
```typescript
// Every function returning errors must use Result<T, E>
function authenticate(credentials: Credentials): Result<User, AuthError> {
  // ...
}
```

### Option<T> Pattern
```typescript
// Optional values use Option<T>, not null/undefined
function findUser(id: string): Option<User> {
  // ...
}
```

### Type Safety
```typescript
// No any types allowed
❌ function process(data: any): any
✅ function process(data: Data): Result<Processed, Error>
```

### Testing
```typescript
// Real implementations, not mocks
✅ it('authenticates with valid credentials', () => {
✅   const result = authenticate(validCredentials)
✅   expect(result.ok).toBe(true)
✅ })
```

---

## Required Sections

Every template requires completion of:

1. **Summary** - What this PR does
2. **Type of Change** - Feature, fix, refactor, etc.
3. **Files Changed** - What was modified
4. **Test Plan** - How to verify
5. **Code Quality** - Philosophy compliance
6. **Deployment** - How to deploy and rollback

---

## Quick Reference

### When to Use Each Template

| Situation | Use Template |
|-----------|-------------|
| Adding new functionality | `feature.md` |
| Fixing a bug | `bugfix.md` |
| Restructuring code | `refactor.md` |
| Updating docs only | `documentation.md` |
| Fixing security issue | `security.md` |
| Improving performance | `performance.md` |
| Emergency production fix | `hotfix.md` |
| Breaking existing API | `breaking-change.md` |
| Tiny trivial fix | `quick-fix.md` |

### Branch Naming

Match branch names to templates:

```
feature/new-authentication
fix/login-bug
refactor/payment-processor
docs/api-guide
security/sql-injection-fix
perf-cache-optimization
hotfix-payment-crash
refactor/breaking-api-v2
fix/typo-error-message
```

---

## Additional Resources

- [Branching Strategy](../branching-strategy.md) - Git workflow
- [Review Guidelines](../review-guidelines.md) - How to review
- [Merging Strategy](../merging-strategy.md) - How to merge
- [Quality Gates](../quality-gates.md) - Automated checks
- [PR Lifecycle](../lifecycle.md) - PR stages and SLAs

---

*Document version: 2.0*
*Last updated: 2025-03-08*
