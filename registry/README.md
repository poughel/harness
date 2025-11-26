# Registry Module

The `registry` module provides container registry functionality for storing and distributing Docker images and OCI artifacts.

## Overview

This module implements a complete container registry compatible with Docker Registry HTTP API V2 and OCI Distribution Specification. It allows users to push, pull, and manage container images and artifacts.

## Features

- Docker Registry V2 API support
- OCI Distribution Specification compliance
- Image layer storage and management
- Manifest management (Docker and OCI formats)
- Tag management
- Garbage collection
- Upstream proxy support for caching remote registries
- Webhook notifications
- Access control and authentication
- Image scanning integration
- Registry API documentation

## Structure

The module is organized into several sub-packages:

- **app**: Main application logic and API handlers
- **config**: Registry configuration
- **docs**: Swagger documentation
- **gc**: Garbage collection for unused artifacts
- **request**: Request handling utilities
- **services**: Core registry services
- **tests**: Conformance and integration tests
- **types**: Registry-specific types
- **utils**: Helper utilities
- **validation**: Input validation

## Key Components

### Registry Services
- Image storage and retrieval
- Manifest validation and storage
- Blob storage and deduplication
- Tag management
- Repository management

### Upstream Proxy
- Cache remote registry images
- Reduce bandwidth and latency
- Support for Docker Hub, GCR, etc.

### Garbage Collection
- Remove unreferenced blobs
- Reclaim storage space
- Configurable retention policies

## API Endpoints

The registry implements standard endpoints:
- `/v2/`: API version check
- `/v2/<name>/manifests/<reference>`: Manifest operations
- `/v2/<name>/blobs/<digest>`: Blob operations
- `/v2/<name>/tags/list`: List tags
- And more...

## Usage

### Push an Image
```bash
docker tag myimage:latest registry.example.com/myrepo/myimage:latest
docker push registry.example.com/myrepo/myimage:latest
```

### Pull an Image
```bash
docker pull registry.example.com/myrepo/myimage:latest
```

### Registry Configuration
```go
config := registry.Config{
    Storage: storageConfig,
    Auth: authConfig,
    Proxy: proxyConfig,
}
```

## Storage

Supports multiple storage backends through the blob storage abstraction:
- Local filesystem
- Cloud storage (GCS, S3, Azure Blob)

## Conformance

The module includes OCI Distribution Spec conformance tests:
```bash
make conformance-test
```

## License

Apache License 2.0
