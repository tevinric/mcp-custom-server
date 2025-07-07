# CREATE A CUSTOM MCP SERVER

## 1. Set up the project folder:
- uv init # Initialise the project folder
- uv venv # Create the virtual environment
- source .venv/Scripts/activate #Activate the environment

## 2.  Install dependencies
- uv add mcp[cli] # Installs packages and updated the pyproject.toml file file the required package

## 3. Create the server file

- touch server.py
- rm -r main.py @ Deleting the default created main.py file which is no longer required

## 4. Vibe coding setup in Cursor

- Now we will connect context websites to cursor so that cursor will have entire sites that it will use when vibe coding.
- This will allow Cursor to crawl the provide sites and index them so that cursor uses the actual technical documentation when generation code for you. 


- Go to CUrsor Settings:
- Go to Indexing and Docs
- Click to add doc
- Provide the following sites and register the index:
    - https://modelcontextprotocol.io
    - https://github.com/modelcontextprotocol/python-sdk

## 5. Configure Cursor with Coding persona to assist us

-  Give cursor the persona of someone that is excellent at Python - Configure the Cursor config file
- In project director, create a ".cursor" folder
- Create sub directory called "rules". The rules folder will contain text that will be attached to every request that we sent to cursor (Think system prompt). \
- Create a file in the rules folder called "python.mdc"

- Go to a site called https://cursor.directory/. 
- This is a great site to get cursor personas (prompts). 
- Click on "Rules"
- In sidebar click "Python"
- Choose from the multiple system prompts available to select a python persona for cursor

```
  You are an expert in Python, FastAPI, and scalable API development.
  
  Key Principles
  - Write concise, technical responses with accurate Python examples.
  - Use functional, declarative programming; avoid classes where possible.
  - Prefer iteration and modularization over code duplication.
  - Use descriptive variable names with auxiliary verbs (e.g., is_active, has_permission).
  - Use lowercase with underscores for directories and files (e.g., routers/user_routes.py).
  - Favor named exports for routes and utility functions.
  - Use the Receive an Object, Return an Object (RORO) pattern.
  
  Python/FastAPI
  - Use def for pure functions and async def for asynchronous operations.
  - Use type hints for all function signatures. Prefer Pydantic models over raw dictionaries for input validation.
  - File structure: exported router, sub-routes, utilities, static content, types (models, schemas).
  - Avoid unnecessary curly braces in conditional statements.
  - For single-line statements in conditionals, omit curly braces.
  - Use concise, one-line syntax for simple conditional statements (e.g., if condition: do_something()).
  
  Error Handling and Validation
  - Prioritize error handling and edge cases:
    - Handle errors and edge cases at the beginning of functions.
    - Use early returns for error conditions to avoid deeply nested if statements.
    - Place the happy path last in the function for improved readability.
    - Avoid unnecessary else statements; use the if-return pattern instead.
    - Use guard clauses to handle preconditions and invalid states early.
    - Implement proper error logging and user-friendly error messages.
    - Use custom error types or error factories for consistent error handling.
  
  Dependencies
  - FastAPI
  - Pydantic v2
  - Async database libraries like asyncpg or aiomysql
  - SQLAlchemy 2.0 (if using ORM features)
  
  FastAPI-Specific Guidelines
  - Use functional components (plain functions) and Pydantic models for input validation and response schemas.
  - Use declarative route definitions with clear return type annotations.
  - Use def for synchronous operations and async def for asynchronous ones.
  - Minimize @app.on_event("startup") and @app.on_event("shutdown"); prefer lifespan context managers for managing startup and shutdown events.
  - Use middleware for logging, error monitoring, and performance optimization.
  - Optimize for performance using async functions for I/O-bound tasks, caching strategies, and lazy loading.
  - Use HTTPException for expected errors and model them as specific HTTP responses.
  - Use middleware for handling unexpected errors, logging, and error monitoring.
  - Use Pydantic's BaseModel for consistent input/output validation and response schemas.
  
  Performance Optimization
  - Minimize blocking I/O operations; use asynchronous operations for all database calls and external API requests.
  - Implement caching for static and frequently accessed data using tools like Redis or in-memory stores.
  - Optimize data serialization and deserialization with Pydantic.
  - Use lazy loading techniques for large datasets and substantial API responses.
  
  Key Conventions
  1. Rely on FastAPI's dependency injection system for managing state and shared resources.
  2. Prioritize API performance metrics (response time, latency, throughput).
  3. Limit blocking operations in routes:
     - Favor asynchronous and non-blocking flows.
     - Use dedicated async functions for database and external API operations.
     - Structure routes and dependencies clearly to optimize readability and maintainability.
  
  Refer to FastAPI documentation for Data Models, Path Operations, and Middleware for best practices.
```

- Paste the above persona in the python.mdc file

- We want the python,mdc file to always be used when a requets is made to cursor. 
- For this we click on the python.mdc file and select rule type = "always"


## 6. Now we will use our Cursor Agent that we created to vibe code the MCP server creation. 

- Use the following prompt in cursor chat: 
```
I want you to implement me a simple MCP server from @MCP. Use the @MCP Python SDK and the server should expose one tool which is called terminal tool which will allow user to run terminal commands, make it simple. 
``` 

This wil add the code to the server.py file as well as create the README.md and EXPLAINED.MD files 


## 7.  Run the server

```
uv run server.py
```

## 8.  Configure the MCP in a HOST application (CLAUDE DESKTOP)

- Go to Claude Desktop
- Go to settings to add a new MCP server
- Open the config json
- Add the new MCP server config details: 

```json
{
  "mcpServers": {
    "server1": {
      "command" : '',
      "args": [
        'args
      ]
    },
    "shell": {  # Name of the new server that we are adding
    "command": "/Users/tevin/AppData/Local/Microsoft/WinGet/Packages/astral-sh.uv_Microsoft.Winget.Source_8wekyb3d8bbwe/uv",
    "args": [
      "path to the project folder", "run", "server.py"
    ]
  }
}
}
````


- For this example the following mcp server config is used to connect to a MCP host:

{
  "mcpServers": {
    "shell": {
      "command": "/Users/tevin/AppData/Local/Microsoft/WinGet/Packages/astral-sh.uv_Microsoft.Winget.Source_8wekyb3d8bbwe/uv",
      "args": ["--directory", "/Github/mcp-custom-server", "run", "server.py"]
    }
  }
}


- This is a great mcp server that will allow you to communicate with the server shell - It can be dangerous though
