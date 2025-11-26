# Logging Module

The `logging` module provides structured logging configuration and utilities for the Harness application.

## Overview

This module sets up and configures the logging infrastructure used throughout the application. It provides structured logging with multiple output formats and log levels.

## Features

- Structured logging with JSON and text formats
- Configurable log levels (debug, info, warn, error)
- Contextual logging with fields
- Request-scoped logging
- Log output configuration (stdout, files)
- Performance-optimized logging
- Integration with monitoring systems

## Log Levels

- **Debug**: Detailed information for debugging
- **Info**: General informational messages
- **Warn**: Warning messages for potentially harmful situations
- **Error**: Error messages for error events
- **Fatal**: Critical errors that cause application termination

## Usage

```go
// Setup logging
logging.Setup(config)

// Basic logging
log.Info().Msg("Application started")
log.Error().Err(err).Msg("Failed to connect to database")

// Logging with fields
log.Info().
    Str("user", username).
    Int("count", count).
    Msg("User action completed")

// Context-aware logging
logger := log.Ctx(ctx)
logger.Info().Msg("Request processed")

// Structured error logging
log.Error().
    Err(err).
    Str("operation", "create_repo").
    Str("repo", repoName).
    Msg("Repository creation failed")
```

## Configuration

```go
config := logging.Config{
    Level: "info",
    Format: "json",  // or "text"
    Pretty: false,   // pretty print for development
}
```

## Best Practices

- Use appropriate log levels
- Include context in log messages
- Use structured fields instead of string formatting
- Don't log sensitive information (passwords, tokens)
- Use contextual logging for request tracking
- Keep log messages concise and actionable

## Integration

The logging module integrates with:
- HTTP middleware for request logging
- Error tracking systems
- Monitoring and alerting platforms
- Log aggregation services

## License

Apache License 2.0
