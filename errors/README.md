# Errors Module

The `errors` module provides standardized error handling and error types for the Harness application.

## Overview

This module defines common error types, error creation utilities, and error handling patterns used throughout the application. It provides structured errors with additional context for better debugging and user experience.

## Features

- Standardized error types and codes
- Error wrapping and context
- HTTP status code mapping
- User-friendly error messages
- Error classification (not found, validation, authorization, etc.)

## Common Error Types

- `NotFoundError`: Resource not found errors
- `ValidationError`: Input validation failures
- `UnauthorizedError`: Authentication failures
- `ForbiddenError`: Authorization failures
- `ConflictError`: Resource conflicts
- `InternalError`: Internal server errors

## Usage

```go
// Create a not found error
err := errors.NotFound("repository not found")

// Create a validation error
err := errors.InvalidArgument("invalid repository name")

// Wrap an error with context
err := errors.Wrap(originalErr, "failed to create repository")

// Check error type
if errors.IsNotFound(err) {
    // Handle not found case
}
```

## Error Information

Errors can include:
- Error code/type
- User-facing message
- Internal error details
- Stack trace (in development)
- HTTP status code mapping

## Best Practices

- Use specific error types for different failure scenarios
- Include context when wrapping errors
- Provide actionable error messages to users
- Log detailed error information for debugging

## License

Apache License 2.0
