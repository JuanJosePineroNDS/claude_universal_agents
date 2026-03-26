---
name: websocket-grpc-patterns
description: Real-time communication patterns for WebSocket, gRPC, Server-Sent Events, and message queues
origin: manual
---

# WebSocket & gRPC Patterns

## When to Activate

- Implementing real-time features (chat, notifications, live updates)
- Building gRPC services for inter-service communication
- Choosing between WebSocket, SSE, gRPC, or polling
- Working with message queues (RabbitMQ, Kafka, Redis Streams)

## Protocol Selection Guide

| Need | Protocol | Why |
|------|----------|-----|
| Bidirectional real-time | **WebSocket** | Full-duplex, low latency |
| Server → client streaming | **SSE** | Simpler than WS, HTTP-based, auto-reconnect |
| Service-to-service (typed) | **gRPC** | Protobuf, streaming, code generation |
| Service-to-service (simple) | **REST** | Universal, cacheable, well-understood |
| Async decoupled processing | **Message Queue** | Durable, retry, fan-out |
| Frequent small updates | **WebSocket** | Avoid HTTP overhead |
| Infrequent updates | **Polling/SSE** | Simpler infrastructure |

## WebSocket Patterns

### Connection Lifecycle
```
1. CONNECT  → Authenticate via token in query string or first message
2. OPEN     → Subscribe to channels/rooms
3. MESSAGE  → Structured JSON with { type, payload, id }
4. PING     → Heartbeat every 30s to detect dead connections
5. CLOSE    → Clean disconnect with reason code
```

### Message Protocol
```json
{
  "type": "chat.message",
  "payload": { "roomId": "abc", "text": "hello" },
  "id": "msg_123",
  "timestamp": "2024-01-15T10:00:00Z"
}
```

### Room/Channel Pattern
```typescript
// Server-side room management
class RoomManager {
  private rooms = new Map<string, Set<WebSocket>>();

  join(roomId: string, ws: WebSocket) { /* add to room */ }
  leave(roomId: string, ws: WebSocket) { /* remove from room */ }
  broadcast(roomId: string, message: any, exclude?: WebSocket) {
    this.rooms.get(roomId)?.forEach(client => {
      if (client !== exclude && client.readyState === WebSocket.OPEN) {
        client.send(JSON.stringify(message));
      }
    });
  }
}
```

### Scaling WebSockets
- Use Redis Pub/Sub or NATS for cross-server broadcasting
- Sticky sessions or shared state for horizontal scaling
- Connection limits per server (~10K-50K typical)
- Implement backpressure for slow consumers

## gRPC Patterns

### Service Definition
```protobuf
syntax = "proto3";

service UserService {
  rpc GetUser(GetUserRequest) returns (User);           // Unary
  rpc ListUsers(ListUsersRequest) returns (stream User); // Server streaming
  rpc CreateUsers(stream User) returns (CreateResponse); // Client streaming
  rpc Chat(stream Message) returns (stream Message);     // Bidirectional
}
```

### Best Practices
- Use deadline/timeout on every RPC call (never infinite)
- Implement retry with exponential backoff for transient errors
- Use interceptors for auth, logging, tracing (like middleware)
- Status codes: `NOT_FOUND`, `ALREADY_EXISTS`, `PERMISSION_DENIED` (not generic `INTERNAL`)
- Health check service for load balancers
- Reflection service for debugging

### Error Handling
```go
// Return rich error details
st := status.New(codes.InvalidArgument, "invalid email")
st, _ = st.WithDetails(&errdetails.BadRequest_FieldViolation{
    Field:       "email",
    Description: "must be a valid email address",
})
return nil, st.Err()
```

## Server-Sent Events (SSE)

```typescript
// Express SSE endpoint
app.get('/events', (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Cache-Control', 'no-cache');
  res.setHeader('Connection', 'keep-alive');

  const send = (event: string, data: any) => {
    res.write(`event: ${event}\ndata: ${JSON.stringify(data)}\n\n`);
  };

  // Subscribe to events
  const unsub = eventBus.on('update', (data) => send('update', data));

  req.on('close', () => unsub());
});
```

## Message Queue Patterns

### Producer-Consumer
- One producer, one consumer group — simple task distribution
- Use for: background jobs, email sending, image processing

### Pub/Sub (Fan-out)
- One producer, multiple consumer groups — each gets all messages
- Use for: notifications, event broadcasting, cache invalidation

### Dead Letter Queue
- Messages that fail N times → DLQ for manual review
- Always configure DLQ in production
- Alert on DLQ growth

### Reliability
- At-least-once delivery: idempotent consumers (use idempotency keys)
- Exactly-once: not possible across systems — design for at-least-once
- Message ordering: partition by key (Kafka), or accept unordered
