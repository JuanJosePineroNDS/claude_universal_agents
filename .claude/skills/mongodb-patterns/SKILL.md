---
name: mongodb-patterns
description: MongoDB patterns for document modeling, aggregation pipelines, indexing strategies, and production operations
origin: manual
---

# MongoDB Patterns

## When to Activate

- Designing MongoDB schemas or collections
- Writing aggregation pipelines
- Optimizing MongoDB queries or indexes
- Setting up MongoDB for production

## Document Modeling Patterns

### Embedding vs Referencing Decision Tree
```
Is the related data...
├── Always accessed together?       → EMBED
├── Updated independently?          → REFERENCE
├── One-to-one or one-to-few?      → EMBED
├── One-to-many (unbounded)?       → REFERENCE
├── Many-to-many?                  → REFERENCE (with array of IDs)
└── Growing without bound?         → REFERENCE (never unbounded arrays)
```

### Common Patterns

**Attribute Pattern** — variable fields as key-value pairs:
```json
{
  "name": "Running Shoe",
  "attributes": [
    { "k": "color", "v": "blue" },
    { "k": "size", "v": "10" }
  ]
}
```

**Bucket Pattern** — group time-series into chunks:
```json
{
  "sensor_id": "temp_01",
  "date": "2024-01-15",
  "readings": [
    { "time": "10:00", "value": 22.5 },
    { "time": "10:05", "value": 22.7 }
  ],
  "count": 2,
  "sum": 45.2
}
```

**Computed Pattern** — pre-calculate aggregates:
```json
{
  "product_id": "abc",
  "total_reviews": 142,
  "avg_rating": 4.3,
  "last_review_at": "2024-01-15"
}
```

**Outlier Pattern** — handle documents that exceed normal size:
```json
{
  "celebrity_id": "xyz",
  "followers_count": 5000000,
  "has_overflow": true
  // Overflow followers in separate collection
}
```

## Indexing

### Index Types
- **Single field**: most common, one field
- **Compound**: multiple fields, order matters (ESR rule)
- **Multikey**: automatic for array fields
- **Text**: full-text search (prefer Atlas Search for production)
- **Geospatial**: 2dsphere for location queries
- **TTL**: auto-delete documents after expiry
- **Partial**: index subset of documents matching a filter

### Index Optimization
```javascript
// Check if query uses index
db.collection.find({...}).explain("executionStats")

// Find unused indexes
db.collection.aggregate([{ $indexStats: {} }])

// Create index without blocking writes
db.collection.createIndex({ field: 1 }, { background: true })
```

## Aggregation Pipeline

### Pipeline Optimization Order
```javascript
db.orders.aggregate([
  { $match: { status: "completed" } },         // 1. Filter early
  { $project: { items: 1, total: 1, date: 1 }}, // 2. Reduce fields
  { $unwind: "$items" },                         // 3. Unwind after reducing
  { $group: { _id: "$items.category", sum: { $sum: "$total" } } },
  { $sort: { sum: -1 } },
  { $limit: 10 }
])
```

### Common Stages
- `$match` + `$project` first (reduce data volume)
- `$lookup` with `pipeline` for filtered joins
- `$facet` for parallel aggregation branches
- `$merge` for materialized views (incremental updates)
- `$setWindowFields` for running totals, rankings

## Production Operations

### Replica Set
- Minimum 3 members (primary + 2 secondaries)
- Priority-based failover configuration
- Read preference: `primaryPreferred` for most apps
- Write concern: `majority` for durability

### Performance Monitoring
```javascript
// Slow query profiler
db.setProfilingLevel(1, { slowms: 100 })

// Current operations
db.currentOp({ "active": true, "secs_running": { "$gt": 5 } })

// Server stats
db.serverStatus().opcounters
db.serverStatus().connections
```

### Security Checklist
- [ ] Authentication enabled (SCRAM-SHA-256)
- [ ] Role-based authorization
- [ ] TLS for all connections
- [ ] Network: bind to specific IPs, firewall rules
- [ ] Audit logging enabled
- [ ] Encryption at rest
- [ ] Field-level encryption for PII
