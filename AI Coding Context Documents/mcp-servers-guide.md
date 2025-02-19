# MCP Servers Guide

## Overview
MCP (Modular Command Protocol) servers are self-contained services that can be used by Cascade or other clients without requiring the client project to install their dependencies. This guide explains how they work and how to develop them.

## How MCP Servers Work

### Self-Contained Nature
1. **Independent Operation**
   - Each MCP server runs as a separate process
   - Has its own virtual environment and dependencies
   - Client projects (like your other projects using Cascade) don't need to install MCP server dependencies

2. **Dependency Management**
   - Dependencies are defined in `pyproject.toml`
   - When an MCP server starts, it ensures its own dependencies are available
   - The client project doesn't need to worry about these dependencies

### Project Structure
```
kev-made-mcp/
├── pyproject.toml          # Project-wide configuration
├── mcp_servers/           # Main package directory
│   ├── __init__.py
│   ├── youtube/          # YouTube MCP server
│   │   ├── __init__.py
│   │   ├── server.py    # Server implementation
│   │   └── README.md    # Server-specific documentation
│   └── other_mcp/       # Other MCP servers follow same pattern
```

## Development vs Usage

### For MCP Server Development
When developing MCP servers in this repository:
1. You need the development environment set up
2. Run `uv pip install -e .[youtube]` to install dependencies for development
3. This installs the package in "editable" mode for testing

### For MCP Server Usage
When using these MCP servers in other projects:
1. No need to install anything in your project
2. Cascade handles starting the MCP server process
3. The MCP server manages its own dependencies
4. Your project communicates with the MCP server through Cascade

## Example: YouTube MCP Server

### As a Developer
If you're working on the YouTube MCP server:
```bash
# Only needed for development
uv pip install -e .[youtube]
```

### As a User
If you're using the YouTube MCP server in another project:
1. No installation needed
2. Just use Cascade's tools to interact with the YouTube MCP
3. The server handles its own dependency management

## Adding New MCP Servers

1. Create a new directory under `mcp_servers/`
2. Add server-specific dependencies to `pyproject.toml` under `[project.optional-dependencies]`
3. Implement the server following the MCP protocol
4. Add documentation in the server's directory

## Best Practices

1. **Dependency Management**
   - Keep core dependencies in main `dependencies` list
   - Put server-specific dependencies under `optional-dependencies`
   - Use `uv` for package management

2. **Documentation**
   - Each MCP server should have its own README
   - Document all endpoints and features
   - Include usage examples

3. **Testing**
   - Test servers in isolation
   - Verify self-contained operation
   - Test with Cascade integration

## Summary
- MCP servers are self-contained services
- They manage their own dependencies
- Client projects don't need to install anything
- Development environment is separate from usage environment
