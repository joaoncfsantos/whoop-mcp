# Whoop MCP Server — Render Deployment

## 1. Create Whoop Developer App

1. Go to https://developer.whoop.com/
2. Sign in with your Whoop account
3. Create a new application
4. Set the **Redirect URI** to: `https://whoop-mcp.onrender.com/callback`
   (You'll update this after the first deploy gives you the actual Render URL)
5. Copy the **Client ID** and **Client Secret**

## 2. Deploy to Render

### Option A: Blueprint (one-click)
1. Push this repo to GitHub
2. Go to https://dashboard.render.com/blueprints
3. Click "New Blueprint Instance" → connect your GitHub repo
4. Render reads `render.yaml` and creates the service
5. Fill in the env vars when prompted

### Option B: Manual
1. Go to https://dashboard.render.com/
2. New → Web Service → Connect your GitHub repo
3. Set:
   - **Name**: `whoop-mcp`
   - **Runtime**: Docker
   - **Plan**: Free
4. Add environment variables (see below)

## 3. Set Environment Variables

```
WHOOP_CLIENT_ID=<from step 1>
WHOOP_CLIENT_SECRET=<from step 1>
WHOOP_REDIRECT_URI=https://whoop-mcp.onrender.com/callback
MCP_MODE=http
PORT=3000
```

## 4. Update Whoop Developer App

Go back to https://developer.whoop.com/ and update the Redirect URI to match your actual Render URL:
```
https://whoop-mcp.onrender.com/callback
```

## 5. Authorize Your Whoop Account

Visit `https://whoop-mcp.onrender.com/health` to confirm the server is running, then use the `get_auth_url` tool via MCP to start the OAuth flow.

## 6. Add to Claude.ai

The MCP URL to paste into Claude.ai → Settings → Connectors → Add custom connector:
```
https://whoop-mcp.onrender.com/mcp
```

## Available Tools

| Tool | Description |
|------|-------------|
| `get_today` | Today's recovery, sleep, strain summary |
| `get_recovery_trends` | Recovery/HRV/RHR trends (up to 90 days) |
| `get_sleep_analysis` | Sleep duration, stages, efficiency |
| `get_strain_history` | Daily strain and calorie data |
| `sync_data` | Manual data sync from Whoop |
| `get_auth_url` | Get OAuth authorization link |

## Notes

- **Free tier cold starts**: Render free services spin down after 15 min idle. First request after idle takes ~30-60 seconds. Subsequent requests are fast.
- **SQLite storage**: Ephemeral on Render free tier — data resets on redeploy. Re-authorize via `get_auth_url` after each deploy.
- **Token refresh**: OAuth tokens auto-refresh. If the service restarts, re-authorize.
- **Data sync**: Auto-syncs on each data request if data is >1 hour old. Use `sync_data` for manual sync.
