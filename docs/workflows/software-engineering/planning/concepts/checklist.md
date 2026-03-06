# Checklist Categories

Comprehensive checklist for complex plans.

---

## 5. Database

**Why**: Forgot indexes = slow queries. Forgot migrations = broken deploys.

| Check | Meaning |
|-------|---------|
| Migration needed? | Does this need a DB migration? |
| New table? | Adding new table? |
| Indexes needed? | Will queries be slow without indexes? |
| Query performance | Any N+1 or heavy queries? |
| Foreign keys | Relationships defined? |

---

## 6. Security

**Why**: Security is not optional.

| Check | Meaning |
|-------|---------|
| Input validation | Validate all user input |
| Authentication | Who can access? |
| Authorization | What can they do? |
| No secrets | No API keys in code |
| SQL injection | Use parameterized queries |
| XSS | Escape output |

---

## 7. Error Handling

**Why**: Users see errors. Logs help debugging.

| Check | Meaning |
|-------|---------|
| Error types | Use Result<T, E> |
| User-friendly | Don't show stack traces |
| Logging | For debugging |
| HTTP codes | 404, 401, 500 handled |

---

## 8. Testing

**Why**: Tests catch regressions.

| Check | Meaning |
|-------|---------|
| Unit tests | Test logic in isolation |
| Integration | Test with DB |
| E2E | Test user flows |
| Affected code | Test things that might break |

---

## 9. Documentation

**Why**: Others need to use and maintain this.

| Check | Meaning |
|-------|---------|
| README | Basic usage |
| API docs | Endpoint documentation |
| Migration | How to migrate if breaking |
| Env vars | Configuration |

---

## 10. Configuration

**Why**: Config should not be hardcoded.

| Check | Meaning |
|-------|---------|
| Env vars | Use environment variables |
| Defaults | Sensible defaults |
| Validation | Validate config |

---

## 11. Breaking Changes

**Why**: Breaking changes need migration paths.

| Check | Meaning |
|-------|---------|
| API change | Different response format? |
| Database | Schema change? |
| Config | Different config format? |
| Migration | How to migrate? |

---

## 12. Performance

**Why**: Slow features = bad UX.

| Check | Meaning |
|-------|---------|
| N+1 | Avoid N+1 queries |
| Indexes | Add where needed |
| Caching | Cache expensive ops |
| Pagination | Don't load all data |

---

## 13. Monitoring

**Why**: Know when things break.

| Check | Meaning |
|-------|---------|
| Logging | Log important events |
| Metrics | Track usage |
| Alerts | Get notified on failure |

---

## 14. Impact Analysis

| Check | Meaning |
|-------|---------|
| Conflicts | What might conflict? |
| Dependencies | What depends on this? |
| Risks | What could go wrong? |
| Similar code | Existing code to reference |

---

## Summary

| Category | Questions to Answer |
|----------|-------------------|
| Database | Migration, tables, indexes, performance |
| Security | Validation, auth, secrets, injection |
| Error Handling | Types, messages, logging |
| Testing | Unit, integration, affected |
| Documentation | README, API, migration, env |
| Configuration | Env vars, defaults, validation |
| Breaking Changes | API, DB, config, migration |
| Performance | N+1, indexes, caching |
| Monitoring | Logging, metrics, alerts |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
