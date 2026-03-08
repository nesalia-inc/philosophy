# Pull Request Templates

Choose the right template for your pull request based on the type of change.

---

## How to Choose a Template

```
┌─────────────────────────────────────────────────────────────────────┐
│                    WHAT TYPE OF CHANGE IS IT?                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. Is it adding a NEW feature or capability?                       │
│     → Use [feature.md](./feature.md)                                │
│                                                                      │
│  2. Is it fixing a BUG in existing code?                            │
│     → Use [bugfix.md](./bugfix.md)                                  │
│                                                                      │
│  3. Is it restructuring code without changing behavior?            │
│     → Use [refactor.md](./refactor.md)                              │
│                                                                      │
│  4. Is it only updating DOCUMENTATION?                              │
│     → Use [documentation.md](./documentation.md)                    │
│                                                                      │
│  5. Is it fixing a SECURITY vulnerability?                          │
│     → Use [security.md](./security.md)                              │
│                                                                      │
│  6. Is it improving PERFORMANCE?                                     │
│     → Use [performance.md](./performance.md)                        │
│                                                                      │
│  7. Is it an URGENT production hotfix?                               │
│     → Use [hotfix.md](./hotfix.md)                                  │
│                                                                      │
│  8. Is it a BREAKING change to the API?                             │
│     → Use [breaking-change.md](./breaking-change.md)                │
│                                                                      │
│  9. Is it a tiny, trivial fix?                                      │
│     → Use [quick-fix.md](./quick-fix.md)                            │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Available Templates

| Template | Usage | Size | Review Level |
|----------|-------|------|--------------|
| [feature.md](./feature.md) | 🚀 New features (non-breaking) | Any | Standard |
| [bugfix.md](./bugfix.md) | 🐛 Bug fixes | Any | Standard |
| [refactor.md](./refactor.md) | ♻️ Code quality improvements | Any | Standard |
| [documentation.md](./documentation.md) | 📚 Docs only changes | Any | Light |
| [security.md](./security.md) | 🔐 Security fixes | Any | Strict + Security Review |
| [performance.md](./performance.md) | ⚡ Performance optimizations | Any | Standard + Perf Review |
| [hotfix.md](./hotfix.md) | 🔥 Emergency production fixes | Small | Expedited |
| [breaking-change.md](./breaking-change.md) | 💥 Breaking API changes | Any | Strict + Tech Lead |
| [quick-fix.md](./quick-fix.md) | 📋 Trivial fixes (< 50 lines) | Tiny | Light |

---

## Template Details

### 🚀 [feature.md](./feature.md)

**Use when:**
- Adding new functionality
- Adding new API endpoints
- Adding new features to existing code

**Includes:**
- Feature description and motivation
- Implementation approach
- Test plan with coverage requirements
- Breaking change checklist
- API changes documentation
- Database migration steps
- Performance considerations

**Example:** `feat: add user authentication with JWT`

---

### 🐛 [bugfix.md](./bugfix.md)

**Use when:**
- Fixing a bug in production code
- Resolving unexpected behavior
- Fixing edge cases

**Includes:**
- Bug description and symptoms
- Root cause analysis
- Before/after code comparison
- Reproduction steps
- Fix verification
- Regression prevention
- Impact assessment

**Example:** `fix: resolve infinite loop in data processing`

---

### ♻️ [refactor.md](./refactor.md)

**Use when:**
- Improving code structure
- Reducing technical debt
- Applying new patterns
- NO behavior changes

**Includes:**
- Refactoring motivation
- Before/after comparison
- Code quality improvements
- Migration path (if needed)
- Backwards compatibility
- Refactoring technique used

**Example:** `refactor: migrate payment processing to Result<T, E> pattern`

---

### 📚 [documentation.md](./documentation.md)

**Use when:**
- Updating README
- Adding API documentation
- Writing guides/tutorials
- Updating code comments

**Includes:**
- Documentation type
- Content quality checklist
- Code examples
- Screenshots/diagrams
- Accessibility check
- Target audience

**Example:** `docs: add API reference for authentication endpoints`

---

### 🔐 [security.md](./security.md)

**Use when:**
- Fixing security vulnerabilities
- Addressing security advisories
- Implementing security features

**Includes:**
- Severity assessment
- Vulnerability details
- Attack scenarios
- Root cause and fix
- Security testing
- Disclosure policy
- Post-mortem requirements

**Example:** `security: fix SQL injection vulnerability in user search`

---

### ⚡ [performance.md](./performance.md)

**Use when:**
- Optimizing slow code
- Reducing memory usage
- Improving throughput
- Caching implementations

**Includes:**
- Performance goals and baseline
- Technical approach
- Before/after benchmarks
- Load test methodology
- Optimization trade-offs
- Monitoring setup

**Example:** `perf: optimize database queries to reduce latency by 80%`

---

### 🔥 [hotfix.md](./hotfix.md)

**Use when:**
- CRITICAL production issues
- Breaking production functionality
- Data loss or security breach
- Requires IMMEDIATE deployment

**Includes:**
- Severity and urgency
- Issue summary and impact
- Minimal fix details
- Bypassed process acknowledgment
- Minimal testing
- Deployment steps
- Post-hotfix actions

**Example:** `hotfix: fix payment processing failure in production`

---

### 💥 [breaking-change.md](./breaking-change.md)

**Use when:**
- Changing public API
- Removing functionality
- Changing return types
- Modifying database schema

**Includes:**
- Breaking change notice
- Migration guide (step-by-step)
- Impact assessment
- Deprecation timeline
- Before/after code examples
- Migration script (if available)
- Support policy

**Example:** `BREAKING CHANGE: replace callback-based API with Promise-based API`

---

### 📋 [quick-fix.md](./quick-fix.md)

**Use when:**
- Trivial typo fixes
- Simple variable renaming
- Comment improvements
- Very small tweaks (< 50 lines)

**Includes:**
- One-line summary
- What's being fixed
- How it's fixed
- Test command

**Example:** `fix: correct typo in error message`

---

## Using Templates

### On GitHub

1. Create a new pull request
2. Click "Template" dropdown
3. Select the appropriate template
4. Fill in all sections
5. Remove placeholder comments

### From CLI

```bash
# Create PR with specific template
gh pr create --template feature.md --title "feat: add authentication" --body "$(cat <<'EOF'
<!-- Fill in the template here -->
EOF
)
```

---

## Template Philosophy

All templates are designed to:

### ✅ Be Explicit
- No ambiguity about what's needed
- Clear sections with specific questions
- Checklists for verification

### ✅ Enforce Quality
- Result<T, E> pattern checks
- Option<T> pattern checks
- Test coverage requirements
- Security considerations

### ✅ Reduce Cognitive Load
- Fill-in-the-blank format
- Clear structure
- Examples provided
- Minimal free-form text

### ✅ Be Automatable
- Structured sections parseable by tools
- Consistent format across all PRs
- Easy to extract metrics

---

## Customizing Templates

Templates can be customized per repository by editing:

```
.github/PULL_REQUEST_TEMPLATE/
├── feature.md
├── bugfix.md
├── refactor.md
└── ...
```

**Keep the core structure:**
- Summary
- Type of change
- Changes made
- Test plan
- Quality checklist
- Additional context

**Add repository-specific sections:**
- Custom deployment steps
- Project-specific checks
- Domain-specific requirements

---

## Best Practices

### For Authors

✅ **Always use a template** - Don't create PRs without one
✅ **Fill every section** - Incomplete descriptions delay reviews
✅ **Be specific** - Vague descriptions lead to questions
✅ **Provide context** - Link to issues, designs, discussions
✅ **Show, don't tell** - Use code examples, screenshots

❌ **Don't skip sections** - If not applicable, say "N/A" with reason
❌ **Don't be vague** - "Fixed bug" tells us nothing
❌ **Don't forget tests** - Show how you verified your changes

### For Reviewers

✅ **Check template completion** - Request more info if incomplete
✅ **Follow the checklist** - Verify each item
✅ **Focus on sections** - Different sections for different review aspects
✅ **Reference template** - Use template structure in feedback

---

## Related Documentation

- [PR Best Practices](../README.md) - Complete PR workflow guide
- [Review Guidelines](../review-guidelines.md) - How to conduct reviews
- [Quality Gates](../quality-gates.md) - Automated quality checks

---

*Document version: 1.0*
*Last updated: 2025-03-08*
