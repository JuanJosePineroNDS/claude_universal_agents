# CLAUDE.md — Universal Superagent

This file provides complete guidance to Claude Code when working with this repository or any project using this plugin.

## Role

This is a **universal development superagent** — a complete AI-powered development team that auto-detects your stack and activates the right experts for every task. It covers the full lifecycle: planning, implementation, review, testing, deployment, and learning.

## Workflows

### New Project
```
/new-project → /plan → implement (TDD) → /code-review → /deploy
```

### Existing Project
```
/onboard → read PROJECT-CONTEXT.md → /plan → implement → /code-review → /test → /deploy → /learn
```

### Bug Fix
```
/fix "error description" → diagnose → write regression test → fix → /code-review
```

### Optimization
```
/optimize → measure baseline → apply changes → verify improvement → /code-review
```

## Auto-Detection: What to Do at Session Start

At the beginning of every session, scan the project directory and activate the appropriate agents based on detected files:

| If you find... | Activate |
|---|---|
| `*.php`, `composer.json` | `php-reviewer`, `security-reviewer` |
| `artisan`, `laravel/framework` | `laravel-expert`, `database-reviewer` |
| `*.tsx`, `*.jsx`, React in package.json | `react-reviewer`, `typescript-reviewer` |
| Next.js in package.json | `react-reviewer`, `typescript-reviewer` |
| Vue in package.json | `vue-expert`, `typescript-reviewer` |
| Angular in package.json | `angular-expert`, `typescript-reviewer` |
| Express/Fastify/NestJS in package.json | `nodejs-reviewer`, `typescript-reviewer` |
| `*.py`, `requirements.txt` | `python-reviewer`, `security-reviewer` |
| `django` or `fastapi` in requirements | `python-reviewer`, `database-reviewer` |
| `Gemfile`, `*.rb` | `ruby-expert`, `security-reviewer` |
| `go.mod` | `go-reviewer`, `go-build-resolver` |
| `Cargo.toml` | `rust-reviewer`, `rust-build-resolver` |
| `pom.xml`, `build.gradle` | `java-reviewer`, `java-build-resolver` |
| `*.kt`, `build.gradle.kts` | `kotlin-reviewer`, `kotlin-build-resolver` |
| `pubspec.yaml`, Flutter | `flutter-expert` |
| `ionic.config.json`, Capacitor | `ionic-expert` |
| `Dockerfile`, `.github/workflows/` | `devops-agent` |
| `*.tf`, Terraform/OpenTofu | `terraform-expert` |
| Kubernetes manifests, Helm | `kubernetes-expert` |
| `prisma/`, `*.sql migrations` | `database-reviewer` |
| `mongosh`, `mongoose` in package.json | `mongodb-expert` |
| Redis client in dependencies | `redis-expert` |
| Unknown/new project | `onboarding-agent` (run `/onboard` first) |

If the stack is unclear, **always run `/onboard` first** before doing any other work.

---

## Agents Directory (45 total)

### Planning & Architecture
| Agent | When to Use |
|---|---|
| `architect` | System design, major architectural decisions, scalability planning |
| `arquitecto-sistemas` | Microservices, monoliths, APIs, DDD, service decomposition |
| `planner` | Before implementing any feature with 3+ steps — creates detailed plans |
| `onboarding-agent` | First time in any new/unfamiliar codebase — auto-detects stack |

