# Vulnerable Dependencies

Using outdated or vulnerable third-party libraries exposes applications to known security issues.

---

## Severity

**HIGH** - Known CVEs can be easily exploited.

---

## The Problem

```
STATISTICS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

- 77% of applications have vulnerable dependencies
- Average app has 500+ dependencies
- New vulnerabilities discovered daily
```

---

## Detection

### npm audit

```bash
# Check for vulnerabilities
npm audit

# Check production dependencies
npm audit --production
```

### tools

```bash
# Snyk
npx snyk test

# GitHub Dependabot
# Automatic alerts on PRs

# npm audit fix
npm audit fix
```

---

## Prevention

### 1. Regular Updates

```bash
# Update dependencies regularly
npm update

# Use latest versions
npm-check-updates -u
npm install
```

### 2. Lock Versions

```json
// package-lock.json ensures exact versions
{
  "dependencies": {
    "express": "4.18.2"
  }
}
```

### 3. Automated Scanning

```yaml
# GitHub Dependabot
# .github/dependabot.yml

version: 2
updates:
  - package-ecosystem: npm
    directory: "/"
    schedule:
      interval: "weekly"
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
