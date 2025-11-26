# Stream Module

The `stream` module provides stream processing capabilities for distributed message processing.

## Overview

This module implements a stream-based message processing system built on top of Redis Streams or in-memory queues. It provides reliable message delivery, consumer groups, and fault-tolerant processing.

## Features

- Stream-based messaging
- Consumer groups for parallel processing
- Message acknowledgment
- Automatic retry with backoff
- Dead letter queue for failed messages
- In-memory and Redis implementations
- Concurrent message processing
- Stream monitoring and statistics

## Architecture

### Producer
- Publishes messages to streams
- Ensures message delivery
- Supports batch publishing

### Consumer
- Processes messages from streams
- Configurable concurrency
- Automatic retry on failure
- Message acknowledgment
- Idle message claiming

## Key Components

### ConsumerConfig
Configuration for stream consumers:
- Concurrency: Number of worker goroutines
- DefaultHandlerConfig: Default settings for handlers

### HandlerConfig
Configuration for individual stream handlers:
- IdleTimeout: Time before message can be reclaimed
- MaxRetries: Maximum retry attempts
- Batch processing options

## Usage

```go
// Create a producer
producer := stream.NewProducer(config)

// Publish messages
err := producer.Publish(ctx, streamName, message)

// Create a consumer
consumer := stream.NewConsumer(config)

// Register a stream handler
err := consumer.Register(streamName, func(ctx context.Context, msg *Message) error {
    // Process message
    return processMessage(msg)
}, handlerOptions...)

// Start consuming
err := consumer.Start(ctx)

// Graceful shutdown
consumer.Stop()
```

## Message Processing

Messages are processed with:
- Automatic acknowledgment on success
- Retry with exponential backoff on failure
- Dead letter queue after max retries
- Exactly-once processing semantics (when configured)

## Consumer Groups

Multiple consumers can process from the same stream:
- Load distribution across consumers
- Automatic failover
- Message claiming from failed consumers
- Parallel processing

## Configuration

```go
consumerConfig := stream.ConsumerConfig{
    Concurrency: 5,
    DefaultHandlerConfig: stream.HandlerConfig{
        IdleTimeout: 1 * time.Minute,
        MaxRetries: 3,
    },
}
```

## Use Cases

- Pipeline execution events
- Webhook delivery
- Background job processing
- Event processing
- Asynchronous task execution
- Data synchronization
- Notification delivery

## Error Handling

- Failed messages are retried automatically
- Configurable retry limits
- Dead letter queue for permanently failed messages
- Error logging and monitoring

## Monitoring

Track consumer performance:
- Message processing rate
- Error rates
- Pending message count
- Consumer lag
- Handler execution time

## Best Practices

- Set appropriate concurrency levels
- Configure retry limits based on operation type
- Implement idempotent message handlers
- Monitor consumer lag
- Use dead letter queues for debugging
- Gracefully shutdown consumers

## License

Apache License 2.0
