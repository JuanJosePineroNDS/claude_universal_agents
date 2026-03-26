---
name: deploy-checklist
description: Universal deployment checklist for production releases — pre-deploy, deploy, post-deploy verification
origin: manual
---

# Deploy Checklist

## When to Activate

- Deploying any application to production or staging
- Setting up CI/CD pipelines
- Reviewing deployment procedures
- Post-incident when deployment process needs improvement

## Pre-Deploy Checklist

### Code Quality
- [ ] All tests pass (unit, integration, E2E)
- [ ] No lint errors or type errors
- [ ] Code review approved
- [ ] No `TODO` or `FIXME` in critical paths
- [ ] No debug/console.log statements in production code
- [ ] Feature flags configured for gradual rollout

### Security
- [ ] No secrets in code or config files
- [ ] Dependency audit clean (`npm audit`, `pip audit`, etc.)
- [ ] HTTPS enforced
- [ ] CORS configured correctly
- [ ] Rate limiting enabled on public endpoints
- [ ] Security headers set (CSP, HSTS, X-Frame-Options)

### Database
- [ ] Migrations tested against staging data
- [ ] Migrations are reversible
- [ ] No breaking schema changes without backward compatibility
- [ ] Backup taken before destructive migrations
- [ ] Index changes tested for performance impact

### Infrastructure
- [ ] Environment variables set in target environment
- [ ] Health check endpoint responds correctly
- [ ] Resource limits configured (CPU, memory)
- [ ] Auto-scaling rules verified
- [ ] SSL certificates valid and not expiring soon

## Deploy Process

### Strategy Selection
| Strategy | When to Use | Risk | Rollback Speed |
|----------|-------------|------|----------------|
| **Rolling** | Default for most deployments | Low | Medium |
| **Blue-Green** | Zero-downtime critical services | Low | Instant |
| **Canary** | High-risk changes, large user base | Very Low | Instant |
| **Recreate** | Dev/staging only, acceptable downtime | High | Slow |

### Deploy Steps
1. Notify team in deployment channel
2. Create deployment tag/release
3. Deploy to staging first, verify
4. Deploy to production using selected strategy
5. Monitor error rates and latency for 15 minutes
6. Confirm deployment or rollback

## Post-Deploy Verification

### Immediate (0-5 minutes)
- [ ] Application starts and health check passes
- [ ] Key user flows work (login, core feature, payment)
- [ ] Error rate not elevated vs. baseline
- [ ] Response times within SLA
- [ ] No increase in 5xx errors

### Short-term (5-60 minutes)
- [ ] Monitor dashboards for anomalies
- [ ] Check logs for new error patterns
- [ ] Verify background jobs processing
- [ ] Confirm scheduled tasks running
- [ ] Check third-party integrations

### Rollback Criteria
Rollback immediately if ANY of:
- Error rate >2x baseline
- P99 latency >3x baseline
- Critical user flow broken
- Data integrity issue detected
- Security vulnerability discovered

## Rollback Procedure

1. Trigger rollback (revert deploy, swap blue/green, drain canary)
2. Verify rollback successful (health check + key flows)
3. Notify team with incident details
4. Create post-mortem document
5. Fix root cause before re-deploying
