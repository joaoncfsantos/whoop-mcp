# Whoop MCP Server — Railway Deployment

## 1. Create Whoop Developer App

1. Go to https://developer.whoop.com/
2. Sign in with your Whoop account
3. Create a new application
4. Set the **Redirect URI** to: `https://YOUR_RAILWAY_URL/callback`
   (You'll update this after the first deploy gives you the Railway URL)
5. Copy the **Client ID** and **Client Secret**

## 2. Deploy to Railway

```bash
# From this directory:
railway login
railway init          # Create new project, name it "whoop-mcp"
railway up            # Deploy
```

After first deploy, grab your Railway URL from the dashboard (e.g. `https://whoop-mcp-production.up.railway.app`).

## 3. Set Environment Variables in Railway

In Railway dashboard → your service → Variables:

```
WHOOP_CLIENT_ID=<from step 1>
WHOOP_CLIENT_SECRET=<from step 1>
WHOOP_REDIRECT_URI=https://YOUR_RAILWAY_URL/callback
MCP_MODE=http
PORT=3000
```

Railway auto-assigns PORT via `${{PORT}}` — you can use Railway's port variable or hardcode 3000.

## 4. Update Whoop Developer App

Go back to https://developer.whoop.com/ and update the Redirect URI to match your actual Railway URL:
```
https://whoop-mcp-production.up.railway.app/callback
```

## 5. Authorize Your Whoop Account

Call the `get_auth_url` tool via MCP, or visit:
```
https://YOUR_RAILWAY_URL/health
```
to confirm the server is running, then trigger OAuth via the MCP client.

## 6. Add to Claude.ai

The MCP URL to paste into Claude.ai → Settings → Connectors → Add custom connector:
```
https://YOUR_RAILWAY_URL/mcp
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

- **SQLite storage**: The server uses SQLite for caching data. Railway's filesystem is ephemeral — data resets on redeploy. This means you'll need to re-authorize and re-sync after each deploy. For race week this is fine.
- **Token refresh**: OAuth tokens are stored in SQLite and auto-refresh. If you redeploy, you'll need to re-authorize via `get_auth_url`.
- **Data sync**: The server auto-syncs on each data request if data is >1 hour old. Use `sync_data` for manual sync.
