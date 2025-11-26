# Secret Module

The `secret` module provides secret management and decryption services for the Harness application.

## Overview

This module handles the secure storage and retrieval of secrets used in pipelines, integrations, and other parts of the system. It provides a service interface for decrypting secrets in the context of a specific space.

## Features

- Secure secret storage
- Space-scoped secret access
- Secret decryption service
- Integration with pipeline execution
- Secret lifecycle management
- Access control enforcement

## Key Interface

### Service
The main interface providing:
- `DecryptSecret`: Decrypt a secret by identifier within a space context

## Usage

```go
// Create secret service
secretService := secret.NewService(...)

// Decrypt a secret
plaintext, err := secretService.DecryptSecret(
    ctx,
    "/root/my-project",  // space path
    "my-secret-id",       // secret identifier
)
if err != nil {
    return err
}

// Use decrypted secret
// (e.g., in pipeline, API calls, etc.)
```

## Security Considerations

- Secrets are encrypted at rest
- Decryption requires proper authorization
- Space-based access control
- Secrets are never logged or exposed in error messages
- Temporary decryption in memory only
- No caching of decrypted values

## Use Cases

- Pipeline secret variables
- Integration credentials (Git, cloud providers)
- API tokens and keys
- Database passwords
- SSH keys
- Webhook secrets
- Third-party service credentials

## Integration

The secret service integrates with:
- Pipeline execution engine
- Encryption module for secure storage
- Authorization system for access control
- Audit logging for secret access tracking

## Best Practices

- Use specific secret identifiers
- Grant minimal required access
- Rotate secrets regularly
- Use space-scoped secrets for better isolation
- Never log or print decrypted secrets
- Handle decryption errors appropriately

## Secret Types

Supports various secret formats:
- Simple text secrets
- Key-value pairs
- File secrets (certificates, keys)
- SSH keys

## License

Apache License 2.0
