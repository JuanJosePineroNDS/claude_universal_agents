# CLAUDE.md — Universal Superagent

This file provides complete guidance to Claude Code when working with this repository or any project using this plugin.

## Auto-Detection: What to Do at Session Start

At the beginning of every session, scan the project directory and activate the appropriate agents based on detected files:

| If you find... | Activate |
|---|---|
| `*.php`, `composer.json` | `php-reviewer`, `security-reviewer` |
| `artisan`, `laravel/framework` | `php-reviewer`, `database-reviewer` |
| `*.tsx`, `*.jsx`, React in package.json | `react-reviewer`, `typescript-reviewer` |
| Next.js in package.json | `react-reviewer`, `typescript-reviewer` |
| Express/Fastify/NestJS in package.json | `nodejs-reviewer`, `typescript-reviewer` |
| `*.py`, `requirements.txt` | `python-reviewer`, `security-reviewer` |
| `django` or `fastapi` in requirements | `python-reviewer`, `database-reviewer` |
| `go.mod` | `go-reviewer`, `go-build-resolver` |
| `Cargo.toml` | `rust-reviewer`, `rust-build-resolver` |
| `pom.xml`, `build.gradle` | `java-reviewer`, `java-build-resolver` |
| `*.kt`, `build.gradle.kts` | `kotlin-reviewer`, `kotlin-build-resolver` |
| `Dockerfile`, `.github/workflows/` | `devops-agent` |
| `prisma/`, `*.sql migrations` | `database-reviewer` |
| Unknown/new project | `onboarding-agent` (run `/onboard` first) |

If the stack is unclear, **always run `/onboard` first** before doing any other work.

---

## Agents Directory (33 total)

### Planning & Architecture
| Agent | When to Use |
|---|---|
| `architect` | System design, major architectural decisions, scalability planning |
| `planner` | Before implementing any feature with 3+ steps — creates detailed plans |
| `onboarding-agent` | First time in any new/unfamiliar codebase — auto-detects stack |

### Code Review (by language/framework)
| Agent | Language/Framework |
|---|---|
| `react-reviewer` | React 18+, Next.js, hooks, a11y, performance |
| `nodejs-reviewer` | Node.js backends, Express, Fastify, NestJS, REST APIs |
| `typescript-reviewer` | TypeScript/JavaScript (generic), type safety |
| `python-reviewer` | Python 3+, Django, FastAPI, Flask |
| `php-reviewer` | PHP 8+, Laravel, Composer |
| `go-reviewer` | Go, idiomatic patterns, concurrency |
| `rust-reviewer` | Rust, ownership, lifetimes, unsafe |
| `java-reviewer` | Java, Spring Boot, JPA |
| `kotlin-reviewer` | Kotlin, Android, KMP, Compose |
| `cpp-reviewer` | C++, memory safety, modern C++ |
| `flutter-reviewer` | Flutter, Dart, widget best practices |
| `code-reviewer` | General review when language is mixed or unclear |
| `security-reviewer` | Security audit for ANY language — OWASP, secrets, injection |
| `database-reviewer` | PostgreSQL, Supabase, query optimization |

### Build Error Resolution
| Agent | When to Use |
|---|---|
| `build-error-resolver` | TypeScript/generic build errors |
| `go-build-resolver` | Go compiler errors, go vet |
| `rust-build-resolver` | Cargo errors, borrow checker |
| `java-build-resolver` | Maven/Gradle compilation errors |
| `kotlin-build-resolver` | Kotlin/Gradle errors |
| `cpp-build-resolver` | CMake/linker errors |
| `pytorch-build-resolver` | CUDA, tensor errors, DataLoader |

### Testing & Quality
| Agent | When to Use |
|---|---|
| `tdd-guide` | Implementing features test-first (Red-Green-Refactor) |
| `e2e-runner` | End-to-end tests with Playwright |

### DevOps & Infrastructure
| Agent | When to Use |
|---|---|
| `devops-agent` | Docker, GitHub Actions, CI/CD, deployments |

### Documentation & Knowledge
| Agent | When to Use |
|---|---|
| `doc-updater` | After major changes — updates README, codemaps, API docs |
| `docs-lookup` | Looking up current library documentation via Context7 |

### Automation & Operations
| Agent | When to Use |
|---|---|
| `loop-operator` | Long autonomous task loops with stop conditions |
| `refactor-cleaner` | Dead code, duplicates, unused exports cleanup |
| `harness-optimizer` | Improve agent configuration for cost/speed |
| `chief-of-staff` | Email/Slack/LINE triage (personal productivity) |

---

## Standard Development Workflow

Follow this workflow for ALL features:

```
1. DISCOVER  →  /onboard (new project) or read PROJECT-CONTEXT.md
2. PLAN      →  /plan (for any feature with 3+ steps)
3. IMPLEMENT →  Write code (TDD preferred: /tdd)
4. REVIEW    →  Auto-triggers: code-reviewer, language-specific reviewer
5. TEST      →  /tdd or /e2e for E2E tests
6. DEPLOY    →  /deploy (uses devops-agent)
7. LEARN     →  /learn (extract reusable patterns from session)
```

**CRITICAL RULE**: Never write code without running `/plan` first for anything non-trivial. Never merge code without running the appropriate reviewer agent.

