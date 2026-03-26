---
description: Fix Java build errors from Maven or Gradle with minimal safe changes
---

# Java Build Fix

Incrementally fix Java build and compilation errors.

## Workflow

1. **Detect build system**: Maven (`mvn`) or Gradle (`gradle`/`gradlew`)
2. **Run build** and capture errors:
   ```bash
   # Maven
   mvn compile 2>&1 | head -100
   # Gradle
   ./gradlew compileJava 2>&1 | head -100
   ```
3. **Classify errors**:
   - Compilation errors (syntax, type mismatches, missing imports)
   - Dependency resolution failures
   - Annotation processor errors (Lombok, MapStruct)
   - Resource/configuration errors
4. **Fix incrementally**: one error at a time, re-run build after each fix
5. **Verify**: full build passes, no new warnings introduced

## Rules

- Fix the root cause, not symptoms
- Prefer updating dependencies over downgrading
- Never suppress warnings without justification
- Run tests after build succeeds: `mvn test` / `./gradlew test`
