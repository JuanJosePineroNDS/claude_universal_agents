---
description: Profile and optimize application performance including queries, bundle size, and runtime
---

# Optimize

Profile the application and apply targeted performance optimizations.

## Workflow

1. **Identify optimization target** from arguments or detect bottlenecks:
   - Frontend: bundle size, render performance, Core Web Vitals
   - Backend: response times, database queries, memory usage
   - Database: slow queries, missing indexes, N+1 patterns

2. **Profile and measure baseline**:
   - Frontend: `lighthouse`, bundle analyzer, React DevTools profiler
   - Backend: request timing, slow query log, memory profiling
   - Database: `EXPLAIN ANALYZE` on slow queries

3. **Apply optimizations** (one at a time):
   - **Frontend**: code splitting, lazy loading, image optimization, memoization, tree shaking
   - **Backend**: caching (Redis/in-memory), connection pooling, async processing, pagination
   - **Database**: add indexes, optimize queries, denormalize hot paths, batch operations
   - **General**: reduce unnecessary dependencies, compress responses, CDN configuration

4. **Measure after** each change — reject if no measurable improvement

5. **Report**: before/after metrics for each optimization applied

## Rules

- Always measure before and after — no blind optimizations
- One optimization at a time with verification
- Never sacrifice readability for micro-optimizations
- Focus on the critical path (P90/P99 latency, largest bundle chunks)

## Usage

```
/optimize                   # Auto-detect and optimize bottlenecks
/optimize --queries         # Focus on database queries
/optimize --bundle          # Focus on frontend bundle size
```
