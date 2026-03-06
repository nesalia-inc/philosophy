# Project Initialization

How to properly initialize a new project using the standard template.

---

## Why Templates Matter

Without a template, each project starts from scratch:

- **Inconsistency**: Every project has different structure
- **Lost time**: Re-configuring the same things
- **Errors**: Missing important setup (linting, types, etc.)
- **Onboarding**: New projects = new learning curve

With a template:

- **Consistency**: Same structure everywhere
- **Speed**: Start in 5 minutes, not 2 hours
- **Reliability**: Proven to work
- **AI-Ready**: Agents know where to find everything

---

## Repository Template Structure

```
project-template/
├── .ai/                          # AI agent configuration
│   ├── rules.md                   # Coding rules (24 rules)
│   ├── prompts/                   # Standard prompts
│   │   ├── new-feature.md
│   │   ├── bug-fix.md
│   │   └── review.md
│   └── context.md                 # Project context template
├── .github/                       # GitHub configuration
│   ├── ISSUE_TEMPLATE/            # Issue templates
│   ├── PULL_REQUEST_TEMPLATE/     # PR templates
│   ├── workflows/                 # CI/CD workflows
│   │   ├── ci.yml
│   │   ├── release.yml
│   │   └── triage.yml
│   └── CODEOWNERS                 # Review rules
├── .vscode/                       # VS Code settings
│   ├── settings.json
│   └── extensions.json
├── src/                          # Source code structure
│   ├── index.ts
│   └── (standard directories)
├── tests/                        # Test structure
│   └── (standard test files)
├── docs/                         # Documentation
│   ├── README.md
│   └── (project-specific docs)
├── scripts/                      # Build/deploy scripts
├── package.json                   # Dependencies
├── tsconfig.json                  # TypeScript config
├── eslint.config.js               # Linting config
├── prettier.config.js             # Formatting config
├── .env.example                   # Environment variables template
├── .gitignore                     # Git ignore
└── README.md                      # Project README
```

---

## Creating a New Repository

### Option 1: Using the CLI (Recommended)

```bash
# Create a new project using the CLI
npx @nesalia/create my-new-project

# Specify a template
npx @nesalia/create my-api api
npx @nesalia/create my-cli cli
```

Available templates:
- `web` (default) - Full Next.js application
- `api` - Backend-only API
- `cli` - CLI tool
- `library` - Shared npm package

### Option 2: Using GitHub Template

```bash
# 1. Go to org/project-template
# 2. Click "Use this template"
# 3. Name the new repository

# Or via CLI
git clone git@github.com:nesalia/project-template.git my-new-repo
cd my-new-repo
rm -rf .git
git init
git remote add origin git@github.com:nesalia/my-new-repo.git
```

### Step 2: Configure Repository

Update these files for the new project:

```markdown
# README.md
- Update project name
- Update description
- Update tech stack

# package.json
- Update name
- Update description
- Update repository URL

# .env.example
- Add project-specific env vars
```

### Step 3: Install Dependencies

```bash
npm install
```

### Step 4: Verify Setup

```bash
# Run the full CI pipeline locally
npm run ci

# Should complete without errors
```

---

## Project Types

### Standard Stack

Most projects use:

- **Frontend**: Next.js + React + TypeScript
- **Backend**: FastAPI (Python) or API routes (Next.js)
- **Database**: PostgreSQL + Drizzle ORM
- **Styling**: Tailwind CSS

### Template Variants

| Template | Use For |
|----------|---------|
| `project-template` | Most web applications |
| `project-template-api` | Backend-only APIs |
| `project-template-mobile` | React Native apps |
| `project-template-cli` | CLI tools |
| `project-template-library` | Shared packages |

---

## Required Configuration Files

### package.json Scripts

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "eslint .",
    "typecheck": "tsc --noEmit",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "ci": "npm run lint && npm run typecheck && npm run test",
    "release": "changesets version && git add ."
  }
}
```

### tsconfig.json

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

### .gitignore

```
# Dependencies
node_modules/
.pnp
.pnp.js

# Build
.next/
out/
dist/
build/

# Environment
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Testing
coverage/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
```

---

## Verification Checklist

After initializing a new project:

- [ ] Repository created from template
- [ ] Remote URL updated
- [ ] Dependencies installed
- [ ] `npm run lint` passes
- [ ] `npm run typecheck` passes
- [ ] `npm run test` passes
- [ ] `npm run build` succeeds
- [ ] README updated with project info
- [ ] `.env.example` configured
- [ ] CI workflow runs on first push

---

## AI Agent Context

When an agent starts on a new project, it should:

1. Read `.ai/rules.md` - Understand coding standards
2. Read `.ai/prompts/` - Know the expected workflow
3. Read `README.md` - Understand the project
4. Check `.github/workflows/` - Know CI requirements
5. Explore `src/` - Understand the codebase structure

**This ensures every agent, on any project, has the same foundation.**

---

*Document version: 1.0*
*Last updated: 2026-03-06*
