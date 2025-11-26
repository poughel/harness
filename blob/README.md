# Blob Module

The `blob` module provides an abstraction layer for blob storage operations, supporting multiple storage backends.

## Overview

This module offers a unified interface for storing and retrieving binary large objects (blobs) across different storage providers. It supports both local filesystem storage and cloud storage solutions.

## Supported Storage Backends

- **Filesystem**: Local file system storage
- **Google Cloud Storage (GCS)**: Cloud-based object storage

## Key Interfaces

### Store
The main interface providing:
- `Upload`: Upload a file to blob storage
- `Download`: Download a file from blob storage
- `GetSignedURL`: Generate time-limited signed URLs for secure file access

## Usage

```go
// Upload a file
err := store.Upload(ctx, fileReader, "path/to/file")

// Download a file
reader, err := store.Download(ctx, "path/to/file")

// Get a signed URL
url, err := store.GetSignedURL(ctx, "path/to/file", expireTime)
```

## Configuration

The module uses a configuration system to specify:
- Storage provider type
- Provider-specific settings (paths, credentials, etc.)
- Upload/download options

## License

Apache License 2.0
