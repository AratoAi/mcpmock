# Getting Started with MCPMock

This tutorial will walk you through everything you need to start using MCPMock, from creating your first tool to calling it via the MCP protocol.

## What You'll Learn

By the end of this tutorial, you'll be able to:
1. Authenticate with MCPMock
2. Create mock tools with different response types
3. Use the Admin API to manage tools
4. Call your tools using the MCP protocol

## Prerequisites

- A web browser
- `curl` or similar HTTP client (or use any programming language)
- 10 minutes of your time

## Step 1: Sign Up

1. Visit [https://www.mcpmock.ai](https://www.mcpmock.ai)
2. Click on one of the OAuth providers:
   - **Google** - Sign in with your Google account
   - **GitHub** - Authorize with GitHub
   - **GitLab** - Authenticate with GitLab

After authentication, you'll be redirected back to the MCPMock dashboard.

## Step 2: Get Your Authentication Token

Your authentication token is available in the **Settings** tab of the MCPMock dashboard.

### For Command Line Use

1. Navigate to the **Settings** tab in the MCPMock dashboard
2. Copy your API token
3. Save it for use in your commands:

```bash
# Save your token in an environment variable
export MCPMOCK_TOKEN="your_token_here"

# Or create a .env file (don't commit this!)
echo "MCPMOCK_TOKEN=your_token_here" > .env
```

**Important**: 
- API endpoints (api.mcpmock.ai/tools, api.mcpmock.ai/me/endpoint) require authentication with this token
- MCP protocol endpoints are open and don't require authentication

## Step 3: Get Your MCP Server Endpoint

Your unique MCP server endpoint is required to call tools via the MCP protocol.

### Finding Your Endpoint

**Option 1 - Via Dashboard**: After logging in, your MCP server endpoint is displayed in the dashboard

**Option 2 - Via API**: Call the endpoint API with your token:
```bash
curl -X GET https://api.mcpmock.ai/me/endpoint \
  -H "Authorization: Bearer $MCPMOCK_TOKEN"
```

Your endpoint will look like:
```
https://mcp.mcpmock.ai/04c81468-0031-709f-918e-144b37ba85b8/
```

Save this endpoint URL - you'll need it for all MCP protocol calls.

## Step 4: Create Your First Tool

Let's create a simple weather tool that returns static data.

```bash
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer $MCPMOCK_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "get_weather",
    "description": "Get current weather for a city",
    "inputSchema": {
      "type": "object",
      "properties": {
        "city": {
          "type": "string",
          "description": "City name"
        }
      },
      "required": ["city"]
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

**Expected Response:**
```json
{
  "message": "Tool created successfully",
  "tool": {
    "name": "get_weather",
    ...
  }
}
```

## Step 5: Verify Your Tool

List all your tools to confirm creation:

```bash
curl -X GET https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer $MCPMOCK_TOKEN"
  -H "Content-Type: application/json"
```

You should see your `get_weather` tool in the response.

## Step 6: Initialize MCP Connection

Before calling tools via MCP, initialize the protocol connection. Replace `YOUR_MCP_ENDPOINT` with your actual endpoint from Step 3.

```bash
curl -X POST https://mcp.mcpmock.ai/YOUR_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "initialize",
    "params": {
      "protocolVersion": "2024-11-05",
      "clientInfo": {
        "name": "my-test-client",
        "version": "1.0.0"
      }
    }
  }'
```

**Note**: MCP endpoints are open and don't require authentication.

**Expected Response:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "protocolVersion": "2024-11-05",
    "serverInfo": {
      "name": "mcpmock-server",
      "version": "1.0.0"
    },
    "capabilities": {
      "tools": {}
    }
  }
}
```

## Step 7: List Available Tools via MCP

```bash
curl -X POST https://mcp.mcpmock.ai/YOUR_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 2,
    "method": "tools/list"
  }'
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "result": {
    "tools": [
      {
        "name": "get_weather",
        "description": "Get current weather for a city",
        "inputSchema": {
          "type": "object",
          "properties": {
            "city": {"type": "string", "description": "City name"}
          },
          "required": ["city"]
        }
      }
    ]
  }
}
```

## Step 8: Call Your Tool

Now call the tool with actual parameters:

```bash
curl -X POST https://mcp.mcpmock.ai/YOUR_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 3,
    "method": "tools/call",
    "params": {
      "name": "get_weather",
      "arguments": {
        "city": "San Francisco"
      }
    }
  }'
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "id": 3,
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

🎉 **Success!** You've created and called your first mock tool!

## Step 9: Create a Dynamic Tool

Let's create a more interesting tool using templates:

```bash
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer $MCPMOCK_TOKEN"
  -H "Content-Type: application/json" \
  -d '{
    "name": "greet_user",
    "description": "Generate a personalized greeting",
    "inputSchema": {
      "type": "object",
      "properties": {
        "name": {"type": "string"},
        "time_of_day": {
          "type": "string",
          "enum": ["morning", "afternoon", "evening"]
        }
      },
      "required": ["name"]
    },
    "mockResponse": {
      "type": "template",
      "template": "Good {{input.time_of_day}}, {{input.name}}! Welcome to MCPMock. The current time is 10:30 AM."
    }
  }'
