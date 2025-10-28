# Getting Started with OSM API

This guide will help you get started with the Online Scout Manager API.

## Prerequisites

- An OSM account with API access
- OAuth client credentials (client_id and client_secret)
- Basic understanding of REST APIs and OAuth 2.0

## Step 1: Obtain OAuth Credentials

Contact your OSM administrator or apply for API access through the OSM platform to receive:
- `client_id`
- `client_secret`

## Step 2: Get an Access Token

All API requests require authentication. First, obtain an access token:

```bash
curl -X POST "https://www.onlinescoutmanager.co.uk/oauth/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET" \
  -d "scope=section:member:read section:finance:read"
```

**Response:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "def502004a3c8b...",
  "scope": "section:member:read section:finance:read"
}
```

**Important**: Access tokens expire after the time specified in `expires_in` (seconds). Store the `refresh_token` to obtain new access tokens without re-authenticating.

## Step 3: Make Your First API Call

Use the access token to fetch members:

```bash
curl -X GET "https://www.onlinescoutmanager.co.uk/ext/members/contact/?action=getListOfMembers&sectionid=75013&termid=841318" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

## Step 4: Handle Rate Limiting

The OSM API implements rate limiting. If you receive a `429` response:

```json
{
  "error": "rate_limit_exceeded",
  "message": "Rate limit exceeded"
}
```

Check the `Retry-After` header (in seconds) and wait before retrying:

```python
import requests
import time

response = requests.get(url, headers=headers)
if response.status_code == 429:
    retry_after = int(response.headers.get('Retry-After', 60))
    print(f"Rate limited. Waiting {retry_after} seconds...")
    time.sleep(retry_after)
    response = requests.get(url, headers=headers)
```

## OAuth Scopes

Request only the scopes you need:

| Scope | Required For |
|-------|-------------|
| `section:member:read` | Reading member information |
| `section:member:write` | Updating member data, custom fields |
| `section:finance:read` | Viewing invoices and payments |
| `section:finance:write` | Creating invoices, recording payments |
| `section:event:read` | Accessing event details and attendees |
| `section:badge:read` | Viewing badge records and progress |
| `section:admin:write` | Creating/deleting patrols |
| `section:attendance:write` | Managing patrol assignments, attendance |

## Common Patterns

### Refreshing Access Tokens

When your access token expires, use the refresh token:

```bash
curl -X POST "https://www.onlinescoutmanager.co.uk/oauth/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=refresh_token" \
  -d "refresh_token=YOUR_REFRESH_TOKEN" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET"
```

### Bulk Member Data Retrieval

For efficient bulk data fetching, use the grid endpoint:

```bash
curl -X POST "https://www.onlinescoutmanager.co.uk/ext/members/contact/grid/?action=getMembers" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "section_id=75013&term_id=841318"
```

This is more efficient than the standard GET endpoint for large member lists.

### Error Handling

Always handle common HTTP error codes:

- **401 Unauthorized**: Access token is invalid or expired → Refresh token
- **403 Forbidden**: Insufficient permissions → Check required OAuth scopes
- **404 Not Found**: Resource doesn't exist → Verify IDs
- **429 Rate Limit**: Too many requests → Wait and retry

## Using the Python Client

For Python projects, use the official OSM API client:

```bash
pip install -e git+https://github.com/MMollart/osm.git#egg=osm-api-client
```

```python
from osm.auth import OSMAuthClient
from osm.requests.members import get_members

# Initialize auth client
auth_client = OSMAuthClient(
    client_id="YOUR_CLIENT_ID",
    client_secret="YOUR_CLIENT_SECRET"
)

# Get access token
token = auth_client.get_access_token()

# Fetch members
members = get_members(
    section_id=75013,
    term_id=841318,
    verbose=True
)

print(f"Found {len(members['items'])} members")
```

## Best Practices

1. **Cache tokens**: Store access tokens securely and reuse until expiration
2. **Implement retry logic**: Handle rate limits and transient errors gracefully
3. **Use bulk endpoints**: Prefer grid/batch endpoints for large datasets
4. **Request minimal scopes**: Only request OAuth scopes you actually need
5. **Log API calls**: Track requests for debugging and monitoring
6. **Validate responses**: Check `status` field in responses before processing data

## Next Steps

- Review the [complete API specification](../api-specs/osm-api.yaml)
- Explore [example requests and responses](../examples/osm-api-examples.md)
- Check the [Python client documentation](https://github.com/MMollart/osm)
- Join the community and share your use cases

## Troubleshooting

### Issue: "Invalid client credentials"

**Solution**: Verify your `client_id` and `client_secret` are correct and active.

### Issue: "Insufficient scope"

**Solution**: Request additional OAuth scopes when obtaining the access token.

### Issue: "Rate limit exceeded repeatedly"

**Solution**: Implement exponential backoff and consider caching API responses.

### Issue: "Empty response data"

**Solution**: Check that the `section_id` and `term_id` are valid and you have access to that section.

## Support

- **GitHub Issues**: [Report bugs or request features](https://github.com/MMollart/API-Documentation/issues)
- **API Specification**: [View OpenAPI spec](../api-specs/osm-api.yaml)
- **Python Client**: [OSM API Client](https://github.com/MMollart/osm)

---

*Last updated: October 28, 2025*
