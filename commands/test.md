---
description: Run the project test suite and generate missing tests for uncovered code
---

# Test

Run tests and optionally generate missing test cases for uncovered code.

## Workflow

1. **Detect test framework**:
   - JavaScript/TypeScript: Jest, Vitest, Mocha, Playwright
   - Python: pytest, unittest, Django test
   - PHP: PHPUnit, Pest
   - Ruby: RSpec, Minitest
   - Go: `go test`
   - Rust: `cargo test`
   - Java: JUnit, TestNG
   - Kotlin: Kotest, JUnit

2. **Run existing tests**:
   ```bash
   # Auto-detect and run
   npm test / pnpm test / pytest / phpunit / rspec / go test ./... / cargo test
   ```

3. **Analyze results**:
   - Count: passed, failed, skipped
   - Failed tests: identify root cause (code bug vs test bug)
   - Coverage: identify uncovered files and functions

4. **Generate missing tests** (if requested with `--generate`):
   - Prioritize: public APIs, business logic, edge cases
   - Follow existing test patterns and conventions
   - One test file per source file
   - Include happy path + error cases + edge cases

5. **Report**: test results summary with coverage delta

## Usage

```
/test                       # Run all tests
/test --generate            # Run tests + generate missing ones
/test src/auth/             # Run tests for specific directory
/test --coverage            # Run with coverage report
```
