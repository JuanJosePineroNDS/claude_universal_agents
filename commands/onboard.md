---
description: Analyze a new or unfamiliar repository, auto-detect the technology stack, generate complete project context, and brief all agents. Run this FIRST in any new project.
---

# Onboard Command

This command invokes the **onboarding-agent** to analyze the current repository and generate a complete project context report.

## What This Command Does

1. **Auto-detects the stack** — Languages, frameworks, databases, infrastructure
2. **Maps directory structure** — Entry points, key files, module organization
3. **Audits quality baseline** — Tests, linting, CI/CD, TypeScript, coverage
4. **Scans dependencies** — Package manager, versions, potential vulnerabilities
5. **Generates `PROJECT-CONTEXT.md`** — Saved to `.claude/` for future sessions
6. **Recommends agents** — Lists which reviewers and tools to activate for this stack
7. **Provides quick-start commands** — Dev, test, build, lint commands for this project

## When to Use

Use `/onboard` when:
- Starting work on a new repository for the first time
- Returning to a project after a long absence
- Handed an unfamiliar codebase without documentation
- Onboarding a new team member's project
- Switching between multiple projects

**Always run `/onboard` before `/plan` in a new project.**

## How It Works

The onboarding-agent will:

1. **Scan for language indicators**: `package.json`, `composer.json`, `go.mod`, `Cargo.toml`, `requirements.txt`, `pom.xml`, `*.csproj`
2. **Detect frameworks**: Check dependency names for React, Next.js, Laravel, Django, Spring Boot, Express, etc.
3. **Identify databases**: Prisma schema, migration folders, ORM packages
4. **Check infrastructure**: Dockerfile, docker-compose.yml, `.github/workflows/`, `vercel.json`, `railway.json`
5. **Run quality scan**: Count test files, check linting configs, verify CI/CD exists
6. **Generate structured report** saved to `.claude/PROJECT-CONTEXT.md`

## Example Output

```
## Stack Detected
- Language: TypeScript 5.x + PHP 8.2
- Frontend: Next.js 14 (App Router) + React 18
- Backend: Laravel 11 API (separate repo)
- Database: PostgreSQL via Prisma + Eloquent
- Auth: NextAuth.js (frontend) + Laravel Sanctum (API)
- Testing: Jest + Playwright (frontend), Pest (backend)
- CI/CD: GitHub Actions → Vercel (frontend) + Railway (backend)

## Agents to Activate
Priority order for this project:
1. react-reviewer — All component changes
2. nodejs-reviewer — Next.js API routes / server actions
3. php-reviewer — Laravel API changes
4. typescript-reviewer — Type safety
5. database-reviewer — Schema changes (Prisma + Eloquent)
6. security-reviewer — Auth endpoints, API security
7. devops-agent — GitHub Actions, Docker changes

## Quality Baseline
✅ Tests: Jest (47 files) + Pest (31 files) + Playwright (12 specs)
✅ Linting: ESLint + PHP-CS-Fixer configured
✅ TypeScript: Strict mode
✅ CI/CD: GitHub Actions (ci.yml + deploy.yml)
❌ Coverage thresholds: Not configured — recommend adding 80% minimum
⚠️  npm audit: 2 moderate vulnerabilities found — run npm audit fix

## Quick Start
npm run dev          # Frontend dev server (localhost:3000)
php artisan serve    # Backend dev server (localhost:8000)
npm test             # Frontend tests
./vendor/bin/pest    # Backend tests
npm run build        # Production build
```

## After Running /onboard

The generated `.claude/PROJECT-CONTEXT.md` is automatically available to all subsequent agents, so they don't need to re-explore the codebase from scratch.

Next steps after onboarding:
- Use `/plan` to plan your first feature
- Use `/audit` if you suspect security or dependency issues
- Use the recommended agents for code reviews

## Related Commands

- `/plan` — Plan a feature after onboarding
- `/audit` — Full security and quality audit
- `/new-project` — Create a brand new project

## Related Agents

This command invokes the `onboarding-agent`.
Agent source: `agents/onboarding-agent.md`
