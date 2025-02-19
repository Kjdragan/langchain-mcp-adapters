# Fast MCP Guide

## Overview
Fast MCP is a high-level Python framework for building Model Context Protocol (MCP) servers. It's designed to be fast, simple, and Pythonic, providing a complete implementation of the core MCP specification.

## Key Features
- **Fast Development**: High-level interface reduces boilerplate code
- **Simple API**: Minimal code required to build MCP servers
- **Pythonic Design**: Natural for Python developers
- **Complete Spec**: Aims to fully implement the MCP specification

## Core Concepts

### 1. Server
The core interface to the MCP protocol, handling:
- Connection management
- Protocol compliance
- Message routing

Example:
```python
from fastmcp import FastMCP

mcp = FastMCP("My App", dependencies=["youtube-transcript-api"])
```

### 2. Resources
Similar to GET endpoints in REST APIs:
- Provide data without significant computation
- Can be static or dynamic
- Support parameterized templates

Example:
```python
@mcp.resource("config://app")
def get_config() -> str:
    """Static configuration data"""
    return "App configuration here"

@mcp.resource("users://{user_id}/profile")
def get_user_profile(user_id: str) -> str:
    """Dynamic user data"""
    return f"Profile data for user {user_id}"
```

### 3. Tools
Similar to POST endpoints in REST APIs:
- Perform computation and have side effects
- Support complex input validation via Pydantic
- Can be synchronous or asynchronous

Example:
```python
from pydantic import BaseModel

class TranscriptRequest(BaseModel):
    video_id: str

@mcp.tool()
async def get_transcript(request: TranscriptRequest) -> str:
    """Get transcript for a YouTube video"""
    # Implementation here
```

### 4. Prompts
Reusable templates for LLM interactions:
- Can be simple strings or structured message sequences
- Help guide LLM behavior
- Support dynamic content

Example:
```python
@mcp.prompt()
def review_code(code: str) -> str:
    return f"Please review this code:\n\n{code}"
```

## Best Practices
1. Use Pydantic models for complex input validation
2. Leverage async functions for I/O operations
3. Provide clear docstrings for tools and resources
4. Structure servers into logical modules
5. Use type hints for better code clarity

## Project Structure
For a YouTube transcript MCP server:
```
mcp_servers/
    youtube/
        __init__.py
        server.py      # Main MCP server
        models.py      # Pydantic models
        tools.py       # Tool implementations
        resources.py   # Resource implementations
```

## Development Notes
- Fast MCP is under active development
- Some advanced features may still be in progress
- Core functionality is stable and working