---

## Slash Commands Reference (68 total)

### Essential Daily Commands
```
/plan           → Create implementation plan (ALWAYS before complex work)
/code-review    → Full security + quality review of changes
/tdd            → Test-driven development workflow
/e2e            → Generate and run E2E tests
/build-fix      → Fix build/compilation errors
/docs           → Look up current library documentation
```

### New Commands (added by this config)
```
/onboard        → Analyze new repo, detect stack, generate PROJECT-CONTEXT.md
/new-project    → Create a new project from scratch with full tooling
/deploy         → Deploy the project to configured target
/audit          → Full security + quality + dependency audit
```

### Language-Specific Build & Review
```
/python-review  → Python code review
/go-build       → Fix Go build errors
/go-review      → Go code review
/go-test        → Run Go tests
/rust-build     → Fix Rust/Cargo errors
/rust-review    → Rust code review
/rust-test      → Run Rust tests
/java-review    → Java/Spring Boot review (use java-reviewer agent)
/kotlin-build   → Fix Kotlin/Gradle errors
/kotlin-review  → Kotlin code review
/cpp-build      → Fix C++ build errors
/cpp-review     → C++ code review
/gradle-build   → Fix Java/Gradle build errors
```

### Session & Context Management
```
/save-session   → Save current session state
/resume-session → Resume previous session
/sessions       → List saved sessions
/checkpoint     → Create a checkpoint in current task
/context-budget → Analyze remaining context window
/aside          → Temporary task within current session
```

### Documentation & Learning
```
/update-docs    → Update project documentation
/update-codemaps→ Update codebase maps
/learn          → Extract reusable patterns from session
/learn-eval     → Evaluate session for learning opportunities
/rules-distill  → Distill rules from skills
/skill-create   → Generate new skill from git history
/skill-health   → Check health of installed skills
```

### Multi-Agent Orchestration
```
/orchestrate    → Sequential or parallel agent orchestration
/devfleet       → Multi-agent via Claude DevFleet
/multi-plan     → Multi-model planning
/multi-backend  → Multi-agent backend implementation
/multi-frontend → Multi-agent frontend implementation
/multi-workflow → Multi-model full workflow
/loop-start     → Start autonomous agent loop
/loop-status    → Check loop status
```

### Quality & Auditing
```
/quality-gate   → Run all quality checks
/test-coverage  → Analyze test coverage
/verify         → General verification
/refactor-clean → Remove dead code and duplicates
/harness-audit  → Audit agent harness configuration
```

### Project Management
```
/projects       → List and manage projects
/promote        → Promote skill/agent to production
/prune          → Remove unused agents/skills
/setup-pm       → Configure package manager
/pm2            → PM2 process manager integration
/model-route    → Configure model routing
/evolve         → Evolve system based on patterns learned
```

---

## Universal Security Rules

These rules apply to ALL languages and frameworks:

### NEVER do:
- Hardcode secrets, API keys, tokens, or passwords in source code
- Use `eval()` or equivalent with user-controlled input
- Concatenate user input into SQL queries
- Trust user input without validation
- Commit `.env` files with real credentials
- Use `--no-verify` to bypass git hooks
- Deploy without running tests
- Store passwords without hashing (bcrypt/argon2 minimum)
- Use MD5 or SHA1 for security purposes
- Expose internal error details to API consumers

### ALWAYS do:
- Validate all external input (user, API, file) at system boundaries
- Use environment variables for all secrets
- Use parameterized queries for all database operations
- Add rate limiting to all public API endpoints
- Log security events (auth failures, permission violations)
- Run `npm audit` / `composer audit` / `pip audit` in CI
- Use HTTPS everywhere
- Follow least-privilege principle for service accounts

---

## Project Architecture

This plugin is organized as:

```
agents/          # 33 specialized subagents (Markdown + YAML frontmatter)
commands/        # 68 slash commands (Markdown with description frontmatter)
hooks/           # Trigger-based automations (hooks.json)
mcp-configs/     # 23 MCP server configurations
rules/           # Language-specific guidelines (common + 11 languages × 5 files)
skills/          # 150+ knowledge collections (domain expertise)
scripts/         # Cross-platform Node.js hook utilities
tests/           # Test suite
.claude/
  settings.json  # Active hooks configuration (Windows paths)
  PROJECT-CONTEXT.md  # Generated by /onboard (per-project)
```

## Running Tests

```bash
node tests/run-all.js                    # All tests
node tests/lib/utils.test.js             # Utils
node tests/lib/package-manager.test.js   # Package manager detection
node tests/hooks/hooks.test.js           # Hooks
```

## Adding New Agents

Follow this format in `agents/your-agent.md`:

```markdown
---
name: your-agent
description: One sentence. Include trigger conditions. MUST BE USED when X.
tools: ["Read", "Grep", "Glob", "Bash"]
model: sonnet
---

[Agent instructions here]
```

## Adding New Commands

Follow this format in `commands/your-command.md`:

```markdown
---
description: Brief description of what this command does
---

# Command Name

[Command instructions here]
```

## Contributing

File naming: `lowercase-with-hyphens.md`
Commit style: `conventional` (feat:, fix:, docs:, test:)
See `CONTRIBUTING.md` for full guidelines.
