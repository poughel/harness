# Events Module

The `events` module provides an event streaming and processing framework for real-time system events.

## Overview

This module implements an event-driven architecture that allows different parts of the system to publish and subscribe to events. It supports both in-memory and Redis-based event streaming for scalability.

## Features

- Event publishing and subscription
- Multiple backend support (in-memory, Redis)
- Event filtering and routing
- Stream management
- Event retention and cleanup
- Type-safe event handling

## Event Modes

- **InMemory**: For single-instance deployments or development
- **Redis**: For distributed deployments with multiple instances

## Key Components

### Event[T]
Generic event wrapper containing:
- Event ID
- Timestamp
- Typed payload

### Reader/Reporter
- **Reader**: Subscribes to and processes events
- **Reporter**: Publishes events to the system

## Configuration

```go
config := events.Config{
    Mode: events.ModeRedis,
    Namespace: "harness",
    MaxStreamLength: 10000,
    ApproxMaxStreamLength: true,
}
```

## Usage

```go
// Create a reporter (publisher)
reporter := events.NewReporter(config)

// Publish an event
err := reporter.Report(ctx, event)

// Create a reader (subscriber)
reader := events.NewReader(config)

// Subscribe to events
err := reader.Subscribe(ctx, category, eventType, handler)
```

## Use Cases

- Real-time notifications
- Activity feeds
- Audit event distribution
- Webhook triggers
- System monitoring
- Integration points

## License

Apache License 2.0
