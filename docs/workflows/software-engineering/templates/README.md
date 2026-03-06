# Repository Templates

Standard templates for creating new projects.

---

## Available Templates

| Template | Description | Use For |
|----------|-------------|---------|
| `project-template` | Full web application | Most projects |
| `project-template-api` | Backend only | API services |
| `project-template-library` | Shared package | npm packages |
| `project-template-cli` | CLI tool | Developer tools |

---

## project-template

Full Next.js application template with:

- Next.js 14 + React + TypeScript
- Drizzle ORM + PostgreSQL
- Tailwind CSS
- ESLint + Prettier + TypeScript strict
- CI/CD workflows
- AI agent configuration

### Directory Structure

```
project-template/
├── .ai/
│   ├── rules.md
│   ├── prompts/
│   └── context.md
├── .github/
│   ├── ISSUE_TEMPLATE/
│   ├── PULL_REQUEST_TEMPLATE/
│   └── workflows/
├── src/
│   ├── app/
│   ├── features/
│   ├── lib/
│   └── db/
├── tests/
├── package.json
├── tsconfig.json
├── eslint.config.js
├── tailwind.config.ts
├── drizzle.config.ts
└── .env.example
```

---

## Usage

### GitHub

1. Go to `nesalia/project-template`
2. Click "Use this template"
3. Name your repository
4. Clone and start

### CLI

```bash
git clone git@github.com:nesalia/project-template.git my-new-repo
cd my-new-repo
rm -rf .git
git init
git remote add origin git@github.com:nesalia/my-new-repo.git

# Update package.json name
# Update README.md

npm install
npm run dev
```

---

## Required Customization

After cloning, update:

```markdown
# README.md
- Project name
- Description

# package.json
- name
- description
- repository

# .env.example
- Add project-specific variables

# .ai/context.md
- Project description
- Tech stack details
```

---

## CI Verification

After setup, verify everything works:

```bash
npm install
npm run ci

# Should output:
# ✓ lint passed
# ✓ typecheck passed
# ✓ tests passed
# ✓ build succeeded
```

---

## Next Steps

1. Add project-specific features
2. Configure environment variables
3. Set up database (if needed)
4. Deploy to hosting

---

*Document version: 1.0*
*Last updated: 2026-03-06*
