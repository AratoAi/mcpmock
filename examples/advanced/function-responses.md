# Function Mock Responses

Function responses allow you to write JavaScript code that executes when a tool is called. This provides maximum flexibility for complex mock logic, calculations, and conditional responses.

## When to Use Function Responses

- **Complex calculations**: Math operations, data transformations
- **Conditional logic**: Different responses based on input validation
- **Data generation**: Create dynamic, computed values
- **Stateful responses**: Track and modify state across calls
- **Input validation**: Return errors for invalid inputs

## Function Basics

Functions receive the `input` parameter and must return a response object:

```javascript
function(input) {
  // Your logic here
  return {
    result: "your response"
  };
}
```

## Basic Function Example

### Creating a Calculator Tool

```bash
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "calculator",
    "description": "Perform arithmetic operations",
    "inputSchema": {
      "type": "object",
      "properties": {
        "operation": {
          "type": "string",
          "enum": ["add", "subtract", "multiply", "divide"]
        },
        "a": {"type": "number"},
        "b": {"type": "number"}
      },
      "required": ["operation", "a", "b"]
    },
    "mockResponse": {
      "type": "function",
      "code": "function(input) { const ops = { add: (a,b) => a+b, subtract: (a,b) => a-b, multiply: (a,b) => a*b, divide: (a,b) => b!==0 ? a/b : '\''Error: Division by zero'\'' }; return { operation: input.operation, a: input.a, b: input.b, result: ops[input.operation](input.a, input.b) }; }"
    }
  }'
```

**Note**: The function code is on one line. For readability, here's the formatted version:

```javascript
function(input) {
  const ops = {
    add: (a, b) => a + b,
    subtract: (a, b) => a - b,
    multiply: (a, b) => a * b,
    divide: (a, b) => b !== 0 ? a / b : 'Error: Division by zero'
  };
  
  return {
    operation: input.operation,
    a: input.a,
    b: input.b,
    result: ops[input.operation](input.a, input.b)
  };
}
```

### Calling the Function Tool

**Note**: Replace `YOUR_MCP_SERVER_ID` with your unique server ID.

```bash
curl -X POST https://mcp.mcpmock.ai/YOUR_MCP_SERVER_ID/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "tools/call",
    "params": {
      "name": "calculator",
      "arguments": {
        "operation": "multiply",
        "a": 15,
        "b": 7
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
        "text": "{\"operation\":\"multiply\",\"a\":15,\"b\":7,\"result\":105}"
      }
    ]
  }
}
```

## More Examples

### Input Validation

```javascript
function(input) {
  // Validate email format
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  
  if (!input.email) {
    return {
      success: false,
      error: "Email is required"
    };
  }
  
  if (!emailRegex.test(input.email)) {
    return {
      success: false,
      error: "Invalid email format"
    };
  }
  
  return {
    success: true,
    email: input.email,
    message: "Email validated successfully"
  };
}
```

### Conditional Responses

```javascript
function(input) {
  const user = input.user_type;
  
  if (user === 'admin') {
    return {
      access_level: 'full',
      permissions: ['read', 'write', 'delete', 'admin'],
      features: ['dashboard', 'users', 'settings', 'billing']
    };
  } else if (user === 'editor') {
    return {
      access_level: 'limited',
      permissions: ['read', 'write'],
      features: ['dashboard', 'content']
    };
  } else {
    return {
      access_level: 'readonly',
      permissions: ['read'],
      features: ['dashboard']
    };
  }
}
```

### Data Transformation

```javascript
function(input) {
  // Convert temperature
  const celsius = input.temperature;
  const fahrenheit = (celsius * 9/5) + 32;
  const kelvin = celsius + 273.15;
  
  return {
    original: {
      value: celsius,
      unit: 'celsius'
    },
    conversions: {
      fahrenheit: Math.round(fahrenheit * 100) / 100,
      kelvin: Math.round(kelvin * 100) / 100
    },
    description: `${celsius}°C is ${fahrenheit.toFixed(1)}°F or ${kelvin.toFixed(1)}K`
  };
}
```

### Random Data Generation

```javascript
function(input) {
  const types = ['info', 'warning', 'error', 'success'];
  const messages = {
    info: 'System running normally',
    warning: 'High memory usage detected',
    error: 'Service connection failed',
    success: 'Backup completed successfully'
  };
  
  const randomType = types[Math.floor(Math.random() * types.length)];
  
  return {
    timestamp: new Date().toISOString(),
    type: randomType,
    message: messages[randomType],
    severity: randomType === 'error' ? 'high' : 'low',
    requires_action: randomType === 'error'
  };
}
```

