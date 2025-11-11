# MCPMock - Examples & Showcase

Welcome to the **MCPMock Examples Repository**! This repository showcases the capabilities of [mcpmock.ai](https://www.mcpmock.ai), a powerful mock server implementing the Model Context Protocol (MCP).

## 🌟 What is MCPMock?

MCPMock is a production-ready mock server that helps you:
- **Test AI integrations** without depending on real services
- **Develop MCP clients** with realistic mock responses
- **Create dynamic mock tools** with templates, sequences, and functions
- **Prototype quickly** with configurable mock behaviors

**Try it live:** [https://www.mcpmock.ai](https://www.mcpmock.ai)

## 🚀 Quick Start

### 1. Sign Up
Visit [mcpmock.ai](https://www.mcpmock.ai) and sign up using Google, GitHub, or GitLab OAuth.

### 2. Get Your API Token
After logging in, navigate to the **Settings** tab to find your API token.

### 3. Get Your MCP Server Endpoint
Your unique MCP server endpoint URL is available in:
- The **Dashboard** in the MCPMock UI
- Or via API call: `GET https://api.mcpmock.ai/me/endpoint` (requires authentication)

The endpoint format is: `https://mcp.mcpmock.ai/{your-server-id}/`

Example: `https://mcp.mcpmock.ai/0123456-1234-5678-90ab-1234567890ab/`

### 4. Create Your First Mock Tool

```bash
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "get_weather",
    "description": "Get weather information for a location",
    "inputSchema": {
      "type": "object",
      "properties": {
        "location": {"type": "string"}
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
  }'
```

### 5. Use the MCP Protocol

**Note**: MCP endpoints don't require authentication. Replace `YOUR_MCP_SERVER_ID` with your actual server ID from the dashboard.

```bash
# Initialize the connection
curl -X POST https://mcp.mcpmock.ai/YOUR_MCP_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "initialize",
    "params": {
      "protocolVersion": "2024-11-05",
      "clientInfo": {"name": "test-client", "version": "1.0.0"}
    }
  }'

# List available tools
curl -X POST https://mcp.mcpmock.ai/YOUR_MCP_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 2,
    "method": "tools/list"
  }'

# Call your tool
curl -X POST https://mcp.mcpmock.ai/YOUR_MCP_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 3,
    "method": "tools/call",
    "params": {
      "name": "get_weather",
      "arguments": {"location": "San Francisco"}
    }
  }'
```

## 📚 Examples Library

### Basic Examples
- [**Authentication Flow**](examples/basic/authentication.md) - OAuth login and token management
- [**Static Mock Responses**](examples/basic/static-responses.md) - Simple fixed responses
- [**Template Responses**](examples/basic/template-responses.md) - Dynamic responses using input parameters

### Advanced Examples
- [**Sequence Responses**](examples/advanced/sequence-responses.md) - Return different responses on each call
- [**Function Responses**](examples/advanced/function-responses.md) - JavaScript functions for complex logic
- [**Admin API**](examples/advanced/admin-api.md) - Full CRUD operations on mock tools
- [**Error Handling**](examples/advanced/error-handling.md) - Working with errors and edge cases

### Integration Examples
- [**Python Client**](examples/integrations/python-client.md) - Using MCPMock with Python
- [**Node.js Client**](examples/integrations/nodejs-client.md) - Using MCPMock with Node.js
- [**Testing Frameworks**](examples/integrations/testing-frameworks.md) - Integration with Jest, Pytest, etc.

## 🎓 Tutorials

1. [**Getting Started**](tutorials/getting-started.md) - Complete walkthrough for new users
2. [**Creating Dynamic Tools**](tutorials/dynamic-tools.md) - Build tools that respond intelligently
3. [**Testing AI Applications**](tutorials/testing-ai-apps.md) - Use MCPMock in your test suite
4. [**Building MCP Clients**](tutorials/building-mcp-clients.md) - Develop clients using MCPMock

## 🎯 Use Cases

- **AI Agent Development**: Test your agents against controlled mock services
- **MCP Client Testing**: Validate protocol implementation without real servers
- **API Prototyping**: Mock APIs before backend implementation
- **Integration Testing**: Reliable test fixtures for CI/CD pipelines
- **Training & Demos**: Consistent environments for workshops and demos

## 📖 Documentation

For comprehensive documentation, visit the [main repository](https://github.com/AratoAi/mcp-mock-server).

## 🛠️ Mock Response Types

MCPMock supports four powerful mock response types:

### Static
Fixed responses that never change:
```json
{
  "type": "static",
  "data": {"result": "Hello, World!"}
}
```

### Template
Dynamic responses using input parameters:
```json
{
  "type": "template",
  "template": "Hello, {{name}}! Temperature is {{input.temp}}°F"
}
```

### Sequence
Different responses for each call:
```json
{
  "type": "sequence",
  "responses": [
    {"data": {"status": "pending"}},
    {"data": {"status": "processing"}},
    {"data": {"status": "complete"}}
  ]
}
```

### Function
JavaScript functions for complex logic:
```json
{
  "type": "function",
  "code": "function(input) { return { result: input.a + input.b }; }"
}
```

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## 📝 License

This examples repository is licensed under the MIT License. See [LICENSE](LICENSE) for details.

The MCPMock platform itself is proprietary software. Visit [mcpmock.ai](https://www.mcpmock.ai) for terms of service.

## 🔗 Resources

- **Website**: [https://www.mcpmock.ai](https://www.mcpmock.ai)
- **Main Repository**: [https://github.com/AratoAi/mcp-mock-server](https://github.com/AratoAi/mcp-mock-server)
- **Support**: Open an issue in this repository

## ⭐ Show Your Support

If you find MCPMock useful, please star this repository and share it with others!

---

Built with ❤️ by [Arato AI](https://github.com/AratoAi)
