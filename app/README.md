# App Module

The `app` module contains the main application logic and orchestration for the Harness application.

## Overview

This module serves as the core application layer, bringing together all other modules and providing the main business logic, API handlers, and service implementations. It acts as the glue between different components of the system.

## Structure

The app module is organized into several sub-packages:

- **api**: HTTP API handlers and routes
- **auth**: Authentication and authorization logic
- **bootstrap**: Application initialization and setup
- **config**: Application configuration management
- **connector**: External system connectors
- **cron**: Scheduled task definitions
- **events**: Event handlers and processors
- **githook**: Git hook implementations
- **gitspace**: Gitspace (dev environment) management
- **jwt**: JWT token handling
- **paths**: Path utilities and resolvers
- **pipeline**: Pipeline execution and management
- **request**: Request context and utilities
- **router**: HTTP router setup
- **server**: HTTP server implementation
- **services**: Business logic services
- **sse**: Server-Sent Events implementation
- **store**: Store layer integration
- **testing**: Testing utilities
- **token**: Token management
- **url**: URL building and parsing utilities

## Key Components

### API Handlers
HTTP endpoint implementations for:
- Repository management
- Pull requests
- Pipelines
- Users and authentication
- Webhooks
- Registry operations
- And more...

### Services
Business logic layer providing:
- Repository operations
- Pull request workflows
- Pipeline execution
- User management
- Permission checking
- Webhook delivery
- And more...

### Server
- HTTP server setup
- Middleware chain
- Route registration
- Graceful shutdown
- Health checks

### Authentication
- User authentication
- Token validation
- Session management
- Authorization checks
- Permission evaluation

## Application Flow

1. **Bootstrap**: Initialize configuration, database, and services
2. **Server Setup**: Configure HTTP server and routes
3. **Request Handling**: Process incoming HTTP requests
4. **Business Logic**: Execute service methods
5. **Response**: Return results to client

## Configuration

The app module manages configuration from various sources:
- Environment variables
- Configuration files
- Command-line flags
- Default values

## Usage

```go
// Bootstrap application
config := app.LoadConfig()
app, err := app.New(config)
if err != nil {
    log.Fatal(err)
}

// Start server
err = app.Run()
if err != nil {
    log.Fatal(err)
}
```

## Dependency Injection

Uses Google Wire for dependency injection:
- Automatic dependency resolution
- Type-safe wiring
- Clear dependency graphs
- Easy testing with mocks

## Middleware

Common middleware applied:
- Authentication
- Authorization
- Request logging
- Error recovery
- CORS handling
- Rate limiting
- Request ID injection

## API Versioning

APIs are versioned:
- `/api/v1/...`: Version 1 endpoints
- Supports multiple API versions
- Backward compatibility

## Error Handling

Standardized error handling:
- HTTP status code mapping
- Consistent error response format
- Error logging
- User-friendly error messages

## Health Checks

Provides health check endpoints:
- `/healthz`: Liveness check
- `/readyz`: Readiness check
- Component health status

## License

Apache License 2.0
