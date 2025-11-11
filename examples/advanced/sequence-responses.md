# Sequence Mock Responses

Sequence responses return different data on each consecutive call. This is perfect for testing workflows, state transitions, and multi-step processes.

## When to Use Sequence Responses

- **State machines**: Simulate status changes (pending → processing → complete)
- **Pagination testing**: Return different pages of results
- **Rate limiting**: Test behavior when limits are hit
- **Workflow testing**: Multi-step processes with different outcomes
- **Time-based changes**: Simulate data that evolves over time

## How Sequences Work

1. Define an array of responses
2. Each call returns the next response in the array
3. After the last response, it cycles back to the first (or stays on last, depending on config)
4. Each user gets their own sequence state

## Basic Sequence Example

### Creating a Sequence Tool

```bash
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "check_job_status",
    "description": "Check the status of a background job",
    "inputSchema": {
      "type": "object",
      "properties": {
        "job_id": {"type": "string"}
      },
      "required": ["job_id"]
    },
    "mockResponse": {
      "type": "sequence",
      "responses": [
        {
          "data": {
            "status": "pending",
            "progress": 0,
            "message": "Job queued"
          }
        },
        {
          "data": {
            "status": "processing",
            "progress": 35,
            "message": "Processing data"
          }
        },
        {
          "data": {
            "status": "processing",
            "progress": 70,
            "message": "Finalizing results"
          }
        },
        {
          "data": {
            "status": "complete",
            "progress": 100,
            "message": "Job completed successfully",
            "result": {
              "records_processed": 1250,
              "output_file": "results.csv"
            }
          }
        }
      ]
    }
  }'
```

### Calling the Tool Multiple Times

**Note**: Replace `YOUR_MCP_SERVER_ID` with your unique server ID from the dashboard.

**First call:**
```bash
curl -X POST https://mcp.mcpmock.ai/YOUR_MCP_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "tools/call",
    "params": {
      "name": "check_job_status",
      "arguments": {"job_id": "job_123"}
    }
  }'
```

**Note**: MCP endpoints are open and don't require authentication.

**Response:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "{\"status\":\"pending\",\"progress\":0,\"message\":\"Job queued\"}"
      }
    ]
  }
}
```

**Second call (same endpoint):**
```json
{
  "status": "processing",
  "progress": 35,
  "message": "Processing data"
}
```

**Third call:**
```json
{
  "status": "processing",
  "progress": 70,
  "message": "Finalizing results"
}
```

**Fourth call:**
```json
{
  "status": "complete",
  "progress": 100,
  "message": "Job completed successfully",
  "result": {
    "records_processed": 1250,
    "output_file": "results.csv"
  }
}
```

## More Examples

### Paginated Results

```json
{
  "name": "list_items",
  "description": "Get paginated list of items",
  "inputSchema": {
    "type": "object",
    "properties": {
      "page": {"type": "number"}
    }
  },
  "mockResponse": {
    "type": "sequence",
    "responses": [
      {
        "data": {
          "items": [
            {"id": 1, "name": "Item 1"},
            {"id": 2, "name": "Item 2"},
            {"id": 3, "name": "Item 3"}
          ],
          "page": 1,
          "has_more": true
        }
      },
      {
        "data": {
          "items": [
            {"id": 4, "name": "Item 4"},
            {"id": 5, "name": "Item 5"},
            {"id": 6, "name": "Item 6"}
          ],
          "page": 2,
          "has_more": true
        }
      },
      {
        "data": {
          "items": [
            {"id": 7, "name": "Item 7"}
          ],
          "page": 3,
          "has_more": false
        }
      }
    ]
  }
}
```

### API Rate Limiting

```json
{
  "name": "api_call",
  "description": "Simulate API with rate limiting",
  "inputSchema": {
    "type": "object",
    "properties": {
      "endpoint": {"type": "string"}
    }
  },
  "mockResponse": {
    "type": "sequence",
    "responses": [
      {"data": {"success": true, "remaining_calls": 5}},
      {"data": {"success": true, "remaining_calls": 4}},
      {"data": {"success": true, "remaining_calls": 3}},
      {"data": {"success": true, "remaining_calls": 2}},
      {"data": {"success": true, "remaining_calls": 1}},
      {
        "data": {
          "success": false,
          "error": "Rate limit exceeded",
          "retry_after": 60
        }
      }
    ]
  }
}
```

### Multi-Step Wizard

```json
{
  "name": "wizard_step",
  "description": "Navigate through setup wizard",
  "inputSchema": {
    "type": "object",
    "properties": {
      "action": {"type": "string"}
    }
  },
  "mockResponse": {
    "type": "sequence",
    "responses": [
      {
        "data": {
          "step": 1,
          "title": "Welcome",
          "description": "Welcome to the setup wizard",
          "next_action": "Configure profile"
        }
      },
      {
        "data": {
          "step": 2,
          "title": "Profile Setup",
          "description": "Enter your profile information",
          "fields": ["name", "email", "company"],
          "next_action": "Choose plan"
        }
      },
      {
        "data": {
          "step": 3,
          "title": "Plan Selection",
          "description": "Choose your subscription plan",
          "options": ["Free", "Pro", "Enterprise"],
          "next_action": "Payment"
        }
      },
      {
        "data": {
          "step": 4,
          "title": "Complete",
          "description": "Setup completed successfully!",
          "next_action": "Dashboard"
        }
      }
    ]
  }
}
```

### Deployment Progress

```json
{
  "name": "deploy_status",
  "description": "Check deployment progress",
  "inputSchema": {
    "type": "object",
    "properties": {
      "deployment_id": {"type": "string"}
    }
  },
  "mockResponse": {
    "type": "sequence",
    "responses": [
      {
        "data": {
          "status": "building",
          "phase": "Installing dependencies",
          "logs": ["npm install started..."]
        }
      },
      {
        "data": {
          "status": "building",
          "phase": "Running tests",
          "logs": ["All tests passed"]
        }
      },
      {
        "data": {
          "status": "deploying",
          "phase": "Uploading artifacts",
          "logs": ["Uploading to production..."]
        }
      },
      {
        "data": {
          "status": "success",
          "phase": "Live",
          "logs": ["Deployment complete"],
          "url": "https://app.example.com"
        }
      }
    ]
  }
}
```

## Cycling Behavior

By default, after the last response, the sequence stays on the last response:

```
Call 1: Response 0
Call 2: Response 1
Call 3: Response 2
Call 4: Response 2 (stays here)
Call 5: Response 2 (stays here)
```

## Combining with Templates

You can use templates within sequence responses for dynamic content:

```json
{
  "mockResponse": {
    "type": "sequence",
    "responses": [
      {
        "template": "Processing {{input.file_name}}... 0%"
      },
      {
        "template": "Processing {{input.file_name}}... 50%"
      },
      {
        "template": "Processing {{input.file_name}}... 100% - Complete!"
      }
    ]
  }
}
```

## Testing with Sequences

Sequences are perfect for integration testing. Replace `YOUR_MCP_SERVER_ID` with your actual server ID.

```javascript
// Test a retry mechanism with actual MCP endpoint
async function testRateLimiting(mcpServerId) {
  for (let i = 0; i < 6; i++) {
    const response = await fetch(`https://mcp.mcpmock.ai/${mcpServerId}/`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        jsonrpc: '2.0',
        id: i + 1,
        method: 'tools/call',
        params: {
          name: 'api_call',
          arguments: { endpoint: '/data' }
        }
      })
    });
    
    const result = await response.json();
    const data = JSON.parse(result.result.content[0].text);
    
    if (data.success) {
      console.log(`✅ Success on attempt ${i + 1}, remaining calls: ${data.remaining_calls}`);
    } else {
      console.log(`❌ ${data.error}, retry after ${data.retry_after}s`);
      break;
    }
  }
}

