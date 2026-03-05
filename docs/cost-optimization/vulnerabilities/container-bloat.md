# Container Bloat

Large container images increase deployment times and costs.

---

## Problem

```
COSTS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Large image = more storage
* More storage = higher ECR costs
* Slower deploys = longer deployments

Example:
* 2GB image = $18/month (ECR)
* 200MB image = $2/month
* Savings: $16/month per environment!
```

---

## Prevention

### 1. Use Minimal Base Image

```dockerfile
# BAD
FROM node:18

# GOOD
FROM node:18-alpine
```

### 2. Multi-stage Build

```dockerfile
# Build stage
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm ci --only=production
CMD ["node", "dist/index.js"]
```

### 3. Ignore Files

```dockerfile
# .dockerignore
node_modules
.git
*.md
tests
coverage
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
