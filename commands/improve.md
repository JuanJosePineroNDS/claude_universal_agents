---
description: Analyze and improve code quality, performance, and maintainability of specified files
---

# Improve

Analyze the specified code and suggest targeted improvements without changing functionality.

## Workflow

1. **Scope**: Identify target files from arguments or uncommitted changes
2. **Analyze** each file for:
   - **Performance**: unnecessary iterations, missing memoization, N+1 queries, blocking calls
   - **Readability**: unclear naming, overly complex functions (>30 lines), deep nesting (>3 levels)
   - **Maintainability**: code duplication, missing types/interfaces, tight coupling
   - **Modern patterns**: deprecated APIs, outdated syntax, missing language features
3. **Propose** improvements as a prioritized list:
   - High impact + low effort first
   - Each suggestion includes: what, why, and a code snippet
4. **Apply** approved changes incrementally with verification after each

## Rules

- Never change behavior — only improve structure and performance
- Run existing tests after each change to verify no regressions
- Respect existing code style and conventions
- One logical change per step

## Usage

```
/improve src/utils.ts        # Improve specific file
/improve                     # Improve all uncommitted changes
```
