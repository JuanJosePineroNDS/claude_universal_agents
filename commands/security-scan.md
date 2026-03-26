---
description: Run a comprehensive security scan covering OWASP Top 10, secrets, and dependency vulnerabilities
---

# Security Scan

Run a full security audit of the project using the `security-reviewer` agent.

## Workflow

1. **Secrets detection**: Scan for hardcoded API keys, tokens, passwords
   ```bash
   grep -rn "password\|secret\|api_key\|token\|private_key" --include="*.{js,ts,py,php,rb,go,java,kt}" .
   ```

2. **Dependency vulnerabilities**:
   - Node.js: `npm audit` / `pnpm audit`
   - Python: `pip audit` / `safety check`
   - PHP: `composer audit`
   - Ruby: `bundle audit`
   - Go: `govulncheck ./...`
   - Java: `mvn dependency-check:check`

3. **OWASP Top 10 review**:
   - Injection (SQL, NoSQL, command, LDAP)
   - Broken authentication
   - Sensitive data exposure
   - XXE, SSRF
   - Security misconfiguration
   - XSS
   - Insecure deserialization
   - Insufficient logging

4. **Infrastructure security**:
   - Docker: no root user, minimal base image, no secrets in layers
   - CI/CD: secrets in environment, no plain text
   - CORS, CSP, HSTS headers configured

5. **Report**: Generate findings with severity (critical/high/medium/low), affected file:line, and remediation steps

## Usage

```
/security-scan              # Full project scan
/security-scan src/auth/    # Scan specific directory
```