```

Call it with different inputs:

```bash
curl -X POST https://mcp.mcpmock.ai/YOUR_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 4,
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

**Response:**
```json
{
  "jsonrpc": "2.0",
  "id": 4,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "Good morning, Alice! Welcome to MCPMock. The current time is 10:30 AM."
      }
    ]
  }
}
```

## Step 10: Update a Tool

Made a mistake or want to change something? Update the tool:

```bash
curl -X PUT https://api.mcpmock.ai/tools/get_weather \
  -H "Authorization: Bearer $MCPMOCK_TOKEN"
  -H "Content-Type: application/json" \
  -d '{
    "name": "get_weather",
    "description": "Get current weather - now with more data!",
    "inputSchema": {
      "type": "object",
      "properties": {
        "city": {"type": "string"}
      },
      "required": ["city"]
    },
    "mockResponse": {
      "type": "static",
      "data": {
        "temperature": 72,
        "condition": "sunny",
        "humidity": 45,
        "wind_speed": 8,
        "uv_index": 7,
        "visibility": 10
      }
    }
  }'
```

## Step 10: Clean Up

Delete tools you don't need anymore:

```bash
curl -X DELETE https://api.mcpmock.ai/tools/greet_user \
  -H "Authorization: Bearer $MCPMOCK_TOKEN" \
  -H "Content-Type: application/json"
```

## Complete Workflow Script

Here's a complete bash script combining all steps:

```bash
#!/bin/bash

# Set your token and MCP server ID
export MCPMOCK_TOKEN="your_token_here"
export MCP_SERVER_ID="your_server_id_here"  # Get from dashboard or API

# Create tool
echo "Creating tool..."
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer $MCPMOCK_TOKEN"
  -H "Content-Type: application/json" \
  -d '{
    "name": "example_tool",
    "description": "An example tool",
    "inputSchema": {
      "type": "object",
      "properties": {
        "message": {"type": "string"}
      }
    },
    "mockResponse": {
      "type": "template",
      "template": "You said: {{input.message}}"
    }
  }'

# Initialize MCP
echo -e "\n\nInitializing MCP..."
curl -X POST https://mcp.mcpmock.ai/$MCP_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "initialize",
    "params": {
      "protocolVersion": "2024-11-05",
      "clientInfo": {"name": "test", "version": "1.0"}
    }
  }'

# List tools
echo -e "\n\nListing tools..."
curl -X POST https://mcp.mcpmock.ai/$MCP_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 2,
    "method": "tools/list"
  }'

# Call tool
echo -e "\n\nCalling tool..."
curl -X POST https://mcp.mcpmock.ai/$MCP_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 3,
    "method": "tools/call",
    "params": {
      "name": "example_tool",
      "arguments": {"message": "Hello MCPMock!"}
    }
  }'

echo -e "\n\nDone!"
```

## Troubleshooting

### "Unauthorized" Error

- Verify your token is correct
- Check token hasn't expired (tokens last 1 hour)
- Re-login to get a fresh token

### "Tool already exists" Error

- Tool names must be unique per account
- Use a different name or delete the existing tool first
- Use PUT to update instead of POST to create

### "Invalid input schema" Error

- Ensure your JSON is valid
- Check schema follows JSON Schema specification
- Verify required fields are present

### Empty Response from tools/list

- Make sure you've created at least one tool via Admin API
- Check you're using the correct authorization token
- Verify the tool creation succeeded (check response)

## Next Steps

Now that you know the basics:

1. **Learn about response types**:
   - [Static Responses](../examples/basic/static-responses.md)
   - [Template Responses](../examples/basic/template-responses.md)
   - [Sequence Responses](../examples/advanced/sequence-responses.md)
   - [Function Responses](../examples/advanced/function-responses.md)

2. **Explore advanced features**:
   - [Admin API Reference](../examples/advanced/admin-api.md)
   - [Error Handling](../examples/advanced/error-handling.md)

3. **Build integrations**:
   - [Python Client](../examples/integrations/python-client.md)
   - [Node.js Client](../examples/integrations/nodejs-client.md)

4. **Real-world usage**:
   - [Testing AI Applications](testing-ai-apps.md)
   - [Building MCP Clients](building-mcp-clients.md)

## Resources

- **MCP Protocol**: [https://modelcontextprotocol.io](https://modelcontextprotocol.io)
- **Support**: Open an issue in the [examples repo](https://github.com/AratoAi/mcpmock/issues)

Happy mocking! 🚀
