---
description: Full security, quality, and dependency audit of the current project. Combines security-reviewer, code-reviewer, and dependency scanning for a comprehensive health report.
---

# Audit Command

Runs a comprehensive audit of the project combining multiple specialized agents for security, code quality, and dependency health.

## What This Command Does

1. **Security scan** — OWASP Top 10, injection vulnerabilities, hardcoded secrets, insecure patterns
2. **Dependency audit** — Known CVEs in packages (`npm audit`, `composer audit`, `pip audit`, `cargo audit`)
3. **Code quality scan** — Dead code, complexity, missing tests, console.log/debug statements
4. **Configuration audit** — CORS, CSP headers, environment variable exposure
5. **Authentication review** — Auth flows, session management, password policies
6. **Infrastructure review** — Docker security, exposed ports, secrets in CI/CD configs
7. **Generates audit report** saved to `.claude/AUDIT-REPORT.md`

## Severity Levels

| Level | Meaning | Action Required |
|---|---|---|
| CRITICAL | Must fix before next deploy | Block deployment immediately |
| HIGH | Fix within 24 hours | Fix before merging PRs |
| MEDIUM | Fix within 1 week | Track in backlog |
| LOW | Best practice improvement | Fix when convenient |
| INFO | Informational only | No action needed |

## What Gets Scanned

### Security (via `security-reviewer`)
- Hardcoded credentials, API keys, tokens in source
- SQL/NoSQL injection vulnerabilities
- XSS vulnerabilities (innerHTML, dangerouslySetInnerHTML)
- Command injection (shell_exec, exec, child_process)
- Path traversal attacks
- CSRF missing on state-changing routes
- Insecure direct object references (IDOR)
- Weak cryptography (MD5, SHA1 for security, plain password storage)
- Prototype pollution
- Open redirect vulnerabilities
- Missing input validation

### Dependencies
```bash
npm audit --audit-level=moderate      # Node.js
composer audit                         # PHP/Composer
pip audit                              # Python
cargo audit                            # Rust
bundle audit                           # Ruby
```

### Code Quality (via `code-reviewer`)
- Functions > 50 lines
- Files > 800 lines
- Nesting depth > 4 levels
- Missing error handling
- Debug statements left in code (`console.log`, `dd()`, `var_dump()`, `print()`)
- TODO/FIXME/HACK comments that block production
- Missing tests for new code
- Test coverage below threshold

### Infrastructure & Configuration
- `.env` files in version control
- Sensitive data in `Dockerfile` layers
- Secrets in GitHub Actions YAML (not using secrets)
- Open ports in `docker-compose.yml`
- Missing `HEALTHCHECK` in Dockerfile
- Missing security headers (Helmet, CSP, HSTS)

## Language-Specific Checks

**PHP/Laravel:**
- Mass assignment vulnerability (missing `$fillable`/`$guarded`)
- Raw SQL queries with user input
- Missing CSRF middleware on POST routes
- Outdated Composer packages with CVEs

**React/Next.js:**
- XSS via `dangerouslySetInnerHTML`
- Client-side secrets (API keys in frontend code)
- Missing Content Security Policy headers
- Unvalidated redirects

**Node.js/Express:**
- Missing `helmet()` middleware
- Missing rate limiting
- Body size limits not configured
- Error stack traces in production responses

**Python/Django:**
- `DEBUG=True` detectable in production code paths
- Missing CSRF protection
- Pickle deserialization with untrusted data
- YAML `unsafe_load` usage

## Example Output

```
# Security Audit Report — my-project
Date: 2026-03-26
Audited by: security-reviewer + code-reviewer + dependency scanner

## Summary
CRITICAL: 1
HIGH: 3
MEDIUM: 7
LOW: 12
Total issues: 23

## CRITICAL Issues (Must fix before next deploy)

[CRITICAL] Hardcoded database password
File: config/database.php:15
Issue: Database password hardcoded in source code
Fix: Move to .env file, use env('DB_PASSWORD')

## HIGH Issues

[HIGH] SQL injection via raw query
File: app/Http/Controllers/UserController.php:67
Issue: User input concatenated into raw SQL query
Fix: Use DB::select('SELECT * FROM users WHERE id = ?', [$id])

[HIGH] npm audit: 2 high severity vulnerabilities
Package: lodash@4.17.15 — Prototype Pollution (CVE-2021-23337)
Fix: npm update lodash

## MEDIUM Issues
[7 issues listed...]

## Dependency Health
npm: 1 critical, 2 high, 5 moderate (run: npm audit fix)
composer: 0 critical, 1 high (run: composer audit)

## Configuration
✅ .env not in git history
✅ No hardcoded secrets in CI/CD
⚠️ Missing Content-Security-Policy header
⚠️ Missing rate limiting on /api/auth/login

## Recommended Next Steps
1. Fix CRITICAL issue immediately (database.php:15)
2. Run: npm audit fix --force
3. Add CSP headers via helmet()
4. Add rate limiting to auth endpoints
```

## Audit Frequency Recommendations

- **Before every production deploy**: At minimum, run `npm audit` + `security-reviewer`
- **Weekly**: Full `/audit` on active projects
- **Monthly**: Full audit including infrastructure review
- **After adding dependencies**: Always run dependency audit

## Saving the Report

The audit report is automatically saved to `.claude/AUDIT-REPORT.md` with a timestamp. Track it in git to maintain an audit history.

## Related Commands

- `/code-review` — Quick code quality review (subset of audit)
- `/deploy` — Deploy (auto-runs security checks)
- `/security-review` — Security-only review via security-reviewer agent

## Related Agents

This command combines:
- `security-reviewer` (`agents/security-reviewer.md`)
- `code-reviewer` (`agents/code-reviewer.md`)
- Language-specific reviewers based on detected stack