### Stateful Counter

```javascript
function(input) {
  // Note: Each tool maintains its own state per user
  if (!this.counter) {
    this.counter = 0;
  }
  
  if (input.action === 'increment') {
    this.counter++;
  } else if (input.action === 'decrement') {
    this.counter--;
  } else if (input.action === 'reset') {
    this.counter = 0;
  }
  
  return {
    action: input.action,
    current_value: this.counter,
    timestamp: new Date().toISOString()
  };
}
```

### Price Calculator with Discounts

```javascript
function(input) {
  const basePrice = input.quantity * input.unit_price;
  let discount = 0;
  let discountReason = 'none';
  
  // Volume discount
  if (input.quantity >= 100) {
    discount = 0.20; // 20%
    discountReason = 'volume_discount_20';
  } else if (input.quantity >= 50) {
    discount = 0.10; // 10%
    discountReason = 'volume_discount_10';
  }
  
  // Promo code
  if (input.promo_code === 'SAVE15') {
    discount = Math.max(discount, 0.15);
    discountReason = 'promo_code_SAVE15';
  }
  
  const discountAmount = basePrice * discount;
  const finalPrice = basePrice - discountAmount;
  const tax = finalPrice * 0.08; // 8% tax
  const total = finalPrice + tax;
  
  return {
    base_price: basePrice.toFixed(2),
    discount_percent: (discount * 100).toFixed(0) + '%',
    discount_reason: discountReason,
    discount_amount: discountAmount.toFixed(2),
    subtotal: finalPrice.toFixed(2),
    tax: tax.toFixed(2),
    total: total.toFixed(2),
    items: input.quantity
  };
}
```

### Date/Time Functions

```javascript
function(input) {
  const now = new Date();
  const targetDate = new Date(input.date);
  
  if (isNaN(targetDate)) {
    return {
      success: false,
      error: 'Invalid date format'
    };
  }
  
  const diffMs = targetDate - now;
  const diffDays = Math.floor(diffMs / (1000 * 60 * 60 * 24));
  const diffHours = Math.floor((diffMs % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
  
  return {
    input_date: input.date,
    current_date: now.toISOString(),
    target_date: targetDate.toISOString(),
    is_future: diffMs > 0,
    is_past: diffMs < 0,
    days_difference: Math.abs(diffDays),
    hours_difference: Math.abs(diffHours),
    human_readable: diffMs > 0 
      ? `${Math.abs(diffDays)} days from now`
      : `${Math.abs(diffDays)} days ago`
  };
}
```

## Error Handling

Always handle potential errors in your functions:

```javascript
function(input) {
  try {
    // Your logic
    const result = someOperation(input);
    
    return {
      success: true,
      data: result
    };
  } catch (error) {
    return {
      success: false,
      error: error.message,
      input_received: input
    };
  }
}
```

## Function Context

Functions have access to:
- `input`: The arguments passed to the tool
- `this`: Persistent state object (per user, per tool)
- Standard JavaScript built-ins: `Math`, `Date`, `JSON`, etc.

Functions do NOT have access to:
- Node.js modules (`require`, `import`)
- Browser APIs (`window`, `document`, `fetch`)
- External network calls
- File system

## Best Practices

1. **Keep functions simple**: Break complex logic into smaller pieces
2. **Validate inputs**: Always check for required fields and valid formats
3. **Handle errors**: Use try/catch and return meaningful error messages
4. **Return consistent structure**: Maintain predictable response format
5. **Test thoroughly**: Call with various inputs including edge cases
6. **Document behavior**: Describe what the function does in the tool description

## Formatting Code for API Calls

When creating tools via API, JavaScript functions must be:
1. On a single line
2. Properly escaped

**Multi-line function:**
```javascript
function(input) {
  const result = input.a + input.b;
  return {result: result};
}
```

**API-ready (single line):**
```javascript
function(input) { const result = input.a + input.b; return {result: result}; }
```

**In curl command (escaped):**
```bash
"code": "function(input) { const result = input.a + input.b; return {result: result}; }"
```

## Limitations

Function responses:
- ✅ Full JavaScript logic
- ✅ Conditional responses
- ✅ Calculations and transformations
- ✅ Per-user state tracking
- ✅ Input validation
- ❌ No external API calls
- ❌ No Node.js modules
- ❌ No async/await (must be synchronous)
- ❌ Limited to JavaScript ES5/ES6

## Next Steps

- [Admin API](admin-api.md) - Create and manage function tools
- [Testing Guide](../../tutorials/testing-ai-apps.md) - Test complex function logic
- [Error Handling](error-handling.md) - Handle edge cases properly
