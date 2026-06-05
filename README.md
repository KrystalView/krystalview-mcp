# KrystalView MCP Server

Give your AI agents direct access to website analytics. Query visitor sessions, investigate UX friction, analyze conversion funnels, review campaigns and errors, and get anomaly alerts — all from Claude, Cursor, or any MCP-compatible client.

## Connection Options

### Hosted Remote MCP

Use this when your client supports remote MCP servers or connector-style OAuth.
No local package install is required.

| Field | Value |
|-------|-------|
| Endpoint | `https://krystalview.com/mcp` |
| Transport | `streamable-http` |
| Authentication | OAuth authorization code + PKCE |
| OAuth metadata | `https://krystalview.com/.well-known/oauth-authorization-server` |
| Protected resource metadata | `https://krystalview.com/.well-known/oauth-protected-resource` |

During OAuth, KrystalView asks the signed-in user to choose the site the MCP
client can read. The issued token is read-only and scoped to that site.
The hosted OAuth server supports dynamic client registration, authorization
code + PKCE, resource indicators, refresh-token rotation, and token revocation.

For clients that support custom headers instead of OAuth, the hosted endpoint
also accepts a KrystalView read API key as either:

```http
Authorization: Bearer kv_live_...
```

or:

```http
X-API-Key: kv_live_...
```

### Local stdio MCP Package

Use this when your MCP client runs local stdio servers, such as Claude Desktop,
Claude Code, Cursor, or similar developer tools.

### Install

```bash
pip install krystalview-mcp
```

### Configure

Generate an API key in your [KrystalView console](https://krystalview.com) under **Settings > API Keys**.

#### Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "krystalview": {
      "command": "krystalview-mcp",
      "env": {
        "KRYSTALVIEW_API_KEY": "your-api-key-here"
      }
    }
  }
}
```

#### Claude Code

```bash
claude mcp add krystalview -- krystalview-mcp
# Then set your API key:
export KRYSTALVIEW_API_KEY="your-api-key-here"
```

#### Cursor

Add to your MCP settings:

```json
{
  "krystalview": {
    "command": "krystalview-mcp",
    "env": {
      "KRYSTALVIEW_API_KEY": "your-api-key-here"
    }
  }
}
```

## Available Tools

| Tool | Description |
|------|-------------|
| `get_sessions` | List/search visitor sessions with filters (device, location, friction, rage clicks) |
| `get_session_detail` | Deep dive into a specific session — full timeline, events, navigation path |
| `get_site_stats` | Aggregate performance metrics — sessions, friction, devices, top pages |
| `get_scroll_depth` | Scroll-depth buckets for a specific page path |
| `get_live_visitors` | Currently active visitor count and recent live sessions |
| `get_anomalies` | AI-detected anomalies with explanations (traffic spikes/drops, friction surges) |
| `get_funnels` | List defined conversion funnels |
| `get_funnel_analysis` | Step-by-step funnel conversion rates and drop-off analysis |
| `get_campaign_summary` | UTM campaign attribution summary |
| `get_campaign_sessions` | Visitor sessions from a specific campaign |
| `get_campaign_roas` | Paid campaign spend, conversions, and ROAS where connected |
| `get_errors` | Aggregated client-side browser errors |
| `get_notifications` | Recent KrystalView notifications and insights |

## Example Prompts

Once connected, try asking your AI assistant:

- *"How's my site performing this week?"*
- *"Show me frustrated mobile users from the last 24 hours"*
- *"Why did our traffic drop yesterday?"*
- *"Where are users dropping off in the checkout funnel?"*
- *"Find sessions with rage clicks on the pricing page"*
- *"Which campaigns are driving the most high-friction sessions?"*
- *"Show me unresolved browser errors with sample session IDs"*
- *"Are there any anomalies I should know about?"*

## Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `KRYSTALVIEW_API_KEY` | Yes | — | Your KrystalView API key |
| `KRYSTALVIEW_BASE_URL` | No | `https://krystalview.com/api` | API base URL |
| `KRYSTALVIEW_TIMEOUT` | No | `15` | Request timeout in seconds |

## Rate Limits

API keys have configurable rate limits (default: 60 requests per minute). Rate limit headers are included in every response. If you hit the limit, the server returns a clear error with retry timing.

## Security

- API keys are scoped to a single site — agents can only access data for the site the key was created for
- OAuth tokens issued by the hosted MCP endpoint are read-only and scoped to the selected site
- Hosted OAuth supports refresh-token rotation and token revocation
- Browser-originating MCP/OAuth requests are checked against explicit trusted origins
- Tools are read-only
- All requests use HTTPS
- Keys can be rotated or revoked in the KrystalView console
- The local stdio package stores no data — it proxies directly to the KrystalView API

<!-- mcp-name: io.github.KrystalView/krystalview -->

## License

MIT
