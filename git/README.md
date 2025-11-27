# Git Module Documentation

This directory contains the Git module implementation for Harness Open Source, providing comprehensive Git repository management and operations.

## Overview

The Git module is a core component that abstracts Git operations, providing a clean, safe, and performant interface for repository management, version control, and source code operations.

## Documentation

### 📖 [Implementation Analysis](./IMPLEMENTATION_ANALYSIS.md)
Comprehensive analysis of the Git module's implementation principles, including:
- Architecture and design patterns
- Core components and their responsibilities
- Security considerations
- Performance optimizations
- Error handling strategies
- Extension points and customization
- Testing considerations

**Start here** if you want to understand how the module works internally.

### 📊 [Architecture Diagrams](./ARCHITECTURE_DIAGRAMS.md)
Visual representations of the system architecture, including:
- Component hierarchy
- Data flow diagrams
- Request/response flows
- Hook execution flow
- Caching strategy
- Error propagation
- State machines

**Start here** if you prefer visual learning or need to understand system interactions.

## Quick Start

### Using the Git Service

```go
import (
    "github.com/harness/gitness/git"
    "github.com/harness/gitness/git/api"
    "github.com/harness/gitness/git/hook"
    "github.com/harness/gitness/git/storage"
    "github.com/harness/gitness/git/types"
)

// Create Git service
config := types.Config{
    Root:     "/data/repos",
    HookPath: "/usr/local/bin/githook",
}

gitAdapter, _ := api.New(config, cache, hookFactory)
gitService, _ := git.New(config, gitAdapter, hookFactory, storage)

// Create a repository
output, err := gitService.CreateRepository(ctx, &git.CreateRepositoryParams{
    RepoUID:       "unique-repo-id",
    Actor:         git.Identity{Name: "User", Email: "user@example.com"},
    DefaultBranch: "main",
})
```

### Available Operations

The `Interface` in [interface.go](./interface.go) defines all available operations:

#### Repository Management
- `CreateRepository` - Initialize a new Git repository
- `DeleteRepository` - Remove a repository
- `SyncRepository` - Sync from a remote repository
- `GetRepositorySize` - Calculate repository size
- `OptimizeRepository` - Run garbage collection and optimization

#### Branch Operations
- `CreateBranch` - Create a new branch
- `DeleteBranch` - Delete a branch
- `GetBranch` - Get branch details
- `ListBranches` - List all branches
- `UpdateDefaultBranch` - Change default branch

#### Commit Operations
- `GetCommit` - Get commit details
- `ListCommits` - List commits
- `CommitFiles` - Commit file changes
- `GetCommitDivergences` - Calculate branch divergence
- `MergeBase` - Find merge base

#### File Operations
- `GetBlob` - Read file content
- `GetTreeNode` - Get file/directory metadata
- `ListTreeNodes` - List directory contents
- `ListPaths` - List all paths in tree

#### Diff Operations
- `Diff` - Get structured diff
- `RawDiff` - Get patch format diff
- `DiffFileNames` - List changed files
- `DiffShortStat` - Get change statistics
- `DiffStats` - Get per-file statistics

#### Merge Operations
- `Merge` - Merge branches
- `Revert` - Revert a commit

#### Tag Operations
- `CreateCommitTag` - Create a tag
- `DeleteTag` - Delete a tag
- `ListCommitTags` - List tags for commit

#### Advanced Operations
- `Blame` - Get line-by-line blame
- `Archive` - Create archive of repository
- `ScanSecrets` - Scan for secrets
- `MatchFiles` - Match files by pattern

## Module Structure

