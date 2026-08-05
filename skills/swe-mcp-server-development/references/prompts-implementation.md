# MCP Prompts Implementation Reference

Prompts enable servers to define reusable prompt templates and workflows that clients can easily surface to users and LLMs. They provide a powerful way to standardize and share common LLM interactions.

> **Note:** Prompts are designed to be **user-controlled**, exposed from servers to clients with the intention of the user explicitly selecting them for use.

## Prompt Structure

Each prompt is defined with a name, description, and an optional list of arguments:

```typescript
{
  name: string;              // Unique identifier for the prompt
  description?: string;      // Human-readable description
  arguments?: [              // Optional list of arguments
    {
      name: string;          // Argument identifier
      description?: string;  // Argument description
      required?: boolean;    // Whether argument is required
    }
  ]
}
```

## Discovering and Using Prompts

### Discovering Prompts
Clients can discover available prompts through the `prompts/list` endpoint.

```typescript
// Request
{
  method: "prompts/list"
}

// Response
{
  prompts: [
    {
      name: "analyze-code",
      description: "Analyze code for potential improvements",
      arguments: [{ name: "language", description: "Programming language", required: true }]
    }
  ]
}
```

### Using Prompts
To use a prompt, clients make a `prompts/get` request:

```typescript
// Request
{
  method: "prompts/get",
  params: {
    name: "analyze-code",
    arguments: { language: "python" }
  }
}
```

## Dynamic Prompts

Prompts can be dynamic and include context from resources.

### Embedded Resource Context Example

```json
{
  "messages": [
    {
      "role": "user",
      "content": {
        "type": "text",
        "text": "Analyze these system logs and the code file for any issues:"
      }
    },
    {
      "role": "user",
      "content": {
        "type": "resource",
        "resource": {
          "uri": "logs://recent?timeframe=1h",
          "text": "[2024-03-14 15:32:11] ERROR: Connection timeout in network.py:127\n...",
          "mimeType": "text/plain"
        }
      }
    }
  ]
}
```

### Multi-step Workflow Example

```typescript
const debugWorkflow = {
  name: "debug-error",
  async getMessages(error: string) {
    return [
      {
        role: "user",
        content: { type: "text", text: `Here's an error I'm seeing: ${error}` }
      },
      {
        role: "assistant",
        content: { type: "text", text: "I'll help analyze this error. What have you tried so far?" }
      }
    ];
  }
};
```

## Complete Implementations

### TypeScript

```typescript
import { Server } from "@modelcontextprotocol/sdk/server";
import {
  ListPromptsRequestSchema,
  GetPromptRequestSchema
} from "@modelcontextprotocol/sdk/types";

const PROMPTS = {
  "git-commit": {
    name: "git-commit",
    description: "Generate a Git commit message",
    arguments: [{ name: "changes", description: "Git diff or description of changes", required: true }]
  },
  "explain-code": {
    name: "explain-code",
    description: "Explain how code works",
    arguments: [
      { name: "code", description: "Code to explain", required: true },
      { name: "language", description: "Programming language", required: false }
    ]
  }
};

const server = new Server({
  name: "example-prompts-server",
  version: "1.0.0"
}, {
  capabilities: { prompts: {} }
});

server.setRequestHandler(ListPromptsRequestSchema, async () => {
  return { prompts: Object.values(PROMPTS) };
});

server.setRequestHandler(GetPromptRequestSchema, async (request) => {
  const prompt = PROMPTS[request.params.name];
  if (!prompt) {
    throw new Error(`Prompt not found: ${request.params.name}`);
  }

  if (request.params.name === "git-commit") {
    return {
      messages: [{
        role: "user",
        content: {
          type: "text",
          text: `Generate a concise but descriptive commit message for these changes:\n\n${request.params.arguments?.changes}`
        }
      }]
    };
  }

  if (request.params.name === "explain-code") {
    const language = request.params.arguments?.language || "Unknown";
    return {
      messages: [{
        role: "user",
        content: {
          type: "text",
          text: `Explain how this ${language} code works:\n\n${request.params.arguments?.code}`
        }
      }]
    };
  }

  throw new Error("Prompt implementation not found");
});
```

### Python

```python
from mcp.server import Server
import mcp.types as types

PROMPTS = {
    "git-commit": types.Prompt(
        name="git-commit",
        description="Generate a Git commit message",
        arguments=[types.PromptArgument(name="changes", description="Git diff or description of changes", required=True)],
    ),
    "explain-code": types.Prompt(
        name="explain-code",
        description="Explain how code works",
        arguments=[
            types.PromptArgument(name="code", description="Code to explain", required=True),
            types.PromptArgument(name="language", description="Programming language", required=False)
        ],
    )
}

app = Server("example-prompts-server")

@app.list_prompts()
async def list_prompts() -> list[types.Prompt]:
    return list(PROMPTS.values())

@app.get_prompt()
async def get_prompt(
    name: str, arguments: dict[str, str] | None = None
) -> types.GetPromptResult:
    if name not in PROMPTS:
        raise ValueError(f"Prompt not found: {name}")

    if name == "git-commit":
        changes = arguments.get("changes") if arguments else ""
        return types.GetPromptResult(
            messages=[types.PromptMessage(
                role="user",
                content=types.TextContent(
                    type="text",
                    text=f"Generate a concise but descriptive commit message for these changes:\n\n{changes}"
                )
            )]
        )

    if name == "explain-code":
        code = arguments.get("code") if arguments else ""
        language = arguments.get("language", "Unknown") if arguments else "Unknown"
        return types.GetPromptResult(
            messages=[types.PromptMessage(
                role="user",
                content=types.TextContent(
                    type="text",
                    text=f"Explain how this {language} code works:\n\n{code}"
                )
            )]
        )

    raise ValueError("Prompt implementation not found")
```

## UI Integration Options

Prompts can be surfaced in client UIs as:
* Slash commands
* Quick actions
* Context menu items
* Command palette entries
* Guided workflows
* Interactive forms

## Updates and Changes

Servers can notify clients about prompt changes. If the server supports the `prompts.listChanged` capability, it can emit `notifications/prompts/list_changed` to prompt the client to re-fetch the list.

## Best Practices and Security

**Best Practices:**
1. Use clear, descriptive prompt names and detailed descriptions.
2. Validate all required arguments and handle missing arguments gracefully.
3. Document expected argument formats and test prompts with various inputs.

**Security:**
* Sanitize user input and validate all arguments.
* Consider prompt injection risks and validate generated content.
* Implement timeouts and rate limiting where appropriate.
