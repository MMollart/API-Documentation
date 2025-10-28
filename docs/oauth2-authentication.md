# OAuth2 Authorization Examples

This document provides examples for both OAuth2 authentication flows supported by the OSM API.

## Flow Selection Guide

### When to Use Client Credentials Flow
- **Use Case**: Automated scripts, background jobs, server-to-server communication
- **Characteristics**: No user interaction, single account, simpler implementation
- **Best For**: Cron jobs, data synchronization, automated reporting

### When to Use Authorization Code Flow
- **Use Case**: Web apps, mobile apps, desktop applications with multiple users
- **Characteristics**: User login screen, per-user authentication, better security
- **Best For**: Multi-tenant applications, user-facing dashboards, mobile apps

---

## 1. Client Credentials Flow (Machine-to-Machine)

### Step 1: Request Access Token

```http
POST /oauth/token HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=your_client_id&client_secret=your_client_secret&scope=section:member:read section:finance:read
```

### Response: Access Token

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "def502004a3c8b...",
  "scope": "section:member:read section:finance:read"
}
```

### Step 2: Use Access Token

```http
GET /ext/members/contact/?action=getListOfMembers&sectionid=12345&termid=67890 HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

## 2. Authorization Code Flow (User Login)

### Step 1: Redirect User to Authorization Endpoint

Build the authorization URL and redirect the user's browser:

```
https://www.onlinescoutmanager.co.uk/oauth/authorize?response_type=code&client_id=your_client_id&redirect_uri=https://your-app.com/oauth/callback&scope=section:member:read section:finance:read&state=random_state_string_123
```

**Parameters:**
- `response_type`: Must be `code`
- `client_id`: Your application's client ID
- `redirect_uri`: HTTPS URL where OSM will redirect after login (must be registered with OSM)
- `scope`: Space-separated list of permissions
- `state`: Random string for CSRF protection (recommended)

**IMPORTANT**: OSM requires `redirect_uri` to start with `https://`. For local development, use ngrok or localhost.run to create an HTTPS tunnel.

### Step 2: User Logs In

The user sees the OSM login screen and enters their credentials. After successful login, they grant permissions to your application.

### Step 3: Handle Redirect with Authorization Code

OSM redirects back to your `redirect_uri` with the authorization code:

```
https://your-app.com/oauth/callback?code=AUTH_CODE_HERE&state=random_state_string_123
```

**On Error:**
```
https://your-app.com/oauth/callback?error=access_denied&error_description=The+user+denied+access&state=random_state_string_123
```

### Step 4: Exchange Code for Tokens

```http
POST /oauth/token HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code=AUTH_CODE_HERE&client_id=your_client_id&client_secret=your_client_secret&redirect_uri=https://your-app.com/oauth/callback
```

### Response: Access and Refresh Tokens

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "def502004a3c8b...",
  "scope": "section:member:read section:finance:read"
}
```

### Step 5: Use Access Token

```http
GET /ext/members/contact/?action=getListOfMembers&sectionid=12345&termid=67890 HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

## HTTPS Redirect URI Requirement

**OSM Security Policy**: All redirect URIs MUST start with `https://`

### Why HTTPS is Required
- Prevents token interception over insecure connections
- Protects user credentials during OAuth flow
- Industry standard for OAuth2 security (RFC 6749)

### Local Development Setup

Since `http://localhost` is not allowed, use HTTPS tunneling:

#### Option 1: ngrok (Recommended)

```bash
# Install ngrok
brew install ngrok  # macOS
# or download from https://ngrok.com/download

# Start your local callback server
python scripts/cli/oauth_authorization_flow.py

# In another terminal, create HTTPS tunnel
ngrok http 8080

# ngrok output will show:
# Forwarding: https://abc123def456.ngrok.io -> http://localhost:8080
```

**Configuration**:
1. Copy the HTTPS URL from ngrok (e.g., `https://abc123def456.ngrok.io`)
2. Set `REDIRECT_URI = "https://abc123def456.ngrok.io/callback"`
3. Register this URL in your OSM OAuth application settings
4. Run your authorization flow

**Note**: ngrok free tier generates a new URL each time. Paid plans allow custom domains.

#### Option 2: localhost.run (Free Alternative)

```bash
# Create HTTPS tunnel to localhost:8080
ssh -R 80:localhost:8080 localhost.run

# Output will show your HTTPS URL
# Register that URL + /callback as your redirect_uri
```

#### Option 3: Deploy to Cloud (Production)

For production or persistent development:
- **Heroku**: Free tier with automatic HTTPS
- **Railway.app**: Easy deployment with HTTPS
- **Your domain**: Use Let's Encrypt for free SSL certificates
- **Vercel/Netlify**: For client-side OAuth flows

### Production Deployment

For production applications:
1. Deploy your callback handler to a server with HTTPS
2. Register your production URL: `https://yourdomain.com/oauth/callback`
3. Ensure SSL certificate is valid and not self-signed
4. Use environment variables for CLIENT_ID and CLIENT_SECRET

---

## 3. Refresh Token Flow (Both Flows)

When your access token expires, use the refresh token to get a new one:

