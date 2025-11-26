# CLI Module

The `cli` module provides command-line interface functionality for the Harness application.

## Overview

This module contains the implementation of the command-line tool that allows users to interact with the Harness system from the terminal. It provides various operations for managing repositories, pipelines, users, and other resources.

## Structure

The module is organized into several sub-packages:

- **operations**: Contains implementations of CLI commands and operations
- **provide**: Dependency injection providers for CLI components
- **session**: Session management and authentication
- **textui**: Text-based user interface components and formatters

## Key Features

- User authentication and session management
- Repository operations
- Pipeline management
- User and token management
- Interactive and non-interactive modes
- Formatted output (tables, JSON, etc.)

## Usage

The CLI is typically built as part of the main `gitness` binary:

```bash
# Login to Harness
./gitness login

# Create a personal access token
./gitness user pat "my-pat-uid" 2592000

# List repositories
./gitness repo list

# View help
./gitness --help
```

## Components

- Command parsing and routing
- HTTP client for API communication
- Output formatting and pretty printing
- Progress indicators and status messages
- Error handling and user-friendly messages

## License

Apache License 2.0
