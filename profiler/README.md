# Profiler Module

The `profiler` module provides application profiling and performance monitoring capabilities.

## Overview

This module integrates profiling tools to help monitor and optimize application performance. It supports multiple profiling backends including Google Cloud Profiler for production monitoring.

## Features

- CPU profiling
- Memory profiling
- Goroutine profiling
- Heap profiling
- Integration with cloud profiling services
- Production-safe profiling
- Continuous profiling support

## Profiler Implementations

- **GCPProfiler**: Google Cloud Profiler integration for production profiling
- **NoopProfiler**: No-op implementation for development or when profiling is disabled

## Usage

```go
// Enable GCP profiler
profiler, err := profiler.NewGCPProfiler(config)
if err != nil {
    log.Fatal(err)
}
defer profiler.Stop()

// Or use noop profiler (disabled)
profiler := profiler.NewNoop()
```

## Configuration

```go
config := profiler.Config{
    Enabled: true,
    Service: "harness",
    Version: "1.0.0",
    ProjectID: "my-gcp-project",
}
```

## What Gets Profiled

- CPU usage patterns
- Memory allocation and usage
- Goroutine creation and blocking
- Mutex contention
- Thread creation
- Heap allocations

## Use Cases

- Performance optimization
- Memory leak detection
- CPU bottleneck identification
- Goroutine leak detection
- Production performance monitoring
- Performance regression detection

## Best Practices

- Enable profiling in production for continuous monitoring
- Use sampling-based profiling to minimize overhead
- Review profiles regularly to identify optimization opportunities
- Correlate profiling data with application metrics
- Profile under realistic load conditions

## Integration

Works with:
- Google Cloud Profiler console
- pprof tools for local analysis
- Performance monitoring dashboards
- Alerting systems

## License

Apache License 2.0