### Code Review (by language/framework)
| Agent | Language/Framework |
|---|---|
| `react-reviewer` | React 18+, Next.js, hooks, a11y, performance |
| `vue-expert` | Vue 3, Composition API, Pinia, Nuxt 3 |
| `angular-expert` | Angular 17+, signals, standalone components, RxJS |
| `nodejs-reviewer` | Node.js backends, Express, Fastify, NestJS, REST APIs |
| `typescript-reviewer` | TypeScript/JavaScript (generic), type safety |
| `python-reviewer` | Python 3+, Django, FastAPI, Flask |
| `php-reviewer` | PHP 8+, Laravel, Composer |
| `laravel-expert` | Laravel 11+, Eloquent, Livewire, Sanctum |
| `ruby-expert` | Ruby, Rails 7+, Hotwire, Active Record |
| `go-reviewer` | Go, idiomatic patterns, concurrency |
| `rust-reviewer` | Rust, ownership, lifetimes, unsafe |
| `java-reviewer` | Java, Spring Boot, JPA |
| `kotlin-reviewer` | Kotlin, Android, KMP, Compose |
| `cpp-reviewer` | C++, memory safety, modern C++ |
| `flutter-reviewer` | Flutter, Dart, widget best practices |
| `flutter-expert` | Flutter advanced: Riverpod, Bloc, platform channels |
| `ionic-expert` | Ionic, Capacitor, hybrid cross-platform apps |
| `code-reviewer` | General review when language is mixed or unclear |
| `security-reviewer` | Security audit for ANY language — OWASP, secrets, injection |

### Database
| Agent | When to Use |
|---|---|
| `database-reviewer` | PostgreSQL, Supabase, query optimization |
| `mongodb-expert` | MongoDB schema design, aggregation, indexing |
| `redis-expert` | Redis caching, pub/sub, streams, data structures |
| `elasticsearch-expert` | Elasticsearch/OpenSearch, mappings, queries, aggregations |

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
| `kubernetes-expert` | K8s manifests, Helm charts, cluster management |
| `terraform-expert` | Infrastructure-as-code, multi-cloud provisioning |

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

## Slash Commands Reference (72 total)

### Essential Daily Commands
```
/plan           → Create implementation plan (ALWAYS before complex work)
/code-review    → Full security + quality review of changes
/fix            → Diagnose bugs with root cause analysis and regression test
/improve        → Analyze and improve code quality and performance
/test           → Run test suite and generate missing tests
/tdd            → Test-driven development workflow
/e2e            → Generate and run E2E tests
/build-fix      → Fix build/compilation errors
/docs           → Look up current library documentation
/optimize       → Profile and optimize performance
```

### New Commands (added by this config)
```
/onboard        → Analyze new repo, detect stack, generate PROJECT-CONTEXT.md
/new-project    → Create a new project from scratch with full tooling
/deploy         → Deploy the project to configured target
/audit          → Full security + quality + dependency audit
/db-setup       → Initialize database schema, migrations, and seed data
/security-scan  → Full OWASP + secrets + dependency vulnerability scan
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
/java-review    → Java/Spring Boot review
/java-build     → Fix Java/Maven/Gradle build errors
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
agents/          # 33 base subagents (Markdown + YAML frontmatter)
.claude/agents/  # 12 extended expert agents
commands/        # 72 slash commands (Markdown with description frontmatter)
hooks/           # Trigger-based automations (hooks.json)
mcp-configs/     # 24 MCP server configurations
rules/           # Language-specific guidelines (common + 11 languages × 5 files)
skills/          # 125 base + 10 extended knowledge collections
.claude/skills/  # Extended skills (cloud, database, backend patterns)
scripts/         # Cross-platform Node.js hook utilities
tests/           # Test suite
.claude/
  settings.json  # Active hooks configuration
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

## Conventional Commits (MANDATORY)

All commits MUST follow this format:

```
<type>(<scope>): <description>

[optional body]

Co-Authored-By: Claude Opus 4.6 (1M context) <noreply@anthropic.com>
```

**Types:** `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `perf`, `ci`
**Scopes:** `agents`, `commands`, `skills`, `hooks`, `rules`, `scripts`, `docs`

**Examples:**
- `feat(agents): add kubernetes-expert and terraform-expert agents`
- `fix(commands): add missing frontmatter to build-fix command`
- `docs: sync AGENTS.md counts with actual file count`

## Contributing

File naming: `lowercase-with-hyphens.md`
Commit style: `conventional` (see above)
See `CONTRIBUTING.md` for full guidelines.
