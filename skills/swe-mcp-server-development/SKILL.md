---
name: swe-mcp-server-development
description: >
  Guides developers through building MCP (Model Context Protocol) servers that expose
  tools, resources, and prompts to LLM applications. Use when the user asks about
  creating an MCP server, implementing MCP tools/resources/prompts, choosing between
  stdio and SSE transports, debugging MCP connections, or integrating with Claude Desktop.
  Triggers on: "MCP server", "MCP tool", "MCP resource", "MCP prompt", "FastMCP",
  "McpServer", "model context protocol", "build an MCP", "create MCP server".
metadata:
  author: choonewza@gmail.com
  version: "1.0.0"
---

# MCP Server Development Guide

Build production-grade MCP servers that expose tools, resources, and prompts to LLM applications like Claude Desktop, Claude Code, Cursor, and other MCP-compatible clients.

**When to load supplementary files:**

- Understanding MCP architecture → see [architecture-overview.md](references/architecture-overview.md)
- Implementing tools (model-controlled functions) → see [tools-implementation.md](references/tools-implementation.md)
- Exposing resources (application-controlled data) → see [resources-implementation.md](references/resources-implementation.md)
- Creating prompt templates → see [prompts-implementation.md](references/prompts-implementation.md)
- TypeScript server quickstart → see [server-quickstart-typescript.md](references/server-quickstart-typescript.md)
- Python server quickstart → see [server-quickstart-python.md](references/server-quickstart-python.md)
- Security, validation, and best practices → see [security-and-best-practices.md](references/security-and-best-practices.md)
- Debugging and testing with MCP Inspector → see [debugging-and-testing.md](references/debugging-and-testing.md)

---

## Core Concepts Quick Reference

| Primitive     | Controlled By | Purpose                                     | Example                         |
| ------------- | ------------- | ------------------------------------------- | ------------------------------- |
| **Tools**     | Model (LLM)   | Executable functions the LLM can invoke     | `get-forecast`, `query-db`      |
| **Resources** | Application    | Read-only data exposed via URIs             | `file:///logs/app.log`          |
| **Prompts**   | User           | Reusable prompt templates / slash commands  | `analyze-code`, `git-commit`    |

**Transport options:**

| Transport        | Use Case                    | Pros                        |
| ---------------- | --------------------------- | --------------------------- |
| **stdio**        | Local CLI / desktop apps    | Simple, zero network config |
| **Streamable HTTP** | Remote / web deployments | HTTP-compatible, scalable   |

---

## When to Activate

Activate when the user:

- Wants to build an MCP server from scratch
- Needs to implement tools, resources, or prompts for an MCP server
- Asks about MCP architecture, transports, or protocol lifecycle
- Wants to integrate a server with Claude Desktop or other MCP clients
- Needs to debug MCP connections or test with MCP Inspector
- Asks about FastMCP (Python) or McpServer (TypeScript)

---

## Step-by-Step: Build an MCP Server

### Step 1: Choose SDK and Transport

Ask the user which language they prefer:

- **TypeScript** → `@modelcontextprotocol/sdk` with `zod` for schemas → [server-quickstart-typescript.md](references/server-quickstart-typescript.md)
- **Python** → `mcp[cli]` with `FastMCP` decorator API → [server-quickstart-python.md](references/server-quickstart-python.md)

Default transport: **stdio** (works with Claude Desktop, Claude Code, Cursor).

### Step 2: Define Capabilities

Decide which primitives the server exposes:

- **Tools** for actions the LLM should invoke → [tools-implementation.md](references/tools-implementation.md)
- **Resources** for data the application controls → [resources-implementation.md](references/resources-implementation.md)
- **Prompts** for reusable templates users select → [prompts-implementation.md](references/prompts-implementation.md)

### Step 3: Implement Handlers

Follow the per-primitive reference for handler patterns, input validation, and error handling. Key rules:

1. **Tool errors** → return inside result (`isError: true`), not as JSON-RPC protocol errors
2. **Resources** → validate all URIs, sanitize file paths
3. **Prompts** → validate required arguments, handle missing gracefully

### Step 4: Test with MCP Inspector

```bash
npx @modelcontextprotocol/inspector <command>
```

See [debugging-and-testing.md](references/debugging-and-testing.md) for full debugging workflow.

### Step 5: Integrate with Claude Desktop

Add server config to `claude_desktop_config.json`. Always use **absolute paths**.

### Step 6: Apply Security Best Practices

See [security-and-best-practices.md](references/security-and-best-practices.md) for validation, access control, rate limiting, and logging guidelines.

---

## Decision Guide

**Choosing between primitives:**

- LLM needs to **perform actions** or **fetch live data** → **Tools**
- App needs to **expose static/semi-static data** for context → **Resources**
- Users need **pre-built prompt workflows** → **Prompts**

**Choosing error handling strategy:**

- Error during tool execution → return `isError: true` in tool result content
- Invalid request parameters → throw protocol-level error (JSON-RPC)
- Server-side failure → log to stderr, return descriptive error to client

**stdio vs HTTP transport:**

- Local desktop integration (Claude Desktop, Cursor) → **stdio**
- Remote server / web deployment / multi-client → **Streamable HTTP**
- Need both? → implement both transports with shared handler logic
