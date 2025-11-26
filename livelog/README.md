# Livelog Module

The `livelog` module provides real-time log streaming functionality for pipeline execution.

## Overview

This module implements a live log streaming system that allows real-time viewing of build and pipeline execution logs. It supports multiple concurrent subscribers and provides efficient log distribution.

## Features

- Real-time log streaming
- Multiple concurrent subscribers per log stream
- Line-by-line log delivery
- Timestamp tracking
- In-memory log buffering
- Stream lifecycle management
- Subscriber management

## Key Components

### LogStream Interface
The main interface providing:
- `Create`: Initialize a log stream for a step
- `Delete`: Clean up a log stream
- `Write`: Append log lines to the stream
- `Tail`: Subscribe to log updates
- `Info`: Get stream statistics

### Line
Represents a single log line with:
- Line number
- Message content
- Timestamp

## Usage

```go
// Create a log stream
err := logStream.Create(ctx, stepID)

// Write log lines
line := &livelog.Line{
    Number: 1,
    Message: "Starting build...",
    Timestamp: time.Now().Unix(),
}
err = logStream.Write(ctx, stepID, line)

// Subscribe to logs
logChan, errChan := logStream.Tail(ctx, stepID)
for {
    select {
    case line := <-logChan:
        fmt.Println(line.Message)
    case err := <-errChan:
        if err != nil {
            log.Fatal(err)
        }
        return
    }
}

// Clean up
err = logStream.Delete(ctx, stepID)
```

## Implementation

The module uses an in-memory pub-sub system for efficient log distribution:
- Publishers write log lines to a central stream
- Subscribers receive log lines in real-time
- Each stream maintains its own subscriber list
- Automatic cleanup when streams are deleted

## Use Cases

- Pipeline execution logs
- Build output streaming
- Test execution logs
- Real-time debugging
- Log viewing in web UI
- CLI log tailing

## Performance

- Efficient memory usage with line buffering
- Low latency log delivery
- Handles high throughput log streams
- Automatic subscriber cleanup

## License

Apache License 2.0
