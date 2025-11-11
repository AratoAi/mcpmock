# Authentication Flow

This example demonstrates how to authenticate with MCPMock using OAuth providers.

## Overview

MCPMock uses OAuth 2.0 for authentication, supporting:
- **Google** OAuth
- **GitHub** OAuth  
- **GitLab** OAuth

After authentication, you receive a JWT token that must be included in all API requests.

## Step 1: Sign Up / Log In

Visit [https://www.mcpmock.ai](https://www.mcpmock.ai) and click the login button for your preferred provider:

- **Google**: Sign in with your Google account
- **GitHub**: Authorize with your GitHub account
- **GitLab**: Authenticate with your GitLab credentials

## Step 2: Retrieve Your Token

After successful authentication, navigate to the **Settings** tab in the MCPMock dashboard to find your API token.

### From Application Code

```javascript
// In your web application
const token = 'YOUR_TOKEN_FROM_SETTINGS';

// Make API calls with the token
fetch('https://api.mcpmock.ai/tools', {
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  }
});
```

## Step 3: Use the Token in API Calls

Include the token in the `Authorization` header of all requests:

```bash
curl -X GET https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -H "Content-Type: application/json"
```

## Token Format

The token is a JWT (JSON Web Token) that contains:
- User identification (sub)
- Email address
- Provider information
- Expiration time

Example decoded token structure:
```json
{
  "sub": "google_123456789",
  "email": "user@example.com",
  "email_verified": true,
  "iss": "https://cognito-idp.us-east-1.amazonaws.com/us-east-1_XXXXXXXXX",
  "token_use": "id",
  "auth_time": 1699999999,
  "exp": 1700003599
}
```

## Token Expiration

Tokens typically expire after 1 hour. When a token expires:
1. You'll receive a `401 Unauthorized` response
2. Re-authenticate through the web UI
3. A new token will be automatically stored in localStorage

## Security Best Practices

1. **Never commit tokens** to version control
2. **Store tokens securely** in environment variables for server-side applications
3. **Refresh tokens** before they expire in production applications
4. **Use HTTPS** for all API communications (MCPMock enforces HTTPS)

## Example: Complete Authentication Flow

```javascript
// 1. User clicks login button - redirects to OAuth provider
window.location.href = 'https://www.mcpmock.ai'; // User clicks Google/GitHub/GitLab

// 2. After OAuth callback, get token from Settings tab
// 3. Use token for API calls
const authToken = 'YOUR_TOKEN_FROM_SETTINGS'; // Get from Settings tab

// 4. Make authenticated request
async function listTools() {
  const response = await fetch('https://api.mcpmock.ai/tools', {
    method: 'GET',
    headers: {
      'Authorization': `Bearer ${authToken}`,
      'Content-Type': 'application/json'
    }
  });
  
  if (response.status === 401) {
    // Token expired - redirect to login
    window.location.href = 'https://www.mcpmock.ai';
    return;
  }
  
  const tools = await response.json();
  console.log('Your tools:', tools);
}

listTools();
```

## Testing Authentication

You can verify your authentication by calling the tools list endpoint:

```bash
# Replace YOUR_TOKEN with your actual token
curl -X GET https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json"
```

Successful response (HTTP 200):
```json
{
  "tools": [
    {
      "name": "example_tool",
      "description": "An example tool",
      "inputSchema": {...},
      "mockResponse": {...}
    }
  ]
}
```

Unauthorized response (HTTP 401):
```json
{
  "message": "Unauthorized"
}
```

## Next Steps

- [Create your first static tool](static-responses.md)
- [Learn about template responses](template-responses.md)
- [Explore the Admin API](../advanced/admin-api.md)