```
git/
├── README.md                     # This file
├── IMPLEMENTATION_ANALYSIS.md    # Implementation deep dive
├── ARCHITECTURE_DIAGRAMS.md      # Visual architecture guide
│
├── interface.go                  # Service interface definition
├── service.go                    # Service implementation
├── wire.go                       # Dependency injection
│
├── repo.go                       # Repository operations
├── branch.go                     # Branch operations
├── commit.go                     # Commit operations
├── operations.go                 # File operations
├── diff.go                       # Diff operations
├── merge.go                      # Merge operations
├── tag.go                        # Tag operations
├── blob.go                       # Blob operations
├── tree.go                       # Tree operations
├── ref.go                        # Reference operations
│
├── params.go                     # Parameter types
├── common.go                     # Common utilities
├── errors.go                     # Error definitions
│
├── api/                          # Git API layer
│   ├── api.go                    # API adapter
│   ├── repo.go                   # Repository API
│   ├── commit.go                 # Commit API
│   ├── branch.go                 # Branch API
│   ├── diff.go                   # Diff API
│   └── ...
│
├── command/                      # Git command builder
│   ├── command.go                # Command construction
│   ├── builder.go                # Builder pattern
│   ├── parser.go                 # Output parsing
│   └── error.go                  # Error handling
│
├── hook/                         # Git hook system
│   ├── client.go                 # Hook client interface
│   ├── types.go                  # Hook types
│   └── ...
│
├── storage/                      # Storage abstraction
│   ├── storage.go                # Storage interface
│   └── ...
│
├── types/                        # Type definitions
│   └── config.go                 # Configuration types
│
└── [other supporting modules]
```

## Key Design Principles

1. **Security First**: Multiple layers of input validation and command escaping
2. **Performance**: Caching, streaming, and optimized operations
3. **Testability**: Interface-based design with dependency injection
4. **Extensibility**: Hook system, storage abstraction, pluggable components
5. **Reliability**: Atomic operations, comprehensive error handling

## Configuration

The Git module is configured through `types.Config`:

```go
type Config struct {
    // Trace enables command tracing for debugging
    Trace bool
    
    // Root is the directory containing repositories
    Root string
    
    // TmpDir is used for temporary data
    TmpDir string
    
    // HookPath points to the Git hook binary
    HookPath string
    
    // LastCommitCache configuration
    LastCommitCache LastCommitCacheConfig
}
```

## Security Considerations

The Git module implements multiple security layers:

- **Command Injection Prevention**: Safe command construction with proper escaping
- **Path Traversal Protection**: Repository UID-based isolation
- **Hook Validation**: Pre-receive hooks enforce policies
- **Secret Scanning**: Built-in secret detection
- **Size Limits**: Configurable limits on repository and file sizes

See [IMPLEMENTATION_ANALYSIS.md](./IMPLEMENTATION_ANALYSIS.md#security-considerations) for details.

## Performance Optimization

The module includes several performance optimizations:

- **Last Commit Cache**: Reduces repeated Git operations
- **Streaming Operations**: Memory-efficient processing of large data
- **Shared Repository Pool**: Reuses temporary repositories
- **Selective Hook Execution**: Only essential hooks enabled
- **Repository Optimization**: Automatic garbage collection and repacking

See [IMPLEMENTATION_ANALYSIS.md](./IMPLEMENTATION_ANALYSIS.md#performance-optimizations) for details.

## Testing

The module is designed for testability:

- Interface-based design allows mocking
- Dependency injection enables test doubles
- Timestamp control for deterministic tests
- Repository UID control for predictable paths

Example test:
```go
func TestCreateRepository(t *testing.T) {
    mockStorage := &mockStorage{}
    mockHookFactory := &mockHookFactory{}
    
    service, _ := git.New(testConfig, mockAdapter, mockHookFactory, mockStorage)
    
    output, err := service.CreateRepository(ctx, &git.CreateRepositoryParams{
        RepoUID:       "test-repo",
        Actor:         git.Identity{Name: "Test", Email: "test@example.com"},
        DefaultBranch: "main",
    })
    
    assert.NoError(t, err)
    assert.Equal(t, "test-repo", output.UID)
}
```

## Contributing

When contributing to the Git module:

1. Understand the architecture by reading the documentation
2. Follow existing patterns and conventions
3. Add tests for new functionality
4. Update documentation for API changes
5. Consider security implications
6. Ensure performance impact is acceptable

## Related Documentation

- [Main Repository README](../README.md) - Project overview
- [API Documentation](../app/api/) - REST API documentation
- [Hook System](./hook/) - Git hook details
- [Storage System](./storage/) - Storage backend details

## Support

For questions or issues:
- Open an issue on GitHub
- Check existing issues and documentation
- Review the implementation analysis for understanding

## License

Apache License 2.0 - See [LICENSE](../LICENSE) for details.
