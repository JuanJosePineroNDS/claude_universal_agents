---
name: nodejs-express-patterns
description: Node.js backend patterns for Express, Fastify, and NestJS — middleware, routing, error handling, and production hardening
origin: manual
---

# Node.js Express Patterns

## When to Activate

- Building or reviewing Node.js backend APIs
- Working with Express, Fastify, or NestJS
- Implementing middleware, authentication, or error handling
- Optimizing Node.js server performance

## Project Structure

```
src/
  config/          # Environment, database, external services config
  middleware/      # Auth, validation, error handling, logging
  routes/          # Route definitions (thin layer)
  controllers/     # Request/response handling
  services/        # Business logic (framework-agnostic)
  models/          # Database models/schemas
  utils/           # Shared utilities
  types/           # TypeScript interfaces and types
  __tests__/       # Test files mirroring src structure
```

## Express Patterns

### Middleware Chain
```typescript
// Order matters: auth → validation → handler → error
app.use(helmet());           // Security headers
app.use(cors(corsOptions));  // CORS
app.use(express.json({ limit: '10mb' }));  // Body parser with limit
app.use(requestId());        // Trace ID
app.use(rateLimiter);        // Rate limiting
app.use('/api', apiRouter);  // Routes
app.use(errorHandler);       // Global error handler (LAST)
```

### Error Handling Middleware
```typescript
const errorHandler: ErrorRequestHandler = (err, req, res, next) => {
  const status = err.statusCode || 500;
  const message = status === 500 ? 'Internal Server Error' : err.message;

  if (status >= 500) {
    logger.error({ err, requestId: req.id }, 'Unhandled error');
  }

  res.status(status).json({
    error: { code: err.code || 'INTERNAL_ERROR', message }
  });
};
```

### Async Route Handler
```typescript
// Wrap async handlers to catch promise rejections
const asyncHandler = (fn: RequestHandler): RequestHandler =>
  (req, res, next) => Promise.resolve(fn(req, res, next)).catch(next);

router.get('/users/:id', asyncHandler(async (req, res) => {
  const user = await userService.findById(req.params.id);
  if (!user) throw new NotFoundError('User');
  res.json(user);
}));
```

## Validation

```typescript
// Use zod or joi at the route level
import { z } from 'zod';

const createUserSchema = z.object({
  body: z.object({
    email: z.string().email(),
    name: z.string().min(2).max(100),
    role: z.enum(['user', 'admin']).default('user'),
  }),
});

// Validation middleware
const validate = (schema: ZodSchema) => (req, res, next) => {
  const result = schema.safeParse(req);
  if (!result.success) {
    return res.status(400).json({ errors: result.error.flatten() });
  }
  next();
};
```

## Authentication Patterns

```typescript
// JWT middleware
const authenticate = asyncHandler(async (req, res, next) => {
  const token = req.headers.authorization?.replace('Bearer ', '');
  if (!token) throw new UnauthorizedError();

  const payload = await verifyToken(token);
  req.user = payload;
  next();
});

// Role-based authorization
const authorize = (...roles: Role[]) => (req, res, next) => {
  if (!roles.includes(req.user.role)) throw new ForbiddenError();
  next();
};
```

## Production Hardening

### Must-Have
- `helmet` for security headers
- `cors` with explicit origin whitelist
- `express-rate-limit` on all public endpoints
- Request body size limits
- Graceful shutdown handling
- Structured logging (pino or winston)
- Health check endpoint (`/health`)
- Request tracing (correlation IDs)

### Process Management
```javascript
// Graceful shutdown
process.on('SIGTERM', async () => {
  logger.info('SIGTERM received, shutting down gracefully');
  server.close(() => {
    db.disconnect();
    process.exit(0);
  });
  setTimeout(() => process.exit(1), 30000); // Force after 30s
});
```

### Performance
- Use `cluster` module or PM2 for multi-core
- Stream large responses (don't buffer)
- Use connection pooling for databases
- Cache with Redis for repeated queries
- Compress responses with `compression` middleware
- Set appropriate `keep-alive` timeouts

## Anti-Patterns

- Business logic in route handlers (move to services)
- Missing error handling middleware
- Synchronous file I/O in request handlers
- Storing sessions in memory (use Redis)
- Not validating request input
- Exposing stack traces in production
- Missing `Content-Type` headers on responses
