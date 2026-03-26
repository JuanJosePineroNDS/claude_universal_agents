---
name: error-handling-universal
description: Universal error handling patterns across languages — structured errors, boundaries, logging, and user-facing messages
origin: manual
---

# Error Handling Universal

## When to Activate

- Implementing error handling in any language
- Designing error boundaries, custom exceptions, or Result types
- Setting up logging and monitoring for errors
- Reviewing code for missing or incorrect error handling

## Core Principles

1. **Fail fast**: Detect errors early, at system boundaries
2. **Fail loud**: Log errors with context, never swallow silently
3. **Fail safe**: Degrade gracefully, protect user data
4. **Fail informatively**: Give the user actionable info, the developer debug context

## Error Classification

| Type | Example | Handle By |
|------|---------|-----------|
| **Validation** | Invalid email format | Return 400, show field error |
| **Authentication** | Invalid token | Return 401, redirect to login |
| **Authorization** | No permission | Return 403, log attempt |
| **Not Found** | Resource missing | Return 404, show helpful message |
| **Conflict** | Duplicate entry | Return 409, suggest resolution |
| **Internal** | Null pointer, DB down | Return 500, log full context, alert |
| **External** | Third-party API timeout | Retry with backoff, fallback |

## Language-Specific Patterns

### JavaScript/TypeScript
```typescript
// Custom error hierarchy
class AppError extends Error {
  constructor(message: string, public code: string, public statusCode: number) {
    super(message);
    this.name = this.constructor.name;
  }
}
class NotFoundError extends AppError {
  constructor(resource: string) {
    super(`${resource} not found`, 'NOT_FOUND', 404);
  }
}

// Result pattern (no exceptions for expected failures)
type Result<T, E = Error> = { ok: true; value: T } | { ok: false; error: E };
```

### Python
```python
# Custom exceptions with context
class AppError(Exception):
    def __init__(self, message: str, code: str, status_code: int = 500):
        self.message = message
        self.code = code
        self.status_code = status_code

# Context managers for cleanup
with managed_resource() as resource:
    resource.process()  # cleanup guaranteed
```

### Go
```go
// Sentinel errors + wrapping
var ErrNotFound = errors.New("not found")

func GetUser(id string) (*User, error) {
    user, err := db.Find(id)
    if err != nil {
        return nil, fmt.Errorf("GetUser(%s): %w", id, err)
    }
    return user, nil
}
```

### Rust
```rust
// Result<T, E> is the standard pattern
use thiserror::Error;

#[derive(Error, Debug)]
enum AppError {
    #[error("not found: {0}")]
    NotFound(String),
    #[error("database error")]
    Database(#[from] sqlx::Error),
}
```

## Error Boundaries

### API Layer
- Global error handler catches all unhandled errors
- Map internal errors to HTTP status codes
- Never expose stack traces or internal details to clients
- Return structured error response: `{ "error": { "code": "...", "message": "..." } }`

### Service Layer
- Catch and wrap lower-level errors with business context
- Retry transient failures (network, DB connections) with exponential backoff
- Circuit breaker for external service calls

### Data Layer
- Wrap database errors with query context
- Handle constraint violations with meaningful messages
- Connection pool exhaustion: queue or reject with backpressure

## Logging Standards

```
ERROR — System is broken, needs immediate attention (page someone)
WARN  — Unexpected but handled, monitor for trends
INFO  — Business events worth tracking (user created, payment processed)
DEBUG — Developer details, disabled in production
```

### Structured Logging Fields
- `timestamp`, `level`, `message` (always)
- `error_code`, `stack_trace` (on errors)
- `request_id`, `user_id` (for tracing)
- `service`, `function`, `duration_ms` (for performance)

## Anti-Patterns to Avoid

- `catch (e) {}` — empty catch blocks swallow errors
- `catch (e) { console.log(e) }` — logging without handling
- Pokemon exception handling (`catch(Exception e)` for everything)
- Returning error strings instead of typed errors
- Using exceptions for control flow (expected conditions)
- Mixing error codes and exceptions inconsistently
