# Lessons Learned: Python Environment and MCP Development

## Environment Management

### UV Package Manager

1. **Installation Commands**
   - DO use `uv pip install -e .` for installing the project in editable mode
   - DON'T use `uv add -e .` (incorrect syntax)
   - DO use `uv pip install -e .[dev]` for installing with optional dependencies
   - DON'T use `uv add -e .[dev]` (incorrect syntax)

2. **Package Management**
   - DO use `uv add package_name` for adding new packages
   - DO use `uv pip install -e .` after modifying pyproject.toml
   - DON'T use `pip install` as it won't update pyproject.toml

### Virtual Environment

1. **Activation**
   ```powershell
   # In PowerShell
   .\.venv\Scripts\Activate.ps1
   ```
   - Always activate the virtual environment before running commands
   - Use backslashes for paths in Windows
   - Use `.\.venv\Scripts\python.exe` to ensure using the correct Python

2. **Common Issues**
   - Error: "The module '.venv' could not be loaded" → Need to activate venv first
   - Error: "No module named 'pytest'" → Need to install dev dependencies
   - Error: "Python not found" → Use full path to Python in .venv

## Project Structure

### Build System

1. **Hatch Configuration**
   ```toml
   [build-system]
   requires = ["hatchling"]
   build-backend = "hatchling.build"

   [tool.hatch.build.targets.wheel]
   packages = ["mcp_servers"]
   ```
   - Must specify packages to include
   - Error: "Unable to determine which files to ship" → Missing packages configuration

2. **Dependencies**
   ```toml
   [project.optional-dependencies]
   youtube = ["youtube-transcript-api"]
   dev = ["pytest>=7.4.0", "pytest-asyncio>=0.23.0"]
   ```
   - Use optional dependencies for feature-specific packages
   - Include version constraints for critical dependencies

### Testing

1. **Pytest Configuration**
   ```toml
   [tool.pytest.ini_options]
   asyncio_mode = "strict"
   testpaths = ["mcp_servers"]
   python_files = ["test_*.py"]
   python_classes = ["Test*"]
   python_functions = ["test_*"]
   addopts = "-v --tb=short"
   asyncio_default_fixture_loop_scope = "function"
   ```
   - Configure pytest-asyncio to avoid warnings
   - Set explicit test paths and patterns
   - Use function scope for async fixtures

2. **Pytest-Asyncio Configuration**
   When using pytest-asyncio, it's important to properly configure the event loop scope for async fixtures. Add this configuration to the `[tool.pytest.ini_options]` section in `pyproject.toml`:

   ```toml
   [tool.pytest.ini_options]
   asyncio_default_fixture_loop_scope = "function"
   ```
   This prevents the deprecation warning: "The configuration option 'asyncio_default_fixture_loop_scope' is unset". The warning indicates that future versions of pytest-asyncio will default to function scope, so it's best to set this explicitly now.

   Note: Do not put this configuration in a separate `[tool.pytest-asyncio]` section - it must be in `[tool.pytest.ini_options]` to work correctly.

3. **Common Test Issues**
   - Mock initialization errors → Need to provide all required arguments
   - Async test failures → Missing pytest.mark.asyncio decorator
   - Import errors → Incorrect relative imports in tests

## MCP Server Development

### Fast MCP Framework

1. **Dependencies**
   - Add `fastmcp` to main dependencies
   - Add feature-specific packages to optional dependencies
   - Use `mcp[cli]` for development tools

2. **Server Structure**
   ```
   mcp_servers/
   ├── server_name/
   │   ├── __init__.py
   │   ├── server.py     # Main MCP server
   │   ├── models.py     # Pydantic models
   │   ├── tools.py      # Tool implementations
   │   ├── resources.py  # Resource implementations
   │   └── utils.py      # Utility functions
   ```
   - Separate concerns into different modules
   - Use Pydantic models for validation
   - Keep utility functions isolated

### Testing MCP Servers

1. **Test Structure**
   ```
   tests/
   ├── __init__.py
   ├── conftest.py      # Shared fixtures
   ├── test_models.py   # Model validation
   ├── test_tools.py    # Tool implementations
   ├── test_resources.py # Resource endpoints
   └── test_utils.py    # Utility functions
   ```
   - Organize tests by component
   - Use fixtures for common setup
   - Mock external services

2. **Mocking External APIs**
   - Provide all required arguments to mock exceptions
   - Use context managers for mock setup
   - Handle async operations properly

## YouTube MCP Server Configuration Lessons

### Key Success Factors
1. **Virtual Environment Isolation**
   - Always use uv for venv creation: `uv venv -p 3.12`
   - Path must use backslashes in PowerShell: `.\\.venv\Scripts\Activate.ps1`

2. **Dependency Management**
   - pyproject.toml must specify:
   ```toml
   [project]
   dependencies = [
     "mcp",
     "fastmcp",
     "youtube-transcript-api>=0.6.3"
   ]
   ```
   - Install with: `uv pip install -e .`

3. **Windsurf Cascade Configuration**
   - Critical elements:
   ```json
   "youtube": {
            "command": "C:/Users/kevin/repos/kev-made-mcp/.venv/Scripts/python.exe",
            "args": [
                "C:/Users/kevin/repos/kev-made-mcp/mcp_servers/youtube/server.py"
            ],
            "cwd": "C:/Users/kevin/repos/kev-made-mcp",
            "env": {
                "PYTHONPATH": "C:/Users/kevin/repos/kev-made-mcp",
                "PYTHONUNBUFFERED": "1"
            }

### Common Pitfalls
- **Path Formatting Issues**
  - PowerShell requires:
    - Backslashes for local paths
    - Forward slashes in JSON configs
- **Dependency Conflicts**
  - youtube-transcript-api 0.6.3+ needed for YouTube changes
  - fastmcp 0.4.1+ required for MCP 1.2+ compatibility

### Validation Checklist
✅ Server starts without imports errors
✅ `transcripts://` resources respond
✅ Tools appear in Windsurf Cascade UI
✅ Logs show successful startup

## Running MCP Servers

### Development Mode
To run an MCP server in development mode with the inspector:
```powershell
mcp dev .\path\to\server.py
```

### Production Mode
To run an MCP server without the inspector:
```powershell
mcp run .\path\to\server.py
```

### Common Issues
1. **Port Conflicts**: The MCP inspector uses ports 5173 and 3000. If these ports are in use:
   ```powershell
   # Find processes using the ports
   Get-Process -Id (Get-NetTCPConnection -LocalPort 5173, 3000).OwningProcess
   
   # Stop the processes
   Stop-Process -Id <process_id>
   ```

2. **Import Errors**: When running the server directly, use absolute imports instead of relative imports to avoid "attempted relative import with no known parent package" errors:
   ```python
   # Good
   from mcp_servers.youtube.models import TranscriptRequest
   
   # Avoid
   from .models import TranscriptRequest
   ```

## Best Practices

1. **Development Workflow**
   - Always work in an activated virtual environment
   - Use UV for package management
   - Run tests before committing changes
   - Keep documentation updated

2. **Error Prevention**
   - Check virtual environment activation
   - Verify correct Python interpreter
   - Use explicit paths in Windows
   - Test both success and error cases

3. **Documentation**
   - Maintain README.md in each server directory
   - Document environment setup steps
   - Include example usage
   - Keep track of lessons learned

4. **Code Organization**
   - Follow modular structure
   - Use type hints
   - Implement proper error handling
   - Write comprehensive tests
