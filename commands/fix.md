---
description: Diagnose and fix bugs with root cause analysis and regression test
---

# Fix

Diagnose a bug, find the root cause, fix it, and add a regression test.

## Workflow

1. **Understand the bug**: Read the error message, stack trace, or user description
2. **Reproduce**: Identify the minimal steps or input that triggers the bug
3. **Locate root cause**:
   - Search for the error message in codebase
   - Trace the call stack from the error point
   - Check recent changes (`git log --oneline -10`)
   - Identify the exact line(s) causing the issue
4. **Write a failing test** that reproduces the bug (TDD approach)
5. **Fix** with the minimal change that addresses the root cause
6. **Verify**:
   - The failing test now passes
   - All existing tests still pass
   - No new warnings introduced
7. **Document** the fix in the commit message (what was wrong, why, how fixed)

## Rules

- Always understand the root cause before changing code
- Prefer targeted fixes over broad refactors
- Add a regression test for every bug fix
- Never suppress errors — fix them
- Check for similar bugs elsewhere in the codebase

## Usage

```
/fix "TypeError: Cannot read property 'id' of undefined"
/fix                    # Fix based on current error context
```
