# Store Module

The `store` module provides data persistence and database operations for the Harness application.

## Overview

This module contains all database-related code including data access objects (DAOs), database migrations, and data models. It provides an abstraction layer over the database for storing and retrieving application data.

## Features

- Database connection management
- Data access objects (DAOs) for all entities
- Database migrations
- Transaction management
- Query builders
- Connection pooling
- Database error handling

## Structure

The module is organized with:
- **database**: Core database implementations and DAOs

## Supported Entities

The store manages persistence for:
- Users and principals
- Repositories
- Spaces (projects)
- Pull requests
- Pipelines and executions
- Webhooks
- Secrets
- Tokens
- SSH keys
- Settings and configurations
- Audit logs
- Registry artifacts
- And more...

## Database Support

Primary database: PostgreSQL

## Usage

```go
// Get a DAO instance
repoStore := store.NewRepositoryStore(db)

// Create a repository
repo := &types.Repository{
    Identifier: "my-repo",
    Path: "/space/my-repo",
    // ... other fields
}
err := repoStore.Create(ctx, repo)

// Find a repository
repo, err := repoStore.Find(ctx, repoID)

// Update a repository
repo.Description = "Updated description"
err = repoStore.Update(ctx, repo)

// List repositories
repos, err := repoStore.List(ctx, parentID, filter)

// Delete a repository
err = repoStore.Delete(ctx, repoID)
```

## Transaction Management

```go
// Execute in transaction
err := db.InTransaction(ctx, func(tx *sql.Tx) error {
    // Multiple database operations
    err := repoStore.Create(ctx, repo)
    if err != nil {
        return err // Rolls back
    }
    
    err = auditStore.Log(ctx, event)
    if err != nil {
        return err // Rolls back
    }
    
    return nil // Commits
})
```

## Migrations

Database schema migrations are managed using migration tools:
```bash
# Run migrations
./gitness migrate up

# Rollback migration
./gitness migrate down
```

## Error Handling

Common store errors:
- `ErrNotFound`: Resource not found
- `ErrDuplicate`: Duplicate key violation
- `ErrVersionConflict`: Optimistic locking conflict
- `ErrForeignKeyViolation`: Foreign key constraint violation

## Best Practices

- Use transactions for related operations
- Handle `ErrNotFound` appropriately
- Use filters and pagination for large result sets
- Index frequently queried fields
- Use prepared statements (handled automatically)
- Close result sets and readers

## Performance

- Connection pooling for efficiency
- Indexed queries for fast lookups
- Batch operations where possible
- Query optimization
- Proper use of database constraints

## License

Apache License 2.0
