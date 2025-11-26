# Version Module

The `version` module manages application version information for the Harness application.

## Overview

This module provides version tracking and reporting capabilities. It exposes the current version, build information, and Git commit details that can be used for debugging, monitoring, and display purposes.

## Features

- Application version tracking
- Build information (build time, commit SHA)
- Git version information
- Version reporting for API and CLI
- Semantic versioning support

## Version Information

The module typically exposes:
- **Version**: Semantic version (e.g., "1.0.0")
- **Commit**: Git commit SHA
- **BuildDate**: When the binary was built
- **GoVersion**: Go compiler version used

## Usage

```go
import "github.com/harness/gitness/version"

// Get version information
v := version.Get()
fmt.Printf("Version: %s\n", v.Version)
fmt.Printf("Commit: %s\n", v.Commit)
fmt.Printf("Built: %s\n", v.BuildDate)

// Display version
version.Print()
```

## Build-time Injection

Version information is typically injected at build time using linker flags:

```bash
go build -ldflags "\
  -X github.com/harness/gitness/version.Version=${VERSION} \
  -X github.com/harness/gitness/version.Commit=${GIT_COMMIT} \
  -X github.com/harness/gitness/version.BuildDate=${BUILD_DATE}"
```

## CLI Usage

Version information can be displayed via CLI:

```bash
./gitness version
# Output:
# Version: 1.0.0
# Commit: abc123def456
# Built: 2024-01-15T10:30:00Z
```

## API Integration

Version information is often exposed via API:
```
GET /api/v1/version
{
  "version": "1.0.0",
  "commit": "abc123def456",
  "buildDate": "2024-01-15T10:30:00Z"
}
```

## Use Cases

- Display version in UI/CLI
- Include in bug reports
- Version compatibility checks
- Monitoring and observability
- Release tracking
- Debugging and support

## Semantic Versioning

Follows semantic versioning (semver):
- MAJOR version for incompatible API changes
- MINOR version for backwards-compatible functionality
- PATCH version for backwards-compatible bug fixes

Example: v1.2.3
- 1 = Major version
- 2 = Minor version
- 3 = Patch version

## Best Practices

- Always inject version at build time
- Include commit SHA for traceability
- Display version in logs on startup
- Include version in error reports
- Use version for feature flags or compatibility checks

## License

Apache License 2.0
