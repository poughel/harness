# Job Module

The `job` module provides a background job scheduling and execution framework.

## Overview

This module implements a robust job scheduler that handles recurring and one-time background tasks. It supports distributed execution, job persistence, and automatic retry mechanisms.

## Features

- Cron-based job scheduling
- One-time and recurring jobs
- Distributed job execution
- Job persistence and recovery
- Automatic retry with backoff
- Job locking to prevent duplicates
- Job lifecycle management
- Job history and retention

## Key Components

### Scheduler
- Manages job registration and execution
- Handles job triggers and timing
- Coordinates job distribution across instances
- Implements job locking mechanisms

### Executor
- Executes job handlers
- Manages job concurrency
- Handles job timeouts
- Implements retry logic

### Store
- Persists job definitions and state
- Tracks job execution history
- Manages job retention policies

## Usage

```go
// Define a job
job := job.Definition{
    UID: "my-job",
    Type: "cleanup",
    Cron: "0 0 * * *", // Daily at midnight
    MaxRetries: 3,
}

// Register a job handler
scheduler.Register(job, func(ctx context.Context) error {
    // Job implementation
    return performCleanup(ctx)
})

// Start the scheduler
err := scheduler.Start(ctx)

// Trigger a job manually
err := scheduler.Trigger(ctx, jobUID)
```

## Job Types

The module supports various built-in job types:
- Repository cleanup and maintenance
- Log retention and archival
- Metric aggregation
- System health checks
- Data synchronization
- Custom application jobs

## Configuration

```go
config := job.Config{
    InstanceID: "instance-1",
    MaxRunning: 10,
    RetentionTime: 30 * 24 * time.Hour,
}
```

## Distribution

In multi-instance deployments:
- Jobs are distributed using distributed locks
- Only one instance executes a job at a time
- Failed jobs can be picked up by other instances
- State is shared via persistent storage

## License

Apache License 2.0
