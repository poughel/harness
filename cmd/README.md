# CMD Module

The `cmd` module contains the main entry points and command definitions for the Harness application.

## Overview

This module provides the command-line application structure, including the main executable entry point and command definitions. It uses the `gitness` binary name as the primary command-line tool.

## Structure

The module is organized with:
- **gitness**: Main application entry point and command definitions

## Key Responsibilities

- Application bootstrapping and initialization
- Command-line argument parsing
- Command routing and execution
- Configuration loading
- Logging setup
- Graceful shutdown handling

## Available Commands

The Harness CLI provides several top-level commands:
- `server`: Start the Harness server
- `login`: Authenticate with a Harness instance
- `user`: User and token management operations
- `repo`: Repository management
- `space`: Space/project management
- And more...

## Usage

```bash
# Start the server
./gitness server .local.env

# Generate Swagger documentation
./gitness swagger > swagger.yaml

# View all available commands
./gitness --help

# View help for a specific command
./gitness server --help
```

## Configuration

The application can be configured through:
- Environment files (e.g., `.local.env`)
- Environment variables
- Command-line flags
- Configuration files

## License

Apache License 2.0
