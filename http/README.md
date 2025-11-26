# HTTP Module

The `http` module provides HTTP utilities and middleware for the Harness web server.

## Overview

This module contains common HTTP utilities, middleware components, and helpers used throughout the Harness web application. It provides standardized request/response handling, error processing, and common HTTP patterns.

## Features

- HTTP middleware components
- Request and response utilities
- Error handling and formatting
- HTTP client helpers
- Content negotiation
- Request validation

## Common Middleware

- Authentication middleware
- Authorization checks
- Request logging
- Error recovery
- CORS handling
- Rate limiting
- Request ID injection

## HTTP Utilities

- Response writers and formatters
- JSON encoding/decoding helpers
- Query parameter parsing
- Header manipulation
- Content-type detection
- Status code helpers

## Usage

```go
// Write JSON response
http.WriteJSON(w, data, http.StatusOK)

// Write error response
http.WriteError(w, err, http.StatusBadRequest)

// Parse request body
var input RequestType
err := http.DecodeJSON(r.Body, &input)

// Apply middleware
handler = http.Chain(handler, authMiddleware, loggingMiddleware)
```

## Error Handling

The module provides standardized error responses with:
- Consistent error format
- HTTP status code mapping
- Error message sanitization
- Error logging integration

## Best Practices

- Use provided utilities for consistency
- Apply appropriate middleware in correct order
- Handle errors with proper status codes
- Log requests for debugging and monitoring

## License

Apache License 2.0