### Request: Refresh Access Token

```http
POST /oauth/token HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&refresh_token=def502004a3c8b...&client_id=your_client_id&client_secret=your_client_secret
```

### Response: New Tokens

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "abc123new...",
  "scope": "section:member:read section:finance:read"
}
```

---

## Python Examples

### Client Credentials Flow

```python
from osm.auth import OSMAuthClient

# Initialize with client credentials
auth_client = OSMAuthClient()

# Get access token (automatic with configured credentials)
access_token = auth_client.get_access_token(
    scope="section:member:read section:finance:read"
)

print(f"Access Token: {access_token}")
```

### Authorization Code Flow

```python
from osm.requests.oauth import (
    open_authorization_url,
    exchange_code_for_token,
)

# Step 1: Open authorization URL in browser
CLIENT_ID = "your_client_id"
CLIENT_SECRET = "your_client_secret"
REDIRECT_URI = "http://localhost:8080/callback"

auth_url = open_authorization_url(
    client_id=CLIENT_ID,
    redirect_uri=REDIRECT_URI,
    scope="section:member:read section:finance:read",
    state="random_state_123",
    verbose=True,
)

# Step 2: User logs in and authorizes
# Step 3: Your app receives callback with code
# (See scripts/cli/oauth_authorization_flow.py for complete example)

# Step 4: Exchange code for tokens
tokens = exchange_code_for_token(
    code="AUTH_CODE_FROM_CALLBACK",
    client_id=CLIENT_ID,
    client_secret=CLIENT_SECRET,
    redirect_uri=REDIRECT_URI,
    verbose=True,
)

print(f"Access Token: {tokens['access_token']}")
print(f"Refresh Token: {tokens['refresh_token']}")
```

---

## Security Best Practices

### State Parameter
Always use the `state` parameter in authorization requests:
- Generate a random, unique string for each authorization request
- Store it in the user's session
- Verify it matches when receiving the callback
- Prevents CSRF attacks

```python
import secrets

state = secrets.token_urlsafe(32)
# Store state in session
# Verify on callback
```

### Token Storage
- **Never** commit tokens to version control
- Store tokens securely (encrypted database, secure keystore)
- Use environment variables for client credentials
- Implement token rotation (use refresh tokens)
- Clear tokens on logout

### Redirect URI
- **MUST use HTTPS** - OSM requires all redirect URIs to start with `https://`
- **No localhost/HTTP allowed** - Even for development, use HTTPS tunneling:
  - **ngrok**: `ngrok http 8080` → Register `https://abc123.ngrok.io/callback`
  - **localhost.run**: `ssh -R 80:localhost:8080 localhost.run`
- Register all redirect URIs with OSM before use
- Validate redirect_uri matches exactly (including trailing slashes)
- Avoid open redirects

### Scopes
- Request only the scopes you need (principle of least privilege)
- Document why each scope is required
- Let users see what permissions they're granting

---

## Common Error Responses

### Invalid Client Credentials

```json
{
  "error": "invalid_client",
  "error_description": "Client authentication failed"
}
```

**Solution**: Verify your client_id and client_secret

### Invalid Authorization Code

```json
{
  "error": "invalid_grant",
  "error_description": "The authorization code is invalid or has expired"
}
```

**Solution**: Authorization codes expire quickly. Exchange them immediately after receiving.

### Redirect URI Mismatch

```json
{
  "error": "invalid_request",
  "error_description": "The redirect URI does not match"
}
```

**Solution**: Ensure redirect_uri in token request exactly matches the one used in authorization request.

### Access Denied

```
error=access_denied&error_description=The user denied access to your application
```

**Solution**: User declined to authorize. Handle gracefully in your application.

---

## Complete Flow Diagrams

### Client Credentials Flow

```
Application                          OSM API
    |                                   |
    |--POST /oauth/token--------------->|
    |  (client_credentials)             |
    |                                   |
    |<--access_token------------------<|
    |                                   |
    |--API Request (with token)-------->|
    |                                   |
    |<--API Response------------------<|
```

### Authorization Code Flow

```
User Browser          Application            OSM API
     |                     |                     |
     |<-redirect-----------|                     |
     |  to /authorize                            |
     |                                           |
     |--GET /authorize-------------------------->|
     |  (login screen)                           |
     |                                           |
     |<-login page----------------------------<|
     |                                           |
     |--credentials---------------------------->|
     |                                           |
     |<-redirect with code----------------------<|
     |  to redirect_uri                          |
     |                                           |
     |--GET redirect_uri--->|                    |
     |  (with code)         |                    |
     |                      |                    |
     |                      |--POST /oauth/token->|
     |                      |  (authorization_code)|
     |                      |                    |
     |                      |<-access_token------<|
     |                      |                    |
     |<-success page--------|                    |
     |                      |                    |
     |                      |--API Request------->|
     |                      |  (with token)      |
     |                      |                    |
     |                      |<-API Response------<|
```

---

For complete working examples, see:
- Python: `scripts/cli/oauth_authorization_flow.py`
- OpenAPI spec: `api-specs/osm-api.yaml`
