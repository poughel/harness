# Types Module

The `types` module contains common type definitions and data structures used throughout the Harness application.

## Overview

This module defines core domain types, enums, and data structures that are shared across different modules. It provides a central location for type definitions to ensure consistency and avoid circular dependencies.

## Key Type Categories

### Core Entities
- **Repository**: Git repository metadata
- **Space**: Project/namespace containers
- **Principal**: User and service account representation
- **Pipeline**: CI/CD pipeline definitions
- **PullRequest**: Pull request data structures
- **Webhook**: Webhook configurations

### Execution Types
- **Execution**: Pipeline execution instances
- **Stage**: Pipeline stage definitions
- **Step**: Individual step in a stage
- **Log**: Build and execution logs

### Registry Types
- **Image**: Container image metadata
- **Artifact**: Registry artifact information
- **Tag**: Image tags
- **Manifest**: Image manifests

### Authentication Types
- **Token**: API tokens and credentials
- **Session**: User sessions
- **PublicKey**: SSH public keys

### Enums
Located in the `enum` sub-package:
- Repository states
- Pull request states
- Pipeline triggers
- Webhook events
- User roles and permissions
- And more...

## Structure

The module includes:
- **check**: Validation utilities and checkers
- **enum**: Enumeration definitions
- Main type definitions in the root

## Common Types

### Repository
```go
type Repository struct {
    ID          int64
    Version     int64
    Identifier  string
    Path        string
    ParentID    int64
    Description string
    IsPublic    bool
    CreatedBy   int64
    Created     int64
    Updated     int64
    // ... additional fields
}
```

### Principal
```go
type Principal struct {
    ID          int64
    UID         string
    Email       string
    DisplayName string
    Admin       bool
    // ... additional fields
}
```

### PullRequest
```go
type PullRequest struct {
    ID              int64
    Version         int64
    Number          int64
    State           PullReqState
    Title           string
    Description     string
    SourceRepoID    int64
    SourceBranch    string
    TargetRepoID    int64
    TargetBranch    string
    Author          Principal
    // ... additional fields
}
```

## Usage

```go
import "github.com/harness/gitness/types"

// Create a repository
repo := &types.Repository{
    Identifier: "my-repo",
    Path: "/space/my-repo",
    IsPublic: true,
}

// Use enums
if pr.State == types.PullReqStateOpen {
    // Process open pull request
}

// Use validation
err := types.ValidateIdentifier(identifier)
```

## Validation

Many types include validation methods:
- Identifier format validation
- Field length checks
- Required field validation
- Business rule validation

## JSON Serialization

Types are designed for JSON serialization:
- Proper JSON tags
- Omitempty for optional fields
- Custom marshaling where needed

## Best Practices

- Use defined types instead of primitives
- Validate inputs using type methods
- Use enums for fixed value sets
- Embed common fields (timestamps, IDs) consistently
- Keep types focused and cohesive

## License

Apache License 2.0
