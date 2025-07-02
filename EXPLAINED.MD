# MCP Terminal Server

This document explains the MCP Terminal Server implementation and how to use it.

## What is MCP?

Model Context Protocol (MCP) is a protocol designed to let you build servers that expose data and functionality to LLM applications (like Claude) in a secure, standardized way. It's similar to a web API but specifically designed for LLM interactions.

## About This Server

The Terminal Server is a simple MCP server that exposes a single tool:

- **execute_command**: A tool that allows running terminal commands and returns the output

This server demonstrates how to create a basic MCP tool that integrates with system functionality.

## Server Implementation

The server is implemented in `server.py` and consists of:

1. A FastMCP server instance
2. A single tool for executing terminal commands
3. Security measures including command timeout and error handling

### Code Walkthrough

```python
from mcp.server.fastmcp import FastMCP
import subprocess
import shlex

# Create an MCP server
mcp = FastMCP("Terminal-Server")


@mcp.tool()
def execute_command(command: str) -> str:
    """
    Execute a terminal command and return the output.
    
    Args:
        command: The command to execute in the terminal
        
    Returns:
        The output of the command execution
    """
    try:
        # Use shlex to properly handle command arguments
        args = shlex.split(command)
        # Execute the command and capture output
        result = subprocess.run(
            args,
            capture_output=True,
            text=True,
            timeout=30  # Timeout after 30 seconds
        )
        
        output = ""
        if result.stdout:
            output += result.stdout
        if result.stderr:
            output += f"\nError: {result.stderr}"
            
        return output
    except subprocess.TimeoutExpired:
        return "Command execution timed out after 30 seconds"
    except Exception as e:
        return f"Error executing command: {str(e)}"
```

The code:
- Initializes a FastMCP server named "Terminal-Server"
- Defines the `execute_command` tool which:
  - Takes a command string as input
  - Uses `shlex.split()` to properly parse the command arguments
  - Runs the command with a 30-second timeout for safety
  - Captures and returns both stdout and stderr
  - Provides helpful error messages if execution fails

## Security Considerations

The terminal tool provides direct access to the system's command execution capabilities, which comes with security risks. Consider:

1. **Limiting commands**: In a production environment, you might want to restrict which commands can be executed
2. **User permissions**: The commands run with the same permissions as the server process
3. **Timeout**: A 30-second timeout prevents long-running commands from blocking the server
4. **Error handling**: All exceptions are caught to prevent server crashes

## Installation and Setup

### Prerequisites

- Python 3.10 or higher
- The MCP Python SDK (version 1.2.0 or higher)

### Installation Steps

1. Clone this repository or download the server files
2. Install dependencies using uv:

```bash
uv add -e .
```

Or using pip:

```bash
pip install -e .
```

## Running the Server

### Local Development

For development and testing, use the MCP development mode:

```bash
mcp dev server.py
```

This will start the server in development mode with an inspector interface.

### Installing to Claude Desktop

To use the server with Claude Desktop:

```bash
mcp install server.py
```

This will register the server with Claude Desktop, making the `execute_command` tool available to use.

## Using the Terminal Tool with Claude

Once the server is installed, you can use it with Claude by:

1. Asking Claude to run a terminal command
2. Claude will recognize the need for the terminal tool
3. Claude will ask for your permission before executing any command
4. After approval, the command will run and the output will be returned to Claude

Example conversation:

```
You: Can you show me the contents of the current directory?

Claude: I can help you with that. I'll use the terminal tool to list the contents of the current directory.

[Claude requests to use the execute_command tool with: "ls -la"]

[After your approval]

Claude: Here are the contents of the current directory:
[Output of the ls -la command]
```

## Limitations

- Commands timeout after 30 seconds
- Commands run with the same permissions as the server process
- No interactive command support (no stdin input during command execution)
- Limited to one command per tool call 