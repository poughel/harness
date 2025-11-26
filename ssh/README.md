# SSH Module

The `ssh` module provides SSH server functionality for Git operations over SSH.

## Overview

This module implements an SSH server that allows users to interact with Git repositories using SSH protocol. It handles SSH authentication, Git command execution, and secure communication.

## Features

- SSH server for Git operations
- Public key authentication
- Git command handling (git-upload-pack, git-receive-pack)
- Repository access control
- User authentication integration
- Secure command execution
- Session management

## Supported Operations

- `git clone` over SSH
- `git push` over SSH
- `git pull` over SSH
- `git fetch` over SSH

## Usage

### Server Configuration
```go
config := ssh.Config{
    Address: ":3022",
    HostKeys: hostKeys,
    // Authentication and repository handlers
}

server := ssh.NewServer(config)
err := server.Start()
```

### Client Usage
```bash
# Clone a repository via SSH
git clone ssh://git@harness.example.com:3022/project/repo.git

# Push changes
git push origin main

# Configure SSH key
ssh-keygen -t ed25519 -C "user@example.com"
# Add public key to Harness user profile
```

## Authentication

Supports SSH public key authentication:
- Users add SSH public keys to their profile
- Server validates keys during connection
- Authorized keys are associated with user accounts
- No password authentication (more secure)

## Access Control

- Repository-level access control
- Integration with Harness permission system
- Read/write access validation
- Audit logging of SSH operations

## Security

- Host key verification
- Public key authentication only
- Encrypted communication
- Command whitelisting (only Git commands)
- Rate limiting
- Connection timeouts

## Configuration

```go
config := ssh.Config{
    Address: ":3022",
    MaxConnections: 100,
    Timeout: 30 * time.Second,
    // Host keys for server identity
    HostKeys: []ssh.Signer{hostKey},
}
```

## Integration

Works with:
- Git module for repository operations
- Authentication system for user validation
- Audit module for logging
- Store module for user SSH key management

## Troubleshooting

Common issues:
- SSH key not authorized: Add key to user profile
- Permission denied: Check repository access rights
- Connection timeout: Verify network and firewall settings

## License

Apache License 2.0
