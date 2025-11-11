# Template Mock Responses

Template responses use placeholders to create dynamic responses based on input parameters. This allows you to create personalized responses without writing JavaScript code.

## When to Use Template Responses

- **Personalized messages**: Include user names, IDs, or other input data
- **Dynamic greetings**: Generate contextual responses
- **Simple calculations**: Basic string interpolation and formatting
- **Input echoing**: Confirm received parameters in response

## Template Syntax

Templates use `{{variable}}` syntax to reference input parameters:

- `{{input.paramName}}` - Access input parameter
- `{{name}}` - Shorthand for common fields
- Plain text mixed with placeholders

## Basic Template Example

### Creating a Template Tool

```bash
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "greet_user",
    "description": "Generate a personalized greeting",
    "inputSchema": {
      "type": "object",
      "properties": {
        "name": {
          "type": "string",
          "description": "User name"
        },
        "time_of_day": {
          "type": "string",
          "description": "morning, afternoon, or evening"
        }
      },
      "required": ["name"]
    },
    "mockResponse": {
      "type": "template",
      "template": "Good {{input.time_of_day}}, {{input.name}}! Welcome to MCPMock."
    }
  }'
```

### Calling the Tool

**Note**: Replace `YOUR_MCP_SERVER_ID` with your unique server ID from the dashboard.

```bash
curl -X POST https://mcp.mcpmock.ai/YOUR_MCP_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "tools/call",
    "params": {
      "name": "greet_user",
      "arguments": {
        "name": "Alice",
        "time_of_day": "morning"
      }
    }
  }'
```

**Note**: MCP endpoints are open and don't require authentication.

### Response

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "Good morning, Alice! Welcome to MCPMock."
      }
    ]
  }
}
```

## More Examples

### Weather Report Template

```json
{
  "name": "weather_report",
  "description": "Get weather report with location",
  "inputSchema": {
    "type": "object",
    "properties": {
      "location": {"type": "string"},
      "units": {"type": "string", "enum": ["celsius", "fahrenheit"]}
    },
    "required": ["location"]
  },
  "mockResponse": {
    "type": "template",
    "template": "Weather in {{input.location}}: 72°F (22°C), sunny with light winds. Temperature shown in {{input.units}}."
  }
}
```

**Call it:**
```bash
curl -X POST https://mcp.mcpmock.ai/YOUR_MCP_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "tools/call",
    "params": {
      "name": "weather_report",
      "arguments": {
        "location": "New York",
        "units": "fahrenheit"
      }
    }
  }'
```

**Result:**
```
Weather in New York: 72°F (22°C), sunny with light winds. Temperature shown in fahrenheit.
```

### Order Confirmation Template

```json
{
  "name": "confirm_order",
  "description": "Generate order confirmation message",
  "inputSchema": {
    "type": "object",
    "properties": {
      "order_id": {"type": "string"},
      "customer_name": {"type": "string"},
      "item_count": {"type": "number"},
      "total": {"type": "number"}
    },
    "required": ["order_id", "customer_name", "item_count", "total"]
  },
  "mockResponse": {
    "type": "template",
    "template": "Order #{{input.order_id}} confirmed for {{input.customer_name}}. Items: {{input.item_count}}, Total: ${{input.total}}. Estimated delivery: 3-5 business days."
  }
}
```

### Task Assignment Template

```json
{
  "name": "assign_task",
  "description": "Create task assignment message",
  "inputSchema": {
    "type": "object",
    "properties": {
      "assignee": {"type": "string"},
      "task_title": {"type": "string"},
      "priority": {"type": "string"},
      "due_date": {"type": "string"}
    },
    "required": ["assignee", "task_title"]
  },
  "mockResponse": {
    "type": "template",
    "template": "Task '{{input.task_title}}' has been assigned to {{input.assignee}}. Priority: {{input.priority}}. Due: {{input.due_date}}."
  }
}
```

### System Notification Template

```json
{
  "name": "system_notification",
  "description": "Generate system notification",
  "inputSchema": {
    "type": "object",
    "properties": {
      "event_type": {"type": "string"},
      "resource": {"type": "string"},
      "user": {"type": "string"}
    },
    "required": ["event_type", "resource", "user"]
  },
  "mockResponse": {
    "type": "template",
    "template": "[{{input.event_type}}] User {{input.user}} performed action on resource: {{input.resource}}"
  }
}
```

## Accessing Nested Input

For nested input objects, use dot notation:

```json
{
  "name": "user_profile_message",
  "inputSchema": {
    "type": "object",
    "properties": {
      "user": {
        "type": "object",
        "properties": {
          "name": {"type": "string"},
          "email": {"type": "string"},
          "settings": {
            "type": "object",
            "properties": {
              "theme": {"type": "string"}
            }
          }
        }
      }
    }
  },
  "mockResponse": {
    "type": "template",
    "template": "Welcome {{input.user.name}} ({{input.user.email}}). Your theme is set to: {{input.user.settings.theme}}"
  }
}
```

## Combining Static and Dynamic Content

Templates can mix static text with dynamic placeholders:

```json
{
  "mockResponse": {
    "type": "template",
    "template": "Hello {{input.name}}! Your account status is ACTIVE. You have 15 credits remaining. Last login: 2024-01-15. Current session: {{input.session_id}}"
  }
}
```

## Limitations

Template responses:
- ✅ Can reference input parameters
- ✅ Support nested object access
- ✅ Mix static and dynamic content
- ❌ Cannot perform calculations or logic
- ❌ Cannot format dates or numbers
- ❌ Cannot iterate over arrays
- ❌ Cannot make conditional decisions

For more complex needs, see:
- [Function Responses](../advanced/function-responses.md) - Full JavaScript logic
- [Sequence Responses](../advanced/sequence-responses.md) - Different responses per call

## Best Practices

1. **Provide fallbacks**: Handle missing optional parameters gracefully
2. **Clear parameter names**: Use descriptive input schema properties
3. **Test with real data**: Verify templates with actual input values
4. **Document expected format**: Explain what parameters are available

## Next Steps

- [Sequence Responses](../advanced/sequence-responses.md) - Return different data each call
- [Function Responses](../advanced/function-responses.md) - Implement complex logic
- [Admin API](../advanced/admin-api.md) - Full tool management
