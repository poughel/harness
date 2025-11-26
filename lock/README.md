# Lock Module

The `lock` module provides distributed locking mechanisms for coordinating operations across multiple instances.

## Overview

This module implements distributed locks to prevent race conditions and ensure mutual exclusion in multi-instance deployments. It supports both in-memory locks (for single-instance) and Redis-based distributed locks.

## Features

- Distributed locking with Redis
- In-memory locking for development
- Mutex-style lock interface
- Configurable lock timeouts
- Automatic lock expiration
- Lock retry mechanisms
- Deadlock prevention

## Lock Implementations

- **Memory**: In-memory locks for single instance deployments
- **Redis**: Distributed locks using Redis (Redsync)

## Key Interface

### MutexManager
Provides lock management:
- `NewMutex`: Create a new mutex for a key
- Lock acquisition and release
- Configurable retry and timeout options

## Usage

```go
// Create a lock manager
manager := lock.NewRedisManager(redisClient)

// Create a mutex for a resource
mutex := manager.NewMutex(
    "resource-key",
    lock.WithExpiry(30 * time.Second),
    lock.WithRetries(3),
)

// Acquire the lock
err := mutex.Lock()
if err != nil {
    // Handle lock acquisition failure
    return err
}
defer mutex.Unlock()

// Perform protected operation
performCriticalOperation()
```

## Error Types

The module defines specific error kinds:
- `ErrorKindLockHeld`: Lock already held by another process
- `ErrorKindLockNotHeld`: Attempt to unlock a non-held lock
- `ErrorKindCannotLock`: Timeout while acquiring lock
- `ErrorKindMaxRetriesExceeded`: Failed after max retries

## Configuration

```go
config := lock.Config{
    Mode: lock.ModeRedis,
    Namespace: "harness",
    // Redis-specific settings
}
```

## Use Cases

- Job scheduler coordination
- Repository operations
- Cache updates
- Resource provisioning
- Database migrations
- Singleton operations in distributed systems

## Best Practices

- Always use defer to ensure locks are released
- Set appropriate expiry times to prevent deadlocks
- Use unique keys for different resources
- Handle lock acquisition failures gracefully
- Keep critical sections short

## License

Apache License 2.0
