# Cache Module

The `cache` module provides a flexible caching abstraction with support for multiple cache implementations.

## Overview

This module offers a generic cache interface with support for both simple key-value caching and extended caching with batch operations. It includes implementations for in-memory caching, TTL-based caching, and Redis-based distributed caching.

## Cache Implementations

- **NoCache**: A pass-through implementation that bypasses caching
- **TTLCache**: In-memory cache with time-to-live expiration
- **RedisCache**: Distributed cache using Redis

## Key Interfaces

### Cache[K, V]
Basic cache interface providing:
- `Get`: Retrieve a value by key
- `Evict`: Remove a value from cache
- `Stats`: Get cache statistics (hits, misses)

### ExtendedCache[K, V]
Extended interface with additional operations:
- `Map`: Batch retrieve multiple values by keys

### Getter[K, V]
Data source interface:
- `Find`: Retrieve a value from the underlying data source

## Usage

```go
// Create a cache with a getter
cache := cache.NewTTLCache(getter, ttl, maxSize)

// Get a value (cache miss will call getter)
value, err := cache.Get(ctx, key)

// Evict a value
cache.Evict(ctx, key)

// Get cache statistics
hits, misses := cache.Stats()
```

## Features

- Generic type support for type-safe caching
- Automatic cache population on miss
- Batch operations for efficient multi-key retrieval
- Statistics tracking
- Configurable TTL and size limits

## License

Apache License 2.0
