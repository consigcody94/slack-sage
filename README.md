# Slack Sage

A production-ready Model Context Protocol (MCP) server for team communication intelligence with Slack.

## Features

- **search_messages** - Search messages across all channels
- **get_thread** - Retrieve all messages in a thread
- **post_message** - Post messages to channels or threads
- **get_channel_history** - Get recent messages from a channel
- **create_reminder** - Create reminders for users

## Installation

```bash
npm install
npm run build
```

## Configuration

### Slack Setup

1. Create a Slack App at [api.slack.com/apps](https://api.slack.com/apps)
2. Add Bot Token Scopes:
   - `channels:history` - View messages in public channels
   - `channels:read` - View basic channel info
   - `chat:write` - Send messages
   - `search:read` - Search workspace content
   - `reminders:write` - Create reminders
   - `groups:history` - View messages in private channels
   - `im:history` - View messages in direct messages
3. Install the app to your workspace
4. Copy the Bot User OAuth Token (starts with `xoxb-`)

## Usage

### Claude Desktop Configuration

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "slack-sage": {
      "command": "node",
      "args": ["/path/to/slack-sage/dist/index.js"]
    }
  }
}
```

### Tool Examples

#### Search Messages

```javascript
{
  "query": "bug in:general from:@alice after:2024-01-01",
  "count": 10,
  "token": "xoxb-your-token"
}
```

Search syntax examples:
- `in:#general` - Search in a specific channel
- `from:@username` - Messages from a user
- `after:2024-01-01` - After a date
- `has:link` - Contains links
- `has:attachment` - Contains attachments

#### Get Thread

```javascript
{
  "channel": "C01234567",
  "threadTs": "1234567890.123456",
  "token": "xoxb-your-token"
}
```

#### Post Message

```javascript
{
  "channel": "C01234567",
  "text": "Hello from Slack Sage! :wave:",
  "token": "xoxb-your-token"
}
```

Post to thread:
```javascript
{
  "channel": "C01234567",
  "text": "Reply in thread",
  "threadTs": "1234567890.123456",
  "token": "xoxb-your-token"
}
```

#### Get Channel History

```javascript
{
  "channel": "C01234567",
  "limit": 50,
  "token": "xoxb-your-token"
}
```

With timestamp filter:
```javascript
{
  "channel": "C01234567",
  "limit": 100,
  "oldest": "1234567890.123456",
  "token": "xoxb-your-token"
}
```

#### Create Reminder

```javascript
{
  "text": "Review pull requests",
  "time": "tomorrow at 9am",
  "token": "xoxb-your-token"
}
```

Natural language time examples:
- `in 20 minutes`
- `tomorrow at 9am`
- `next Monday at 2pm`
- `1234567890` (Unix timestamp)

## Message Formatting

Slack supports markdown-like formatting:

- `*bold*` - Bold text
- `_italic_` - Italic text
- `~strikethrough~` - Strikethrough
- `` `code` `` - Inline code
- ` ```code block``` ` - Code block
- `<@U01234567>` - Mention user
- `<#C01234567>` - Mention channel
- `<https://example.com|Link text>` - Hyperlink

## Development

```bash
# Install dependencies
npm install

# Build
npm run build

# Run
npm start
```

## Requirements

- Node.js 18+
- Slack workspace with app installed
- Bot User OAuth Token

## Security Notes

- Never commit tokens to version control
- Use environment variables or secure vaults
- Rotate tokens regularly
- Use minimum required scopes

## License

MIT

## Author

consigcody94
