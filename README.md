# Slack Sage

A Model Context Protocol (MCP) server for Slack team communication. Search messages, read threads, post updates, browse channel history, and create reminders.

## Overview

Slack Sage brings your Slack workspace into your AI conversations. Search across channels, catch up on threads, post messages, and set reminders without switching context.

### Why Use Slack Sage?

**Traditional workflow:**
- Open Slack app
- Navigate between channels
- Scroll through message history
- Manually search for information

**With Slack Sage:**
```
"Search for messages about the API outage from last week"
"What's the latest in the #engineering thread about deployments?"
"Post an update to #general about the release"
"Remind me tomorrow at 9am to review PRs"
```

## Features

- **Message Search** - Full-text search across all channels with Slack syntax
- **Thread Reading** - Get complete thread conversations
- **Message Posting** - Post to channels or reply in threads
- **Channel History** - Browse recent messages with timestamp filters
- **Reminders** - Create reminders with natural language timing

## Installation

```bash
# Clone the repository
git clone https://github.com/consigcody94/slack-sage.git
cd slack-sage

# Install dependencies
npm install

# Build the project
npm run build
```

## Configuration

### Creating a Slack App

1. Go to [api.slack.com/apps](https://api.slack.com/apps)
2. Click "Create New App" → "From scratch"
3. Name your app and select your workspace
4. Click "Create App"

### Required Bot Token Scopes

Go to "OAuth & Permissions" and add these Bot Token Scopes:

| Scope | Purpose |
|-------|---------|
| `channels:history` | Read messages in public channels |
| `channels:read` | View basic channel info |
| `chat:write` | Post messages |
| `search:read` | Search workspace content |
| `reminders:write` | Create reminders |
| `groups:history` | Read messages in private channels (optional) |
| `im:history` | Read direct messages (optional) |
| `mpim:history` | Read group DMs (optional) |

### Installing to Workspace

1. Go to "Install App" in your app settings
2. Click "Install to Workspace"
3. Authorize the requested permissions
4. Copy the "Bot User OAuth Token" (starts with `xoxb-`)

### Claude Desktop Integration

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "slack-sage": {
      "command": "node",
      "args": ["/absolute/path/to/slack-sage/dist/index.js"]
    }
  }
}
```

**Config file locations:**
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- Linux: `~/.config/Claude/claude_desktop_config.json`

## Tools Reference

### search_messages

Search for messages across all accessible channels.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Search query (supports Slack search syntax) |
| `count` | number | No | Number of results (default: 20) |
| `token` | string | Yes | Slack bot token |

**Example - Basic search:**

```json
{
  "query": "deployment failed",
  "count": 10,
  "token": "xoxb-your-token"
}
```

**Example - Advanced search:**

```json
{
  "query": "bug in:#engineering from:@alice after:2024-01-01",
  "count": 20,
  "token": "xoxb-your-token"
}
```

**Search Syntax:**

| Modifier | Example | Description |
|----------|---------|-------------|
| `in:#channel` | `in:#general` | Search in specific channel |
| `from:@user` | `from:@alice` | Messages from user |
| `after:date` | `after:2024-01-01` | After date |
| `before:date` | `before:2024-01-31` | Before date |
| `during:month` | `during:january` | During time period |
| `has:link` | `has:link` | Contains links |
| `has:attachment` | `has:attachment` | Contains files |
| `has:reaction` | `has:reaction` | Has reactions |
| `is:thread` | `is:thread` | Thread messages |
| `"exact phrase"` | `"release notes"` | Exact phrase match |

### get_thread

Get all messages in a thread.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `channel` | string | Yes | Channel ID |
| `threadTs` | string | Yes | Thread timestamp (parent message) |
| `token` | string | Yes | Slack bot token |

**Example:**

```json
{
  "channel": "C01234567",
  "threadTs": "1234567890.123456",
  "token": "xoxb-your-token"
}
```

**Response includes:**
- All replies in chronological order
- User info for each message
- Timestamps and reactions
- File attachments

### post_message

Post a message to a channel or thread.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `channel` | string | Yes | Channel ID or name |
| `text` | string | Yes | Message text (supports mrkdwn) |
| `threadTs` | string | No | Thread timestamp (to reply in thread) |
| `token` | string | Yes | Slack bot token |

**Example - Post to channel:**

```json
{
  "channel": "C01234567",
  "text": "Deployment complete! :rocket:",
  "token": "xoxb-your-token"
}
```

