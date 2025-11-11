# Admin API - Full Tool Management

The Admin API provides complete CRUD (Create, Read, Update, Delete) operations for managing your mock tools. This is your control panel for all tool configuration.

## Base URL

```
https://api.mcpmock.ai
```

All requests require authentication via Bearer token in the `Authorization` header.

## Authentication

```bash
-H "Authorization: Bearer YOUR_TOKEN"
```

Get your token from the **Settings** tab after logging in at [mcpmock.ai](https://www.mcpmock.ai).

**Note**: Only API endpoints (api.mcpmock.ai/tools, api.mcpmock.ai/me/endpoint) require authentication. MCP protocol endpoints (mcp.mcpmock.ai/{server-id}/) are open and don't require a token.

## List All Tools

Get all tools created by your account.

### Request

```bash
curl -X GET https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "tools": [
    {
      "name": "get_weather",
      "description": "Get weather information",
      "inputSchema": {
        "type": "object",
        "properties": {
          "location": {"type": "string"}
        },
        "required": ["location"]
      },
      "mockResponse": {
        "type": "static",
        "data": {"temperature": 72, "condition": "sunny"}
      }
    },
    {
      "name": "calculator",
      "description": "Perform calculations",
      "inputSchema": {
        "type": "object",
        "properties": {
          "operation": {"type": "string"},
          "a": {"type": "number"},
          "b": {"type": "number"}
        }
      },
      "mockResponse": {
        "type": "function",
        "code": "function(input) { /* ... */ }"
      }
    }
  ]
}
```

## Get Single Tool

Retrieve details for a specific tool by name.

### Request

```bash
curl -X GET https://api.mcpmock.ai/tools/get_weather \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "name": "get_weather",
  "description": "Get weather information for a location",
  "inputSchema": {
    "type": "object",
    "properties": {
      "location": {
        "type": "string",
        "description": "City name"
      }
    },
    "required": ["location"]
  },
  "mockResponse": {
    "type": "static",
    "data": {
      "temperature": 72,
      "condition": "sunny",
      "humidity": 45
    }
  }
}
```

## Create Tool

Create a new mock tool.

### Request

```bash
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "send_email",
    "description": "Send an email message",
    "inputSchema": {
      "type": "object",
      "properties": {
        "to": {
          "type": "string",
          "description": "Recipient email address"
        },
        "subject": {
          "type": "string",
          "description": "Email subject"
        },
        "body": {
          "type": "string",
          "description": "Email body content"
        }
      },
      "required": ["to", "subject", "body"]
    },
    "mockResponse": {
      "type": "template",
      "template": "Email sent to {{input.to}} with subject: {{input.subject}}"
    }
  }'
```

### Response

```json
{
  "message": "Tool created successfully",
  "tool": {
    "name": "send_email",
    "description": "Send an email message",
    "inputSchema": { /* ... */ },
    "mockResponse": { /* ... */ }
  }
}
```

### Error Response (Tool Already Exists)

```json
{
  "error": "Tool with name 'send_email' already exists"
}
```

## Update Tool

Update an existing tool. Replaces the entire tool definition.

### Request

```bash
curl -X PUT https://api.mcpmock.ai/tools/get_weather \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "get_weather",
    "description": "Get detailed weather forecast",
    "inputSchema": {
      "type": "object",
      "properties": {
        "location": {"type": "string"},
        "days": {"type": "number", "default": 1}
      },
      "required": ["location"]
    },
    "mockResponse": {
      "type": "function",
      "code": "function(input) { return { location: input.location, forecast: Array(input.days || 1).fill({temp: 72, condition: '\''sunny'\''}) }; }"
    }
  }'
```

### Response

```json
{
  "message": "Tool updated successfully",
  "tool": {
    "name": "get_weather",
    "description": "Get detailed weather forecast",
    "inputSchema": { /* ... */ },
    "mockResponse": { /* ... */ }
  }
}
```

## Delete Tool

Permanently delete a tool.

### Request

```bash
curl -X DELETE https://api.mcpmock.ai/tools/send_email \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "message": "Tool deleted successfully",
  "toolName": "send_email"
}
```

### Error Response (Tool Not Found)

```json
{
  "error": "Tool 'send_email' not found"
}
```

## Complete CRUD Workflow Example

```bash
# 1. Create a tool
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "user_profile",
    "description": "Get user profile",
    "inputSchema": {
      "type": "object",
      "properties": {
        "user_id": {"type": "string"}
      },
      "required": ["user_id"]
    },
    "mockResponse": {
      "type": "static",
      "data": {
        "id": "user_123",
        "name": "John Doe",
        "email": "john@example.com"
      }
    }
  }'

# 2. List all tools
curl -X GET https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer $TOKEN"

# 3. Get specific tool
curl -X GET https://api.mcpmock.ai/tools/user_profile \
  -H "Authorization: Bearer $TOKEN"

# 4. Update the tool
curl -X PUT https://api.mcpmock.ai/tools/user_profile \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "user_profile",
    "description": "Get user profile with preferences",
    "inputSchema": {
      "type": "object",
      "properties": {
        "user_id": {"type": "string"}
      }
    },
    "mockResponse": {
      "type": "static",
      "data": {
        "id": "user_123",
        "name": "John Doe",
        "email": "john@example.com",
        "preferences": {
          "theme": "dark",
          "notifications": true
        }
      }
    }
  }'

# 5. Delete the tool
curl -X DELETE https://api.mcpmock.ai/tools/user_profile \
  -H "Authorization: Bearer $TOKEN"
```

## Tool Naming Rules

- **Allowed characters**: Letters, numbers, underscores, hyphens
- **Pattern**: `[a-zA-Z0-9_-]+`
- **Examples**:
  - ✅ `get_weather`
  - ✅ `send-email`
  - ✅ `calculate2`
  - ❌ `get weather` (space)
  - ❌ `send@email` (special char)

## Input Schema Format

Input schemas follow JSON Schema specification:

```json
{
  "type": "object",
  "properties": {
    "field_name": {
      "type": "string|number|boolean|object|array",
      "description": "Field description",
      "enum": ["option1", "option2"],
      "default": "default_value"
    }
  },
  "required": ["field1", "field2"]
}
```

## Mock Response Types

### Static

```json
{
  "type": "static",
  "data": {
    "key": "value"
  }
}
```

### Template

```json
{
  "type": "template",
  "template": "Hello {{input.name}}, your value is {{input.value}}"
}
```

### Sequence

```json
{
  "type": "sequence",
  "responses": [
    {"data": {"status": "pending"}},
    {"data": {"status": "complete"}}
  ]
}
```

### Function

```json
{
  "type": "function",
  "code": "function(input) { return {result: input.a + input.b}; }"
}
```

## Error Responses

All error responses follow this format:

```json
{
  "error": "Description of what went wrong",
  "details": "Optional additional information"
}
```

Common HTTP status codes:
- `200` - Success
- `400` - Bad Request (invalid input)
- `401` - Unauthorized (missing/invalid token)
- `404` - Not Found (tool doesn't exist)
- `409` - Conflict (tool name already exists)
- `500` - Internal Server Error

## Rate Limiting

Currently there are no rate limits on the Admin API. Fair use is appreciated.

## Best Practices

1. **Test before creating**: Validate your tool definition locally
2. **Use descriptive names**: Clear names help identify tools
3. **Document thoroughly**: Use detailed descriptions and schema documentation
4. **Version control**: Keep tool definitions in git
5. **Backup important tools**: Export tool JSON before major changes
6. **Validate schemas**: Ensure input schemas are valid JSON Schema

## Automation Example

Create multiple tools programmatically:

```javascript
const tools = [
  {
    name: 'get_user',
    description: 'Get user by ID',
    inputSchema: {
      type: 'object',
      properties: { user_id: { type: 'string' } },
      required: ['user_id']
    },
    mockResponse: {
      type: 'static',
      data: { id: 'user_123', name: 'Test User' }
    }
  },
  {
    name: 'create_user',
    description: 'Create new user',
    inputSchema: {
      type: 'object',
      properties: {
        name: { type: 'string' },
        email: { type: 'string' }
      },
      required: ['name', 'email']
    },
    mockResponse: {
      type: 'template',
      template: 'User {{input.name}} created with email {{input.email}}'
    }
  }
];

const token = 'YOUR_TOKEN_FROM_SETTINGS'; // Get from Settings tab

for (const tool of tools) {
  await fetch('https://api.mcpmock.ai/tools', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(tool)
  });
}
```

## Next Steps

- [MCP Protocol Usage](../../tutorials/building-mcp-clients.md) - Use your tools via MCP
- [Function Responses](function-responses.md) - Create complex function tools
- [Error Handling](error-handling.md) - Handle errors gracefully
