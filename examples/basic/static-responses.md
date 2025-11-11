# Static Mock Responses

Static responses return the same data every time a tool is called. This is the simplest type of mock response, perfect for testing with predictable data.

## When to Use Static Responses

- **Fixed test data**: When you need consistent, unchanging responses
- **Simple prototypes**: Quick API mocking without logic
- **Reference data**: Dictionary-like lookups with constant values
- **Health checks**: Status endpoints that always return OK

## Basic Static Response

### Creating a Static Tool

```bash
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "get_weather",
    "description": "Get current weather for a location",
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
        "humidity": 45,
        "wind_speed": 8
      }
    }
  }'
```

### Calling the Tool

**Note**: Replace `YOUR_MCP_SERVER_ID` with your unique server ID from the dashboard or API.

```bash
curl -X POST https://mcp.mcpmock.ai/YOUR_MCP_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "tools/call",
    "params": {
      "name": "get_weather",
      "arguments": {
        "location": "San Francisco"
      }
    }
  }'
```

**Note**: MCP endpoints don't require authentication.

### Response

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "{\"temperature\":72,\"condition\":\"sunny\",\"humidity\":45,\"wind_speed\":8}"
      }
    ]
  }
}
```

**Note**: The response is always the same regardless of the `location` parameter!

## More Examples

### User Profile Tool

```json
{
  "name": "get_user_profile",
  "description": "Get user profile information",
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
      "email": "john@example.com",
      "role": "developer",
      "created_at": "2024-01-15T10:30:00Z"
    }
  }
}
```

### Product Catalog Tool

```json
{
  "name": "get_products",
  "description": "List available products",
  "inputSchema": {
    "type": "object",
    "properties": {}
  },
  "mockResponse": {
    "type": "static",
    "data": {
      "products": [
        {
          "id": "prod_1",
          "name": "Widget Pro",
          "price": 49.99,
          "in_stock": true
        },
        {
          "id": "prod_2",
          "name": "Gadget Plus",
          "price": 79.99,
          "in_stock": false
        }
      ],
      "total": 2
    }
  }
}
```

### Health Check Tool

```json
{
  "name": "health_check",
  "description": "Check service health status",
  "inputSchema": {
    "type": "object",
    "properties": {}
  },
  "mockResponse": {
    "type": "static",
    "data": {
      "status": "healthy",
      "version": "1.0.0",
      "uptime": 86400,
      "checks": {
        "database": "ok",
        "cache": "ok",
        "external_api": "ok"
      }
    }
  }
}
```

## Complex Data Structures

Static responses can include nested objects and arrays:

```json
{
  "name": "get_organization",
  "description": "Get organization details",
  "inputSchema": {
    "type": "object",
    "properties": {
      "org_id": {"type": "string"}
    },
    "required": ["org_id"]
  },
  "mockResponse": {
    "type": "static",
    "data": {
      "id": "org_456",
      "name": "Acme Corporation",
      "settings": {
        "timezone": "America/New_York",
        "features": ["analytics", "api_access", "sso"]
      },
      "members": [
        {"id": "u1", "name": "Alice", "role": "admin"},
        {"id": "u2", "name": "Bob", "role": "member"}
      ],
      "metadata": {
        "created": "2023-06-01",
        "plan": "enterprise",
        "seats": 50
      }
    }
  }
}
```

## Best Practices

1. **Use realistic data**: Mock data should resemble production data structure
2. **Include edge cases**: Test with null values, empty arrays, optional fields
3. **Document expected format**: Clear description helps consumers understand response
4. **Version your mocks**: Update mock data when API contracts change

## Limitations

Static responses:
- ❌ Cannot use input parameters in the response
- ❌ Always return the same data
- ❌ Cannot implement conditional logic
- ❌ Cannot track state between calls

For dynamic responses, see:
- [Template Responses](template-responses.md) - Use input parameters
- [Sequence Responses](../advanced/sequence-responses.md) - Return different data each call
- [Function Responses](../advanced/function-responses.md) - Implement custom logic

## Next Steps

- [Template Responses](template-responses.md) - Make responses dynamic
- [Admin API](../advanced/admin-api.md) - Learn full CRUD operations
- [MCP Protocol](../../tutorials/building-mcp-clients.md) - Build clients that consume tools
