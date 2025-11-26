# Contextutil Module

The `contextutil` module provides utilities for working with Go contexts, including storing and retrieving values from context.

## Overview

This module offers helper functions and patterns for context management, making it easier to pass request-scoped values through the application layers in a type-safe manner.

## Features

- Type-safe context value storage and retrieval
- Common context key definitions
- Helper functions for context manipulation
- Request-scoped value management

## Usage

```go
// Store a value in context
ctx = contextutil.WithValue(ctx, key, value)

// Retrieve a value from context
value, ok := contextutil.GetValue(ctx, key)

// Common patterns for request IDs, users, etc.
ctx = contextutil.WithRequestID(ctx, requestID)
requestID := contextutil.GetRequestID(ctx)
```

## Common Context Values

The module provides standardized ways to store and retrieve:
- Request IDs
- User principals
- Authentication information
- Trace/span information
- Request metadata

## Best Practices

- Use typed context keys to avoid collisions
- Always check for value existence before use
- Document context requirements in function signatures
- Keep context values immutable

## License

Apache License 2.0
