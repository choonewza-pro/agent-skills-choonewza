# Debugging and Testing MCP Servers

This reference guide covers debugging and testing strategies for Model Context Protocol (MCP) servers, including tooling, logging best practices, and LLM-assisted workflows.

## 1. MCP Inspector

The [MCP Inspector](https://github.com/modelcontextprotocol/inspector) is an interactive developer GUI for testing and debugging MCP servers. It provides a visual interface for inspecting resources, testing prompts, and executing tools before integrating with a client.

### Launching the Inspector
Run the Inspector directly using `npx` (no installation required):
```bash
rtk npx @modelcontextprotocol/inspector <command> [args...]
```

**Examples:**
- **NPM Package:**
  ```bash
  rtk npx -y @modelcontextprotocol/inspector npx <package-name> <args>
  ```
- **PyPi Package:**
  ```bash
  rtk npx @modelcontextprotocol/inspector uvx <package-name> <args>
  ```
- **Local TypeScript Server:**
  ```bash
  rtk npx @modelcontextprotocol/inspector node path/to/server/index.js [args...]
  ```
- **Local Python Server:**
  ```bash
  rtk npx @modelcontextprotocol/inspector uv --directory path/to/server run package-name [args...]
  ```

### Inspector Interface Tabs
- **Connection:** Select the transport and configure CLI arguments for local servers.
- **Resources:** List available resources, view metadata (MIME types), inspect content, and test subscriptions.
- **Prompts:** Browse available prompt templates, view arguments, test with custom inputs, and preview messages.
- **Tools:** List tool schemas, provide custom inputs, and view execution results.
- **Notifications:** View all logs recorded and notifications received from the server.

## 2. Claude Desktop Debugging

When testing integration within Claude Desktop, monitoring server status and logs is crucial.

### Log Locations
Claude Desktop automatically captures `stderr` output from connected local servers.
- **macOS:** `~/Library/Logs/Claude/mcp*.log`

### Monitoring Logs
Use `tail` to continuously monitor logs during execution:
```bash
rtk tail -n 20 -F ~/Library/Logs/Claude/mcp*.log
```

### Chrome DevTools
You can enable Chrome DevTools inside Claude Desktop by setting `"allowDevTools": true` in your `developer_settings.json` file.

## 3. Server-Side Logging

**CRITICAL RULE:** Servers using the local `stdio` transport **MUST NOT** log messages to `stdout` (standard out). Doing so will interfere with protocol operation. All console logging must be directed to `stderr`.

### Standard Error Logging

**TypeScript:**
```typescript
// Standard logging MUST use stderr
console.error("Server initialized successfully");
```

**Python:**
```python
import sys
# Standard logging MUST use stderr
print("Server initialized successfully", file=sys.stderr)
```

### Client Notification Logs
For all transports, you can send structured log message notifications directly to the client:

**TypeScript:**
```typescript
server.sendLoggingMessage({
  level: "info",
  data: "Server started successfully",
});
```

**Python:**
```python
server.request_context.session.send_log_message(
  level="info",
  data="Server started successfully",
)
```

*Important events to log:* Initialization steps, resource access, tool execution, error conditions, and performance metrics.

## 4. Common Issues & Fixes

- **Configuration Errors:** Verify correct command paths, arguments, and escaping (especially on Windows) in your client configurations.
- **Server Not Starting:** Check the `stderr` output (using Claude Desktop logs or Inspector) for startup exceptions or missing dependencies.
- **Tools/Resources Not Appearing:** Ensure your server capabilities are correctly declared during initialization (e.g., `capabilities: { tools: {} }`).
- **Connection Drops:** Verify transport health and ensure you are not writing non-JSON-RPC text to `stdout`.

## 5. Testing Workflow

A standard development and testing cycle for an MCP server consists of:

1. **Initial Development:** Use the **MCP Inspector** for basic testing to verify connectivity, capability negotiation, and specific tools or resources.
2. **Integration Testing:** Test within Claude Desktop (or your target client). Monitor logs to ensure smooth integration.
3. **Check Edge Cases:** Validate handling of invalid inputs, missing arguments, and concurrent operations.
4. **Verify Error Responses:** Ensure the server returns proper error messages rather than crashing.
5. **Transport Testing:** Test different transports if applicable (e.g., stdio vs SSE).
6. **Load Testing:** Observe resource usage, message sizes, and latency under load.

## 6. Building MCP Servers with LLM Assistance

You can significantly speed up development using Claude or other frontier LLMs.

### Setup Context
Provide the LLM with up-to-date documentation:
- Feed `https://modelcontextprotocol.io/llms-full.txt` (the complete MCP spec context).
- Include the TypeScript or Python SDK `README.md` files.

### Workflow & Iteration
- Clearly specify what resources, tools, and prompts the server should expose.
- Describe required external connections (e.g., PostgreSQL database, third-party APIs).
- Start with core functionality, then test thoroughly with the **MCP Inspector** before moving on.
- Break down complex servers into smaller components.
- Iterate incrementally, and have the LLM assist with testing edge cases and implementing structured logging.
