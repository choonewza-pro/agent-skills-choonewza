# Security and Best Practices for MCP Servers

When building Model Context Protocol (MCP) servers, it is critical to implement robust security measures and follow best practices. Servers often expose local filesystems, database connections, and executable tools to language models and client applications.

## 1. Transport Security

Transport mechanisms dictate how clients and servers exchange JSON-RPC messages. Securing these channels is the first line of defense.

* **Use TLS for remote connections**: When using SSE (Server-Sent Events) or custom HTTP transports over a network, always encrypt traffic using TLS (HTTPS).
* **Validate connection origins**: Verify the `Origin` or `Host` headers to ensure connections originate from trusted clients.
* **Authentication when needed**: Require API keys, bearer tokens, or client certificates before completing the initialization phase.

## 2. Input Validation

Data originating from the client or the LLM must be treated as untrusted.

* **Validate all incoming messages**: Ensure that message contents map to expected formats.
* **Sanitize inputs**: File paths, shell commands, and URLs must be aggressively sanitized.
* **Check message size limits**: Reject overly large payloads to prevent memory exhaustion and DoS.
* **Verify JSON-RPC format**: Ensure messages match the MCP JSON-RPC protocol specification.
* **Type-safe schemas**: Use strict schema validation.

```typescript
// Example: Using Zod for TypeScript input validation
import { z } from "zod";

const ToolInputSchema = z.object({
  filepath: z.string().min(1).regex(/^[^<>:"|?*]+$/),
  operation: z.enum(["read", "write"])
});
```

## 3. Tool Security

Tools allow LLMs to take actions. They are powerful but carry significant risk if not properly constrained.

* **Treat tool inputs as untrusted user data**: Always validate parameters against your JSON schema definitions.
* **Validate before execution**: Check for command injection vulnerabilities before passing data to a shell or external process.
* **Access controls**: Implement role-based or contextual authorization. Require user approval for destructive operations.
* **Rate limiting**: Prevent abuse by throttling how often tools can be called.
* **Audit tool usage**: Keep a log of what tools were executed, when, and with what arguments.
* **Implement timeouts**: Ensure tool executions do not hang the server indefinitely.

```python
# Example: Implementing tool timeouts and validation in Python
import asyncio

async def safe_tool_execution(command: str):
    if not command.isalnum():
        raise ValueError("Invalid characters in command")
        
    try:
        # Enforce a 5-second timeout on tool execution
        async with asyncio.timeout(5.0):
            return await run_subprocess(command)
    except asyncio.TimeoutError:
        raise RuntimeError("Tool execution timed out")
```

## 4. Resource Protection

Resources expose data to LLMs. You must restrict what data can be accessed.

* **Validate resource URIs**: Ensure the requested URI matches your server's expected patterns.
* **Sanitize file paths to prevent directory traversal**: Resolve paths absolutely and check that they fall within permitted root directories.
* **Access controls**: Ensure the client has permission to read the requested resource.
* **Rate limit reads**: Protect backend systems (like databases) from being overwhelmed by aggressive reading.
* **Encrypt sensitive data in transit**: Secure data delivery when reading confidential resources.

## 5. Error Handling Best Practices

Proper error handling prevents information leakage and improves server stability.

* **Don't leak sensitive information in errors**: Avoid returning stack traces, API keys, or internal infrastructure details in error responses to the client.
* **Log security-relevant errors**: Record failed validations, unauthorized access attempts, and authentication failures.
* **Proper cleanup on errors**: Ensure file descriptors and database connections are closed when operations fail.
* **Handle DoS scenarios**: Use connection limits and timeouts to survive traffic spikes.
* **Use appropriate error codes**: Return standard JSON-RPC error codes (e.g., `-32600` for Invalid Request) rather than crashing.

## 6. Logging Best Practices

Logging is crucial for debugging, auditing, and monitoring MCP servers.

* **stdio transport**: MUST log to `stderr` only. The `stdout` stream is reserved exclusively for MCP JSON-RPC protocol messages. Logging to `stdout` will break the protocol.
* **Use `server.sendLoggingMessage()`**: Send structured diagnostic logs directly to the client using MCP's built-in notification system.
* **Log protocol events**: Track connection lifecycle, message flow, and tool invocations for easier debugging.

```typescript
// Example: Sending a log message to the client
server.sendLoggingMessage({
  level: "info",
  data: "Tool execution completed successfully",
});
```

## 7. Environment & Secrets

Secrets management is fundamental to securing your server deployments.

* **Never hardcode API keys**: Do not store secrets, tokens, or passwords in source code.
* **Use `.env` files or environment variables**: Provide configuration and secrets securely at runtime.
* **Add `.env` to `.gitignore`**: Always ensure that local configuration files containing secrets are excluded from version control.
