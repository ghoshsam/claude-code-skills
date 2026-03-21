# Push to Slack

Push files or messages to Slack channels.

## Configuration

Slack credentials are stored in `.claude/slack-config.json`:

```json
{
  "bot_token": "xoxb-...",
  "default_channel": "C0A5M83LU48"
}
```

## Usage

### Push a File

```
/push-to-slack path/to/file.md
/push-to-slack path/to/report.pdf "Optional comment about this file"
```

### Push a Message

```
/push-to-slack --message "Your message here"
/push-to-slack -m "Quick update: deployment complete"
```

### Push to a Specific Channel

```
/push-to-slack path/to/file.md --channel C0XXXXXXXX
/push-to-slack -m "Alert!" --channel C0XXXXXXXX
```

---

## Instructions for Agent

When the user invokes this command, follow these steps:

### Step 1: Load Configuration

Read the Slack config from `.claude/slack-config.json`. If it doesn't exist, ask the user for:
- Bot Token (starts with `xoxb-`)
- Default Channel ID (starts with `C`)

Then create the config file.

### Step 2: Parse the Input

Determine if the user wants to send:
- **A file**: Input is a file path (check if file exists)
- **A message**: Input includes `--message` or `-m` flag

### Step 3: Execute

#### For Files:

Use the Slack file upload API (new method):

1. Get upload URL:
```bash
FILE_SIZE=$(stat -f%z "FILE_PATH")
curl -s -X POST "https://slack.com/api/files.getUploadURLExternal" \
  -H "Authorization: Bearer BOT_TOKEN" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "filename=FILENAME&length=$FILE_SIZE"
```

2. Upload file to the returned URL:
```bash
curl -s -X POST "UPLOAD_URL" -F file=@"FILE_PATH"
```

3. Complete the upload:
```bash
curl -s -X POST "https://slack.com/api/files.completeUploadExternal" \
  -H "Authorization: Bearer BOT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"files":[{"id":"FILE_ID","title":"FILE_TITLE"}],"channel_id":"CHANNEL_ID","initial_comment":"COMMENT"}'
```

#### For Messages:

Use the chat.postMessage API:
```bash
curl -s -X POST "https://slack.com/api/chat.postMessage" \
  -H "Authorization: Bearer BOT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"channel":"CHANNEL_ID","text":"MESSAGE"}'
```

### Step 4: Confirm

Report success with the Slack permalink, or report the error if it failed.

---

## Examples

| Input | Action |
|-------|--------|
| `/push-to-slack Content/audit.md` | Upload audit.md to default channel |
| `/push-to-slack report.pdf "Q4 results"` | Upload with comment |
| `/push-to-slack -m "Build passed"` | Post message to default channel |
| `/push-to-slack -m "Alert" --channel C123` | Post to specific channel |

---

## Troubleshooting

| Error | Fix |
|-------|-----|
| `invalid_auth` | Bot token is wrong or expired |
| `channel_not_found` | Bot not invited to channel - run `/invite @BotName` |
| `not_in_channel` | Bot needs to be added to the channel |
| `file_not_found` | Check the file path is correct |

---

## Quick Reference

**Required Slack Scopes:**
- `files:write` - Upload files
- `chat:write` - Post messages

**Config Location:** `.claude/slack-config.json`

**Default Channel:** `#claude-reviews` (C0A5M83LU48)
