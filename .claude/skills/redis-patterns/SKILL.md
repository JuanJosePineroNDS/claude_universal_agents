---
name: redis-patterns
description: Redis patterns for caching, sessions, rate limiting, pub/sub, streams, and production operations
origin: manual
---

# Redis Patterns

## When to Activate

- Implementing caching, sessions, or rate limiting
- Building real-time features with Pub/Sub or Streams
- Choosing Redis data structures for specific use cases
- Optimizing Redis performance or memory usage

## Data Structure Selection

| Use Case | Structure | Key Pattern | Commands |
|----------|-----------|-------------|----------|
| Cache object | String (JSON) | `cache:{entity}:{id}` | GET, SET, SETEX |
| Object fields | Hash | `user:{id}` | HGET, HSET, HMGET |
| Queue | List | `queue:{name}` | LPUSH, RPOP, BRPOP |
| Unique tags | Set | `tags:{entity}:{id}` | SADD, SMEMBERS, SINTER |
| Leaderboard | Sorted Set | `leaderboard:{game}` | ZADD, ZRANGE, ZRANK |
| Rate limit | Sorted Set | `ratelimit:{ip}` | ZADD, ZRANGEBYSCORE, ZCARD |
| Event log | Stream | `events:{type}` | XADD, XREAD, XREADGROUP |
| Feature flag | String | `flag:{feature}` | GET, SET |
| Unique count | HyperLogLog | `visitors:{date}` | PFADD, PFCOUNT |

## Caching Patterns

### Cache-Aside (Lazy Loading)
```python
def get_user(user_id):
    cached = redis.get(f"user:{user_id}")
    if cached:
        return json.loads(cached)

    user = db.query("SELECT * FROM users WHERE id = %s", user_id)
    redis.setex(f"user:{user_id}", 3600, json.dumps(user))  # TTL 1h
    return user
```

### Cache Stampede Prevention
```python
# Probabilistic early expiry
import random

def get_with_early_expiry(key, ttl, fetch_fn):
    cached, remaining_ttl = redis.get_with_ttl(key)
    if cached and remaining_ttl > ttl * 0.1 * random.random():
        return cached
    # Refresh cache
    value = fetch_fn()
    redis.setex(key, ttl, value)
    return value
```

### Cache Invalidation
```
1. TTL-based: simplest, eventual consistency
2. Event-based: invalidate on write (pub/sub or after DB write)
3. Version-based: key includes version, increment on change
```

## Rate Limiting

### Sliding Window (Sorted Set)
```python
def is_rate_limited(user_id, limit=100, window=60):
    key = f"ratelimit:{user_id}"
    now = time.time()
    pipe = redis.pipeline()
    pipe.zremrangebyscore(key, 0, now - window)  # Remove old entries
    pipe.zadd(key, {str(now): now})              # Add current request
    pipe.zcard(key)                               # Count requests
    pipe.expire(key, window)                      # Auto cleanup
    _, _, count, _ = pipe.execute()
    return count > limit
```

### Token Bucket (String + Lua)
```lua
-- Atomic token bucket in Lua
local key = KEYS[1]
local rate = tonumber(ARGV[1])  -- tokens per second
local capacity = tonumber(ARGV[2])
local now = tonumber(ARGV[3])

local data = redis.call('HMGET', key, 'tokens', 'last_refill')
local tokens = tonumber(data[1]) or capacity
local last = tonumber(data[2]) or now

local elapsed = now - last
tokens = math.min(capacity, tokens + elapsed * rate)

if tokens >= 1 then
  tokens = tokens - 1
  redis.call('HMSET', key, 'tokens', tokens, 'last_refill', now)
  redis.call('EXPIRE', key, capacity / rate * 2)
  return 1  -- allowed
end
return 0  -- denied
```

## Pub/Sub vs Streams

| Feature | Pub/Sub | Streams |
|---------|---------|---------|
| Persistence | No | Yes |
| Replay | No | Yes |
| Consumer groups | No | Yes |
| Acknowledgment | No | Yes (XACK) |
| Use case | Fire-and-forget notifications | Reliable message processing |

## Session Management

```python
# Store session in hash
def create_session(user_id, data, ttl=3600):
    session_id = secrets.token_urlsafe(32)
    key = f"session:{session_id}"
    redis.hmset(key, {"user_id": user_id, **data})
    redis.expire(key, ttl)
    return session_id

def get_session(session_id):
    return redis.hgetall(f"session:{session_id}")

# Sliding expiry: extend TTL on each access
def touch_session(session_id, ttl=3600):
    redis.expire(f"session:{session_id}", ttl)
```

## Production Best Practices

### Memory
- Set `maxmemory` and `maxmemory-policy` (allkeys-lru for cache, noeviction for data)
- Monitor memory with `INFO memory`
- Use `MEMORY USAGE key` to profile large keys
- Compress large values (>1KB)

### Performance
- Pipeline commands to reduce round trips
- Use Lua scripts for atomic multi-step operations
- Never use `KEYS *` — use `SCAN` with cursor
- Set reasonable TTLs on all cache keys

### High Availability
- Redis Sentinel for automatic failover
- Redis Cluster for horizontal scaling
- AOF + RDB for persistence (AOF for durability, RDB for speed)
- Test failover procedures regularly
