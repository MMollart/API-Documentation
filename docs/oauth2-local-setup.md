# OAuth2 Local Development Setup Guide

Quick guide to set up OAuth2 authorization code flow for local development.

## Challenge: HTTPS Requirement

OSM requires **all redirect URIs to start with `https://`**. This means `http://localhost` won't work, even for development.

**Solution**: Use HTTPS tunneling to expose your local server with an HTTPS URL.

---

## Step-by-Step Setup (Using ngrok)

### 1. Install ngrok

```bash
# macOS
brew install ngrok

# Or download from: https://ngrok.com/download
```

### 2. Get OSM OAuth Credentials

Contact OSM or check their developer portal to obtain:
- `CLIENT_ID`
- `CLIENT_SECRET`

### 3. Start Local Callback Server

```bash
cd /path/to/osm
python scripts/cli/oauth_authorization_flow.py
```

The server will start on `http://localhost:8080`

### 4. Create HTTPS Tunnel (New Terminal)

```bash
ngrok http 8080
```

You'll see output like:
```
Session Status: online
Forwarding: https://abc123def456.ngrok.io -> http://localhost:8080
```

**Copy the HTTPS URL**: `https://abc123def456.ngrok.io`

### 5. Register Redirect URI with OSM

In your OSM OAuth application settings:
- Add redirect URI: `https://abc123def456.ngrok.io/callback`
- Save the configuration

### 6. Update Configuration

Edit `scripts/cli/oauth_authorization_flow.py`:

```python
CLIENT_ID = "your_actual_client_id"
CLIENT_SECRET = "your_actual_client_secret"
REDIRECT_URI = "https://abc123def456.ngrok.io/callback"  # Your ngrok URL
SCOPES = "section:member:read section:finance:read"
```

### 7. Run Authorization Flow

```bash
python scripts/cli/oauth_authorization_flow.py
```

**What happens:**
1. Browser opens OSM login page
2. You log in with OSM credentials
3. You grant permissions to your application
4. OSM redirects to your ngrok URL → tunnels to localhost:8080
5. Your script receives the authorization code
6. Script exchanges code for access token
7. You're authenticated! 🎉

---

## Troubleshooting

### "Redirect URI mismatch"
- Check that the redirect URI in your code **exactly matches** what's registered in OSM
- Include or exclude trailing slashes consistently
- Verify you're using the ngrok HTTPS URL, not localhost

### "ngrok URL changed"
- ngrok free tier generates a new URL each restart
- Either: Update your configuration each time
- Or: Upgrade to ngrok paid plan for a persistent URL
- Or: Use localhost.run (also generates new URLs)

### "Invalid client credentials"
- Verify CLIENT_ID and CLIENT_SECRET are correct
- Check for extra spaces or line breaks in credentials
- Ensure credentials are for the correct OSM environment

### "Authorization code expired"
- Authorization codes expire quickly (usually 60 seconds)
- Ensure your callback server is running when you authorize
- Check for network/firewall issues blocking the callback

---

## Alternative: localhost.run (Free)

If you don't want to install ngrok:

```bash
# Start your local server first
python scripts/cli/oauth_authorization_flow.py

# In another terminal
ssh -R 80:localhost:8080 localhost.run
```

You'll get an HTTPS URL. Use it the same way as ngrok.

---

## Production Deployment

For production, deploy your callback handler to a real server:

### Heroku (Free Tier)
```bash
# Create Heroku app
heroku create your-app-name

# Deploy
git push heroku main

# Your redirect URI: https://your-app-name.herokuapp.com/oauth/callback
```

### Railway.app
1. Connect your GitHub repo
2. Deploy automatically
3. Get HTTPS URL: `https://your-app.railway.app`

### Your Own Domain
1. Deploy to any server (AWS, DigitalOcean, etc.)
2. Set up SSL with Let's Encrypt (free)
3. Use your domain: `https://yourdomain.com/oauth/callback`

---

## Security Notes

- **Never commit CLIENT_SECRET** to version control
- Use environment variables for credentials
- Validate the `state` parameter to prevent CSRF attacks
- Store tokens securely (encrypted database, not plain text)
- Use HTTPS everywhere (enforced by OSM)
- Implement token refresh before expiration

---

## Next Steps

Once authorized:
1. Store the access token securely
2. Use it for API requests: `Authorization: Bearer {access_token}`
3. Refresh before expiration using the refresh token
4. Handle token expiration gracefully in your app

See [OAuth2 Authentication Guide](oauth2-authentication.md) for complete documentation.
