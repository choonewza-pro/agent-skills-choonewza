# MCP Tools Implementation Reference

Tools are a powerful primitive in the Model Context Protocol (MCP) that enable servers to expose executable functionality to clients. They allow LLMs to interact with external systems, perform computations, and take actions in the real world. Tools are designed to be **model-controlled**, meaning they are exposed with the intention of the AI model automatically invoking them (often with human-in-the-loop approval).

## Tool Structure Definition

Each tool is defined with the following structure:

```typescript
{
  name: string;          // Unique identifier for the tool
  description?: string;  // Human-readable description
  inputSchema: {         // JSON Schema for the tool's parameters
    type: "object",
    properties: { ... }  // Tool-specific parameters
  }
}
```

*Note: While not shown in basic examples, tools can include annotations like `readOnlyHint` or `destructiveHint` to help clients better understand the safety implications of invoking them.*

## Discovery and Invocation

1. **Discovery**: Clients list available tools using the `tools/list` endpoint.
2. **Invocation**: Clients call tools using the `tools/call` endpoint, providing the requested `arguments`.
3. **Dynamic Updates**: Servers can notify clients when tools change using the `notifications/tools/list_changed` notification, allowing tools to be added, removed, or updated dynamically during runtime.

## Tool Response Format

A tool response typically returns an array of content items (which can be `text`, `image`, or `resource` types).

```typescript
{
  content: [
    {
      type: "text",
      text: "Result string"
    }
  ],
  isError: false // Set to true if an error occurred during execution
}
```

## 🚨 CRITICAL: Error Handling Pattern

Tool errors **must** be reported within the result object, **not** as MCP protocol-level (JSON-RPC) errors. This allows the LLM to see the error, understand what went wrong, and potentially take corrective action or request human intervention.

When a tool encounters an error during execution:
1. Set `isError: true` in the result object.
2. Include the error details in the `content` array (usually as text).

## Complete TypeScript Example

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { ListToolsRequestSchema, CallToolRequestSchema } from "@modelcontextprotocol/sdk/types.js";

const server = new Server({
  name: "example-server",
  version: "1.0.0"
}, {
  capabilities: {
    tools: {}
  }
});

// 1. Discovering tools (tools/list)
server.setRequestHandler(ListToolsRequestSchema, async () => {
  return {
    tools: [{
      name: "calculate_sum",
      description: "Add two numbers together",
      inputSchema: {
        type: "object",
        properties: {
          a: { type: "number" },
          b: { type: "number" }
        },
        required: ["a", "b"]
      }
    }]
  };
});

// 2. Invoking tools (tools/call)
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  if (request.params.name === "calculate_sum") {
    try {
      const { a, b } = request.params.arguments as any;
      const result = a + b;
      
      return {
        content: [
          {
            type: "text",
            text: String(result)
          }
        ]
      };
    } catch (error: any) {
      // Return error inside result (isError: true)
      return {
        isError: true,
        content: [
          {
            type: "text",
            text: `Error: ${error.message}`
          }
        ]
      };
    }
  }
  
  throw new Error("Tool not found");
});
```

## Complete Python Example

```python
from mcp.server import Server
import mcp.types as types

app = Server("example-server")

# 1. Discovering tools (tools/list)
@app.list_tools()
async def list_tools() -> list[types.Tool]:
    return [
        types.Tool(
            name="calculate_sum",
            description="Add two numbers together",
            inputSchema={
                "type": "object",
                "properties": {
                    "a": {"type": "number"},
                    "b": {"type": "number"}
                },
                "required": ["a", "b"]
            }
        )
    ]

# 2. Invoking tools (tools/call)
@app.call_tool()
async def call_tool(
    name: str,
    arguments: dict
) -> list[types.TextContent | types.ImageContent | types.EmbeddedResource]:
    if name == "calculate_sum":
        try:
            a = arguments["a"]
            b = arguments["b"]
            result = a + b
            
            return [types.TextContent(type="text", text=str(result))]
        except Exception as error:
            # Return error inside result (isError=True)
            return types.CallToolResult(
                isError=True,
                content=[
                    types.TextContent(
                        type="text",
                        text=f"Error: {str(error)}"
                    )
                ]
            )
            
    raise ValueError(f"Tool not found: {name}")
```

## Best Practices

1. **Descriptive Names**: Provide clear, descriptive names and descriptions.
2. **Detailed Schemas**: Use detailed JSON Schema definitions for parameters.
3. **Usage Examples**: Include examples in tool descriptions to demonstrate how the model should use them.
4. **Error Handling**: Implement proper error handling by returning `isError: true` inside the result.
5. **Progress Reporting**: Use progress reporting for long operations.
6. **Focus**: Keep tool operations focused and atomic.
7. **Documentation**: Document expected return value structures.
8. **Timeouts**: Implement proper timeouts.
9. **Rate Limiting**: Consider rate limiting for resource-intensive operations.
10. **Logging**: Log tool usage for debugging and monitoring (but avoid leaking sensitive data to clients).
