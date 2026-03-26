---
description: Create a new project from scratch with full tooling setup. Detects desired stack from user description and scaffolds project with tests, linting, CI/CD, Docker, and security baseline.
---

# New Project Command

Creates a complete, production-ready project structure from scratch based on the specified stack.

## What This Command Does

1. **Asks the user** for project name, stack, and target deployment platform
2. **Scaffolds the project** with the correct directory structure
3. **Configures all tooling**: linting, formatting, TypeScript, testing
4. **Sets up CI/CD**: GitHub Actions for testing + deployment
5. **Creates Docker config**: Dockerfile (multi-stage) + docker-compose.yml
6. **Generates `.env.example`** with all required variables
7. **Initializes git** with `.gitignore`, conventional commit setup
8. **Runs `/onboard`** on the created project to generate context

## Supported Stacks

Ask the user which stack they want, then scaffold accordingly:

### Frontend
- **Next.js + TypeScript + Tailwind**: `npx create-next-app@latest --typescript --tailwind --app`
- **React + Vite + TypeScript**: `npm create vite@latest -- --template react-ts`
- **Vue + Nuxt 4**: `npx nuxi@latest init`

### Backend
- **Node.js + Express + TypeScript**: Manual scaffold with ts-node, zod, helmet, express-rate-limit
- **Node.js + Fastify + TypeScript**: `npm create fastify@latest`
- **Python + FastAPI**: `pip install fastapi uvicorn pydantic-settings`
- **PHP + Laravel**: `composer create-project laravel/laravel`
- **Go + Fiber/Chi**: `go mod init` + manual scaffold

### Full-Stack
- **Next.js + Prisma + PostgreSQL**: Next.js + Prisma init + database setup
- **Laravel + Inertia.js + React**: `composer create-project laravel/laravel` + Inertia setup
- **NestJS + Prisma + PostgreSQL**: `npx @nestjs/cli new`

## Project Checklist

For any new project, ensure these are configured:

```
✅ Package manager lockfile (package-lock.json, composer.lock, etc.)
✅ .gitignore (node_modules, .env, vendor/, dist/, build/)
✅ .env.example (all required variables documented, no real values)
✅ README.md (setup instructions, architecture, available scripts)
✅ Linting config (ESLint/.pylintrc/PHP-CS-Fixer/golangci-lint)
✅ Formatting config (Prettier/Black/Pint)
✅ Testing framework (Jest/pytest/Pest/go test)
✅ TypeScript strict mode (if applicable)
✅ GitHub Actions CI: lint + typecheck + test on PR
✅ GitHub Actions Deploy: deploy to target on main merge
✅ Dockerfile (multi-stage, non-root user, health check)
✅ docker-compose.yml (app + database + redis services)
✅ SECURITY.md (how to report vulnerabilities)
```

## Standard GitHub Actions CI Template

Creates `.github/workflows/ci.yml`:

```yaml
name: CI
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup (detected from stack)
        # Node: uses actions/setup-node@v4
        # Python: uses actions/setup-python@v5
        # PHP: uses shivammathur/setup-php@v2
        # Go: uses actions/setup-go@v5
      - run: [install command]
      - run: [lint command]
      - run: [typecheck command if applicable]
      - run: [test command]
      - run: [security audit command]
```

## How to Use

```
User: /new-project
Agent: What is the project name and stack?

User: "invoice-app" — Laravel API backend + Next.js frontend, PostgreSQL, deploy to Railway + Vercel

Agent:
- Creates invoice-app/ directory
- Scaffolds Laravel 11 in invoice-app/backend/
- Scaffolds Next.js 14 in invoice-app/frontend/
- Configures Prisma for frontend, Eloquent for backend
- Creates .github/workflows/ci.yml and deploy.yml
- Creates Dockerfile for each service
- Creates docker-compose.yml for local dev
- Creates .env.example for both services
- Creates root README.md
- Runs git init + initial commit
- Runs /onboard on the created project
```

## After Creating

1. The agent will provide the next steps to start developing
2. Run `/onboard` (auto-invoked) to generate full project context
3. Use `/plan` to start planning your first feature

## Related Commands

- `/onboard` — Analyze the created project
- `/plan` — Plan first feature
- `/deploy` — Deploy when ready