// Usage: testRateLimiting('04c81468-0031-709f-918e-144b37ba85b8');
```

**Example with polling a job status:**

```javascript
// Poll job status until complete
async function pollJobStatus(mcpServerId, jobId) {
  let status = 'pending';
  let attempts = 0;
  const maxAttempts = 10;
  
  while (status !== 'complete' && attempts < maxAttempts) {
    const response = await fetch(`https://mcp.mcpmock.ai/${mcpServerId}/`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        jsonrpc: '2.0',
        id: attempts + 1,
        method: 'tools/call',
        params: {
          name: 'check_job_status',
          arguments: { job_id: jobId }
        }
      })
    });
    
    const result = await response.json();
    const jobData = JSON.parse(result.result.content[0].text);
    
    status = jobData.status;
    console.log(`Attempt ${attempts + 1}: ${jobData.status} - ${jobData.progress}% - ${jobData.message}`);
    
    attempts++;
    
    // Wait before next poll
    if (status !== 'complete') {
      await new Promise(resolve => setTimeout(resolve, 1000));
    }
  }
  
  return status === 'complete';
}

// Usage: pollJobStatus('04c81468-0031-709f-918e-144b37ba85b8', 'job_123');
```

## Best Practices

1. **Realistic progression**: Make sequences follow real-world patterns
2. **Clear state changes**: Each response should show clear progress
3. **Final state stability**: Last response should be stable/terminal
4. **Document sequence**: Explain what each response represents
5. **Test full cycle**: Call tool enough times to reach end of sequence

## Limitations

Sequence responses:
- ✅ Return different data per call
- ✅ Can include templates in each response
- ✅ Per-user state tracking
- ❌ Cannot skip or jump to specific positions
- ❌ No conditional branching based on input
- ❌ Cannot reset to beginning programmatically

For conditional logic, see:
- [Function Responses](function-responses.md) - Full JavaScript control

## Next Steps

- [Function Responses](function-responses.md) - Implement custom logic
- [Admin API](admin-api.md) - Update sequences dynamically
- [Testing Guide](../../tutorials/testing-ai-apps.md) - Test workflows with sequences
