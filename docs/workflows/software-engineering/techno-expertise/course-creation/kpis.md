# Techno Course KPIs

Key Performance Indicators for measuring techno course quality.

---

## Why KPIs Matter

KPIs ensure courses meet quality standards and provide real value to developers and agents.

---

## Core KPIs

### 1. Completeness

**Definition**: All required sections are present and fully populated.

| Metric | Target | How to Measure |
|--------|--------|----------------|
| Sections present | 100% | Count sections vs required |
| Content depth | 3+ concepts per section | Manual review |
| Examples count | 3+ per course | File count |

**Required Sections Checklist**:

- [ ] README.md with Quick Start
- [ ] Fundamentals (3+ concepts)
- [ ] Patterns (2+ patterns)
- [ ] Pitfalls (2+ pitfalls)
- [ ] Examples (3+ examples)
- [ ] Changelog

---

### 2. Accuracy

**Definition**: Information matches official documentation.

| Metric | Target | How to Measure |
|--------|--------|----------------|
| Factual errors | 0 | Expert review |
| Outdated APIs | 0 | Compare with current version |
| Link validity | 100% | Automated link check |

**Verification Process**:

1. Cross-reference with official docs
2. Check API versions
3. Validate all external links
4. Expert review

---

### 3. Practicality

**Definition**: Code examples are copy-paste ready and working.

| Metric | Target | How to Measure |
|--------|--------|----------------|
| Working examples | 100% | Run all code |
| Copy-paste ready | Yes | No missing imports/context |
| Real-world scenarios | 3+ | Match common use cases |

**Example Validation**:

```typescript
// Must include:
- All imports
- Complete functions
- No placeholders (TODO, FIXME)
// Must test:
- Run code in real environment
- Verify output matches expectation
```

---

### 4. Usability

**Definition**: Easy for developers to find and understand information.

| Metric | Target | How to Measure |
|--------|--------|----------------|
| Quick Start time | < 5 min | User testing |
| Navigation | Intuitive | User feedback |
| Clarity score | 4/5 | Survey |

**Navigation Requirements**:

- Clear table of contents
- Logical section order
- Cross-references between sections
- Consistent formatting

---

### 5. Currency

**Definition**: Information is up-to-date.

| Metric | Target | How to Measure |
|--------|--------|----------------|
| Last update | < 6 months | Changelog date |
| Version accuracy | Current | Compare with latest |
| Deprecated APIs | 0 | Check deprecation lists |

**Update Triggers**:

- New major version released
- Breaking changes announced
- Common patterns change
- Annual review

---

### 6. Searchability

**Definition**: Information is easy to find.

| Metric | Target | How to Measure |
|--------|--------|----------------|
| Search time | < 30 sec | User testing |
| Relevant results | 90%+ | Query testing |
| Clear structure | Yes | Manual review |

**Structure Requirements**:

- Descriptive headings
- Consistent naming
- Tags/categories
- Index files

---

## KPI Scoring

### Score Calculation

| Level | Score | Meaning |
|-------|-------|---------|
| Excellent | 90-100% | Production ready |
| Good | 75-89% | Minor improvements needed |
| Fair | 60-74% | Significant work needed |
| Poor | < 60% | Not ready for use |

### Weighting

| KPI | Weight |
|-----|--------|
| Completeness | 25% |
| Accuracy | 25% |
| Practicality | 20% |
| Usability | 15% |
| Currency | 10% |
| Searchability | 5% |

---

## Review Process

### Self-Review

Before submission, verify:

- [ ] All sections present
- [ ] Code examples tested
- [ ] Links validated
- [ ] Format consistent
- [ ] Changelog updated

### Peer Review

Reviewer checks:

- [ ] Accuracy against docs
- [ ] Clarity of explanations
- [ ] Completeness of coverage
- [ ] Practicality of examples

### Final Approval

Required for publication:

- [ ] All KPIs green
- [ ] Peer review complete
- [ ] Examples tested
- [ ] Links verified

---

## Tracking KPIs

### Course Health Dashboard

| Course | Completeness | Accuracy | Practicality | Usability | Currency | Overall |
|--------|--------------|----------|--------------|-----------|----------|---------|
| Stripe | 100% | 95% | 90% | 85% | 80% | 91% |

### Review Schedule

| Review Type | Frequency | Who |
|-------------|-----------|-----|
| Quick check | Monthly | Author |
| Full review | Quarterly | Team |
| Major update | Per release | Expert |

---

## Improving KPIs

### If Completeness is Low

- Add missing sections
- Expand existing content
- Add more examples

### If Accuracy is Low

- Re-verify against docs
- Update outdated info
- Fix errors

### If Practicality is Low

- Test all code
- Add missing imports
- Make examples self-contained

### If Usability is Low

- Improve navigation
- Add table of contents
- Clarify explanations

### If Currency is Low

- Review for updates
- Check for new versions
- Update changelog

---

## KPI Automation

### Automated Checks

```yaml
# .github/workflows/course-quality.yml
name: Course Quality Check
on: [pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Check structure
        run: ./scripts/check-structure.sh
      - name: Validate links
        run: ./scripts/validate-links.sh
      - name: Test code examples
        run: ./scripts/test-examples.sh
```

### Manual Checks

- Expert accuracy review
- Usability testing
- Content quality review

---

*Document version: 1.0*
*Last updated: 2026-03-06*
