# MCP Resources Implementation Guide

Resources are a core primitive in the Model Context Protocol (MCP) that allow servers to expose data and content that can be read by clients and used as context for LLM interactions.

> **Note**: Resources are designed to be **application-controlled**, meaning that the client application can decide how and when they should be used. In contrast, to expose data to models automatically, server authors should use a **model-controlled** primitive such as Tools.

## Resource URIs

Resources are identified using URIs that follow this format:

```
[protocol]://[host]/[path]
```

Examples:
* `file:///home/user/documents/report.pdf`
* `postgres://database/customers/schema`
* `screen://localhost/display1`

## Resource Types

Resources can contain two types of content:

### Text Resources
Contain UTF-8 encoded text data. Suitable for:
* Source code
* Configuration files
* Log files
* JSON/XML data
* Plain text

### Binary Resources
Contain raw binary data encoded in base64. Suitable for:
* Images
* PDFs
* Audio/Video files
* Other non-text formats

## Resource Discovery

Clients can discover available resources through two main methods:

### Direct Resources
Servers expose a list of concrete resources via the `resources/list` endpoint. Each resource includes:

```typescript
{
  uri: string;           // Unique identifier for the resource
  name: string;          // Human-readable name
  description?: string;  // Optional description
  mimeType?: string;     // Optional MIME type
}
```

### Resource Templates
For dynamic resources, servers can expose URI templates (following RFC 6570) that clients can use to construct valid resource URIs:

```typescript
{
  uriTemplate: string;   // URI template following RFC 6570
  name: string;          // Human-readable name for this type
  description?: string;  // Optional description
  mimeType?: string;     // Optional MIME type for all matching resources
}
```

## Reading Resources

To read a resource, clients make a `resources/read` request with the resource URI.
The server responds with a list of resource contents (can return multiple resources, e.g., files in a directory):

```typescript
{
  contents: [
    {
      uri: string;        // The URI of the resource
      mimeType?: string;  // Optional MIME type

      // One of:
      text?: string;      // For text resources
      blob?: string;      // For binary resources (base64 encoded)
    }
  ]
}
```

## Resource Updates

MCP supports real-time updates for resources through two mechanisms:

1. **List changes**: Servers can notify clients when their list of available resources changes via the `notifications/resources/list_changed` notification.
2. **Content changes**: Clients can subscribe to updates for specific resources:
   * Client sends `resources/subscribe` with resource URI
   * Server sends `notifications/resources/updated` when the resource changes
   * Client can fetch latest content with `resources/read`
   * Client can unsubscribe with `resources/unsubscribe`

## Implementation Examples

### TypeScript Implementation

```typescript
const server = new Server({
  name: "example-server",
  version: "1.0.0"
}, {
  capabilities: {
    resources: {}
  }
});

// List available resources
server.setRequestHandler(ListResourcesRequestSchema, async () => {
  return {
    resources: [
      {
        uri: "file:///logs/app.log",
        name: "Application Logs",
        mimeType: "text/plain"
      }
    ]
  };
});

// Read resource contents
server.setRequestHandler(ReadResourceRequestSchema, async (request) => {
  const uri = request.params.uri;

  if (uri === "file:///logs/app.log") {
    const logContents = await readLogFile();
    return {
      contents: [
        {
          uri,
          mimeType: "text/plain",
          text: logContents
        }
      ]
    };
  }

  throw new Error("Resource not found");
});
```

### Python Implementation

```python
app = Server("example-server")

@app.list_resources()
async def list_resources() -> list[types.Resource]:
    return [
        types.Resource(
            uri="file:///logs/app.log",
            name="Application Logs",
            mimeType="text/plain"
        )
    ]

@app.read_resource()
async def read_resource(uri: AnyUrl) -> str:
    if str(uri) == "file:///logs/app.log":
        log_contents = await read_log_file()
        return log_contents

    raise ValueError("Resource not found")

# Start server
async with stdio_server() as streams:
    await app.run(
        streams[0],
        streams[1],
        app.create_initialization_options()
    )
```

## Best Practices

1. Use clear, descriptive resource names and URIs
2. Include helpful descriptions to guide LLM understanding
3. Set appropriate MIME types when known
4. Implement resource templates for dynamic content
5. Use subscriptions for frequently changing resources
6. Handle errors gracefully with clear error messages
7. Consider pagination for large resource lists
8. Cache resource contents when appropriate
9. Validate URIs before processing
10. Document your custom URI schemes

## Security Considerations

* Validate all resource URIs
* Implement appropriate access controls
* Sanitize file paths to prevent directory traversal
* Be cautious with binary data handling
* Consider rate limiting for resource reads
* Audit resource access
* Encrypt sensitive data in transit
* Validate MIME types
* Implement timeouts for long-running reads
* Handle resource cleanup appropriately
