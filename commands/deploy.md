---
description: Deploy the project to its configured platform (Vercel, Railway, VPS, Docker). Automatically detects deployment target, runs pre-deploy checks, and executes deployment.
---

# Deploy Command

Deploys the current project using the **devops-agent**. Automatically detects the deployment platform and executes the full deployment workflow with pre-flight safety checks.

## What This Command Does

1. **Detects deployment target** from config files (`vercel.json`, `railway.json`, `Dockerfile`, `Procfile`)
2. **Runs pre-deploy checks**: tests, linting, type checking, security audit
3. **Builds the project** using the detected build command
4. **Deploys to the target platform**
5. **Verifies deployment** via health check
6. **Reports deployment status** with URL and any warnings

## Pre-Deploy Safety Checks

The agent runs ALL of these before deploying. If any fail, deployment is blocked:

```
✅ Tests pass (npm test / ./vendor/bin/pest / pytest)
✅ No TypeScript errors (tsc --noEmit)
✅ No lint errors (eslint / phpstan / pylint)
✅ No CRITICAL security issues (npm audit / composer audit)
✅ .env.example is up to date (no undocumented env vars)
✅ No console.log / dd() / var_dump() left in code
✅ Build succeeds (npm run build / composer install --no-dev)
```

**You can bypass checks with `/deploy --force` (NOT recommended for production)**

## Deployment Targets

The agent auto-detects and handles:

### Vercel
```bash
# Detected by: vercel.json or "vercel" in package.json scripts
npx vercel --prod
# Agent confirms: deployment URL, function regions, edge config
```

### Railway
```bash
# Detected by: railway.json or Procfile
railway up
# Agent confirms: deployment status, service URL, logs
```

### Docker + VPS
```bash
# Detected by: Dockerfile + deployment scripts
docker build -t app:latest .
docker push registry/app:latest
# SSH deploy or kubectl rollout
```

### Manual GitHub Actions
```bash
# Detected by: .github/workflows/deploy.yml
git push origin main
# Agent monitors workflow run via gh CLI
gh run watch
```

## Deployment Workflow

```
1. Run pre-deploy checks
   ↓ (fail → stop and report)
2. Build project
   ↓ (fail → run /build-fix automatically)
3. Deploy to platform
   ↓ (fail → check logs, report root cause)
4. Wait for health check to pass (max 5 min)
   ↓ (fail → trigger rollback if possible)
5. Report: URL + deployment time + any warnings
```

## Environment Validation

Before deploying, the agent verifies:
- All variables in `.env.example` are set in the deployment environment
- No `NODE_ENV=development` or `APP_ENV=local` in production config
- Database migration has been run (if applicable)
- SSL/TLS configured for the domain

## Example Usage

```
User: /deploy

Agent:
Detecting deployment target...
✅ Found: vercel.json → Vercel deployment

Running pre-deploy checks:
✅ Tests: 47 passed, 0 failed
✅ TypeScript: No errors
✅ ESLint: No errors
✅ npm audit: 0 critical, 0 high vulnerabilities
✅ Build: Succeeded (2.3s)

Deploying to Vercel...
✅ Deployed successfully!

Production URL: https://your-app.vercel.app
Deployment time: 45s
Preview: https://your-app-git-main.vercel.app

⚠️ Warning: 2 moderate npm audit vulnerabilities — run npm audit fix soon
```

## Rollback

If deployment fails or causes issues:

```
User: /deploy --rollback

Agent:
Rolling back to previous deployment...
[Platform-specific rollback command]
✅ Rolled back to: deployment-id-xyz (2026-03-25 14:32:00)
```

## Related Commands

- `/build-fix` — Fix build errors before deploying
- `/audit` — Full security audit before deploying to production
- `/code-review` — Review changes before deploying

## Related Agents

This command invokes the `devops-agent`.
Agent source: `agents/devops-agent.md`
