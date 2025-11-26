# Audit Module

The `audit` module provides audit logging functionality for tracking important actions and resource changes in the Harness system.

## Overview

This module enables tracking and logging of user actions, resource modifications, and security-relevant events. It supports audit trails for compliance and security monitoring purposes.

## Key Components

- **Action Types**: Defines actions like `Created`, `Updated`, `Deleted`, `Bypassed`, and `ForcePush`
- **Resource Types**: Supports various resource types including:
  - Repositories
  - Branch rules
  - Tags and tag rules
  - Pull requests
  - Webhooks
  - Registry artifacts
  - And more

## Main Types

### Event
Represents an audit event containing:
- Action performed
- User/principal information
- Resource affected
- Timestamp
- Client IP and request method
- Before/after object states (for updates)

### Resource
Represents a resource being audited with:
- Resource type
- Resource identifier
- Additional metadata

## Usage

```go
// Create an audit event
event := audit.Event{
    Action: audit.ActionCreated,
    User: principal,
    SpacePath: "/root/projects",
    Resource: audit.NewResource(
        audit.ResourceTypeRepository,
        "my-repo",
        audit.RepoName, "my-repo",
    ),
}
```

## License

Apache License 2.0