**Example - Reply in thread:**

```json
{
  "channel": "C01234567",
  "text": "Following up on this - the fix has been merged.",
  "threadTs": "1234567890.123456",
  "token": "xoxb-your-token"
}
```

**Message Formatting (mrkdwn):**

| Syntax | Result |
|--------|--------|
| `*bold*` | **bold** |
| `_italic_` | *italic* |
| `~strikethrough~` | ~~strikethrough~~ |
| `` `code` `` | `code` |
| ` ```code block``` ` | Code block |
| `<@U01234567>` | @mention user |
| `<#C01234567>` | #mention channel |
| `<https://url\|text>` | [text](url) |
| `:emoji:` | Emoji |

### get_channel_history

Get recent messages from a channel.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `channel` | string | Yes | Channel ID |
| `limit` | number | No | Number of messages (default: 100) |
| `oldest` | string | No | Get messages after this timestamp |
| `token` | string | Yes | Slack bot token |

**Example - Recent messages:**

```json
{
  "channel": "C01234567",
  "limit": 50,
  "token": "xoxb-your-token"
}
```

**Example - Messages after timestamp:**

```json
{
  "channel": "C01234567",
  "limit": 100,
  "oldest": "1234567890.123456",
  "token": "xoxb-your-token"
}
```

### create_reminder

Create a reminder for yourself.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `text` | string | Yes | Reminder text |
| `time` | string | Yes | When to remind (timestamp or natural language) |
| `token` | string | Yes | Slack bot token |

**Example - Natural language:**

```json
{
  "text": "Review pull requests",
  "time": "tomorrow at 9am",
  "token": "xoxb-your-token"
}
```

**Example - Relative time:**

```json
{
  "text": "Check on deployment",
  "time": "in 30 minutes",
  "token": "xoxb-your-token"
}
```

**Time formats:**
- `in 20 minutes`
- `in 2 hours`
- `tomorrow at 9am`
- `next Monday at 2pm`
- `January 15 at 10am`
- `1234567890` (Unix timestamp)

## Finding IDs

### Channel ID
1. Open Slack in browser
2. Go to the channel
3. Channel ID is in the URL: `slack.com/archives/C01234567`
4. Or right-click channel → "Copy link"

### User ID
1. Click on user's profile
2. Click "..." menu → "Copy member ID"
3. Or use `<@username>` format in searches

### Thread Timestamp
1. Hover over message → "..." menu → "Copy link"
2. URL contains: `...?thread_ts=1234567890.123456`
3. Or get from `get_channel_history` response

## Workflow Examples

### Morning Catch-up

1. **Check for mentions:**
   ```
   search_messages with query: "from:@me has:reaction", token: "..."
   ```

2. **Read unread threads:**
   ```
   get_thread with channel: "C123", threadTs: "...", token: "..."
   ```

3. **Post standup update:**
   ```
   post_message with channel: "#standup", text: "...", token: "..."
   ```

### Incident Response

1. **Search for related issues:**
   ```
   search_messages with query: "outage in:#incidents after:yesterday", token: "..."
   ```

2. **Get channel history:**
   ```
   get_channel_history with channel: "C-incidents", limit: 100, token: "..."
   ```

3. **Post status update:**
   ```
   post_message with channel: "#incidents", text: "Status update: ...", token: "..."
   ```

### Task Management

1. **Set reminder:**
   ```
   create_reminder with text: "Deploy to production", time: "today at 5pm", token: "..."
   ```

2. **Search for action items:**
   ```
   search_messages with query: "action item from:@me", token: "..."
   ```

## Requirements

- Node.js 18 or higher
- Slack workspace with admin access
- Bot User OAuth Token

## Troubleshooting

### "missing_scope" error

Add the required scope in your Slack app's "OAuth & Permissions" settings, then reinstall the app.

### "channel_not_found"

1. Verify the channel ID is correct
2. Ensure the bot is invited to the channel (`/invite @YourBot`)
3. For private channels, add `groups:history` scope

### Search returns no results

1. Ensure `search:read` scope is added
2. Try broader search terms
3. Check date range filters

### Can't post to channel

1. Verify `chat:write` scope
2. Invite the bot to the channel
3. Check channel permissions

## Security Notes

- Never commit tokens to version control
- Bot tokens have workspace-wide access
- Use minimum required scopes
- Rotate tokens periodically
- Consider separate tokens for different environments

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Author

consigcody94
