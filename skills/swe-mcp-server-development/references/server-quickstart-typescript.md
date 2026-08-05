# TypeScript MCP Server Quickstart

## 1. Prerequisites
- **Node.js**: Version 16 or higher
- **npm / npx**: For package management and running tools
- **@modelcontextprotocol/sdk**: The core MCP SDK
- **zod**: Used for tool schema definitions

## 2. Project Setup

Initialize the project and install dependencies:
```bash
rtk npm init -y
rtk npm install @modelcontextprotocol/sdk zod
rtk npm install -D @types/node typescript
```

Update `package.json` to use ES modules and add a build script:
```json
{
  "type": "module",
  "bin": {
    "weather": "./build/index.js"
  },
  "scripts": {
    "build": "tsc && chmod 755 build/index.js"
  },
  "files": [
    "build"
  ]
}
```

Create `tsconfig.json` for compilation:
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "Node16",
    "moduleResolution": "Node16",
    "outDir": "./build",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

## 3. McpServer Setup

The `McpServer` class provides a high-level API to define the server.

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";

const server = new McpServer({
  name: "weather",
  version: "1.0.0",
  capabilities: {
    resources: {},
    tools: {},
  },
});
```

## 4. Registering Tools

Use `server.tool()` to define a tool's name, description, zod schema for arguments, and its handler function.

```typescript
server.tool(
  "get-alerts",
  "Get weather alerts for a state",
  {
    state: z.string().length(2).describe("Two-letter state code (e.g. CA, NY)"),
  },
  async ({ state }) => {
    // Handler implementation logic here
    const alertsText = `Active alerts for ${state}:\n\n...`;
    return {
      content: [
        {
          type: "text",
          text: alertsText,
        },
      ],
    };
  },
);
```

## 5. Registering Resources

Resources are file-like data that can be read by clients (e.g., API responses or file contents). Use `server.resource()` to register them.

## 6. Registering Prompts

Prompts are pre-written templates that help users accomplish specific tasks. Use `server.prompt()` to register them.

## 7. StdioServerTransport Setup

Configure the server to use stdio for client-server communication:

```typescript
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("Weather MCP Server running on stdio");
}

main().catch((error) => {
  console.error("Fatal error in main():", error);
  process.exit(1);
});
```

## 8. Complete Weather Server Example

Here is how the concepts map together for a weather server:

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "weather",
  version: "1.0.0",
  capabilities: {
    resources: {},
    tools: {},
  },
});

// Assume helper functions like makeNWSRequest<T> and formatAlert exist

server.tool(
  "get-alerts",
  "Get weather alerts for a state",
  {
    state: z.string().length(2).describe("Two-letter state code (e.g. CA, NY)"),
  },
  async ({ state }) => {
    // ... data fetching logic ...
    return {
      content: [
        { type: "text", text: `Active alerts for ${state}` },
      ],
    };
  }
);

server.tool(
  "get-forecast",
  "Get weather forecast for a location",
  {
    latitude: z.number().min(-90).max(90).describe("Latitude of the location"),
    longitude: z.number().min(-180).max(180).describe("Longitude of the location"),
  },
  async ({ latitude, longitude }) => {
    // ... data fetching logic ...
    return {
      content: [
        { type: "text", text: `Forecast for ${latitude}, ${longitude}` },
      ],
    };
  }
);

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("Weather MCP Server running on stdio");
}

main().catch((error) => {
  console.error("Fatal error in main():", error);
  process.exit(1);
});
```

## 9. Building and Running the Server

Make sure to build the project before execution. This is essential for getting the server to connect properly:
```bash
rtk npm run build
```

## 10. Claude Desktop Configuration

Configure Claude for Desktop by updating `claude_desktop_config.json`. Note the use of absolute paths.

```json
{
    "mcpServers": {
        "weather": {
            "command": "node",
            "args": [
                "/ABSOLUTE/PATH/TO/PARENT/FOLDER/weather/build/index.js"
            ]
        }
    }
}
```

## 11. Key Rules
- **Stdout Protocol Integrity:** Local MCP servers must NOT log messages to `stdout`. `stdout` is strictly for JSON-RPC communication.
- **Logging:** Always use `console.error()` for debugging and informational server logging.
