# Client Module

The `client` module provides Go client libraries for interacting with the Harness API.

## Overview

This module contains programmatic client implementations that allow Go applications to communicate with the Harness server. It provides type-safe methods for accessing all Harness API endpoints.

## Features

- Complete API coverage for Harness operations
- Type-safe request and response handling
- Authentication support (tokens, sessions)
- Error handling and retries
- Request/response interceptors
- Context-aware operations

## Usage

```go
// Create a new client
client := client.New(
    "https://harness.example.com",
    client.WithToken("your-api-token"),
)

// Make API calls
repo, err := client.GetRepository(ctx, spaceRef, repoRef)
if err != nil {
    log.Fatal(err)
}

// Create resources
pr, err := client.CreatePullRequest(ctx, repoRef, pullRequest)
```

## Key Components

- HTTP client with authentication
- Resource-specific client methods (repos, pipelines, users, etc.)
- Request builders and validators
- Response parsers and error mappers
- Pagination support

## Configuration

Clients can be configured with:
- Base URL
- Authentication credentials
- Timeout settings
- Custom HTTP client
- Logging and debugging options

## License

Apache License 2.0
