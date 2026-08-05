# MCP Architecture Overview

The Model Context Protocol (MCP) connects clients, servers, and LLMs using a flexible, extensible architecture.

## Roles

MCP follows a client-server architecture where:
* **Hosts** are LLM applications (like Claude Desktop or IDEs) that initiate connections.
* **Clients** maintain 1:1 connections with servers, inside the host application.
* **Servers** provide context, tools, and prompts to clients.

## Protocol Layer

The protocol layer handles message framing, request/response linking, and high-level communication patterns. Key components are `Protocol`, `Client`, and `Server`.

## Transport Layer

The transport layer handles the actual communication between clients and servers using JSON-RPC 2.0.

1. **Stdio transport**: Uses standard input/output for communication. Ideal for local processes.
2. **HTTP with SSE transport**: Uses Server-Sent Events for server-to-client messages and HTTP POST for client-to-server messages.

## Message Types

MCP uses four main types of messages:

1. **Requests**: Expect a response from the other side.
   ```typescript
   interface Request {
     method: string;
     params?: { ... };
   }
   ```

2. **Results**: Successful responses to requests.
   ```typescript
   interface Result {
     [key: string]: unknown;
   }
   ```

3. **Errors**: Indicate that a request failed.
   ```typescript
   interface Error {
     code: number;
     message: string;
     data?: unknown;
   }
   ```

4. **Notifications**: One-way messages that don't expect a response.
   ```typescript
   interface Notification {
     method: string;
     params?: { ... };
   }
   ```

## Connection Lifecycle

1. **Initialization**:
   - Client sends `initialize` request with protocol version and capabilities.
   - Server responds with its protocol version and capabilities.
   - Client sends `initialized` notification as acknowledgment.
   - Connection is ready for use.

2. **Message exchange**:
   - **Request-Response**: Client or server sends requests, the other responds.
   - **Notifications**: Either party sends one-way messages.

3. **Termination**: Clean shutdown via `close()`, transport disconnection, or error conditions.

## Error Codes

MCP defines standard JSON-RPC error codes:

```typescript
enum ErrorCode {
  // Standard JSON-RPC error codes
  ParseError = -32700,
  InvalidRequest = -32600,
  MethodNotFound = -32601,
  InvalidParams = -32602,
  InternalError = -32603
}
```

## Basic Server Implementation

### TypeScript

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server({
  name: "example-server",
  version: "1.0.0"
}, {
  capabilities: {
    resources: {}
  }
});

// Handle requests
server.setRequestHandler(ListResourcesRequestSchema, async () => {
  return {
    resources: [
      {
        uri: "example://resource",
        name: "Example Resource"
      }
    ]
  };
});

// Connect transport
const transport = new StdioServerTransport();
await server.connect(transport);
```

### Python

```python
import asyncio
import mcp.types as types
from mcp.server import Server
from mcp.server.stdio import stdio_server

app = Server("example-server")

@app.list_resources()
async def list_resources() -> list[types.Resource]:
    return [
        types.Resource(
            uri="example://resource",
            name="Example Resource"
        )
    ]

async def main():
    async with stdio_server() as streams:
        await app.run(
            streams[0],
            streams[1],
            app.create_initialization_options()
        )

if __name__ == "__main__":
    asyncio.run(main())
```

## Best Practices

### Transport Selection
* **Local communication**: Use stdio transport for local processes. Efficient for same-machine communication and simple process management.
* **Remote communication**: Use SSE for scenarios requiring HTTP compatibility. Consider security implications like authentication and authorization.

### Message Handling
* **Request processing**: Validate inputs thoroughly, use type-safe schemas, handle errors gracefully, and implement timeouts.
* **Progress reporting**: Use progress tokens for long operations, report progress incrementally, and include total progress when known.
* **Error management**: Use appropriate error codes, include helpful error messages, and clean up resources on errors.
