# Git Module

The `git` module provides comprehensive Git operations and repository management functionality.

## Overview

This module is a core component that wraps Git functionality and provides high-level abstractions for working with Git repositories. It handles all Git-related operations including commits, branches, merges, diffs, and more.

## Key Sub-packages

- **api**: HTTP API integration for Git operations
- **check**: Git repository validation and checks
- **command**: Low-level Git command execution
- **diff**: Diff parsing and manipulation
- **enum**: Enumerations for Git-related types
- **hash**: Git object hash utilities
- **hook**: Git hook management and execution
- **maintenance**: Repository maintenance operations
- **merge**: Merge strategies and conflict resolution
- **parser**: Git output parsing utilities
- **sha**: SHA handling and validation
- **sharedrepo**: Shared repository management
- **storage**: Repository storage abstraction
- **tempdir**: Temporary directory management
- **types**: Git-specific type definitions

## Main Operations

- **Repository Management**: Clone, init, delete
- **Branch Operations**: Create, delete, list, protect
- **Commit Operations**: Create, amend, revert, cherry-pick
- **Diff Operations**: Generate diffs, parse patches
- **Merge Operations**: Merge branches, resolve conflicts
- **Tag Operations**: Create, delete, list tags
- **Blame**: File annotation with commit information
- **Tree Operations**: Browse repository trees
- **Ref Operations**: Manage Git references

## Usage

```go
// Initialize a Git repository
repo, err := git.InitRepository(ctx, path)

// Create a branch
err = git.CreateBranch(ctx, repoPath, branchName, targetSHA)

// Commit changes
sha, err := git.Commit(ctx, repoPath, params)

// Get commit information
commit, err := git.GetCommit(ctx, repoPath, sha)

// Generate diff
diff, err := git.Diff(ctx, repoPath, baseSHA, headSHA)
```

## Features

- Full Git protocol support
- Optimized operations for large repositories
- Pre-receive and post-receive hook support
- Secret scanning integration
- Repository optimization and maintenance
- GPG signature verification
- Submodule support

## Performance Optimizations

- Shared repository mode for space efficiency
- Incremental operations where possible
- Caching of frequently accessed data
- Batch operations support

## License

Apache License 2.0
