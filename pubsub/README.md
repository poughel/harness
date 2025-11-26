# Pubsub Module

The `pubsub` module provides publish-subscribe messaging for inter-process communication.

## Overview

This module implements a pub-sub messaging system that allows different parts of the application to communicate asynchronously through topics. It supports both in-memory and Redis-based messaging for scalability.

## Features

- Topic-based messaging
- Multiple subscriber support
- In-memory and Redis backends
- Message delivery guarantees
- Consumer groups
- Message persistence (Redis mode)
- Graceful shutdown handling

## Messaging Modes

- **InMemory**: For single-instance deployments or testing
- **Redis**: For distributed deployments with multiple instances

## Key Interfaces

### Publisher
- `Publish`: Send messages to a topic

### PubSub
Combines Publisher with:
- `Subscribe`: Register a handler for a topic

### Consumer
- `Subscribe`: Subscribe to topics
- `Unsubscribe`: Unsubscribe from topics
- `Close`: Cleanup consumer resources

## Usage

```go
// Create a pub-sub instance
ps := pubsub.New(config)

// Publish a message
err := ps.Publish(ctx, "user.created", payload)

// Subscribe to a topic
consumer := ps.Subscribe(ctx, "user.created", func(payload []byte) error {
    // Handle message
    return processUserCreated(payload)
})

// Subscribe to multiple topics
err := consumer.Subscribe(ctx, "user.updated", "user.deleted")

// Cleanup
consumer.Close()
```

## Configuration

```go
config := pubsub.Config{
    Mode: pubsub.ModeRedis,
    Namespace: "harness",
    // Redis-specific settings
}
```

## Message Patterns

- **Fan-out**: One publisher, multiple subscribers
- **Work queue**: Multiple consumers processing from the same topic
- **Topic routing**: Route messages based on topic patterns

## Use Cases

- Event notifications
- Asynchronous task distribution
- Cache invalidation
- Real-time updates
- Microservice communication
- Webhook delivery
- Activity feeds

## Reliability

- Message persistence in Redis mode
- Automatic reconnection
- Error handling in message handlers
- Graceful degradation

## Best Practices

- Use meaningful topic names
- Keep message handlers idempotent
- Handle errors in message processing
- Use appropriate mode for deployment type
- Clean up consumers on shutdown

## License

Apache License 2.0
