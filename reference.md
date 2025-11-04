# Setup Guide for Send Keep Summary Skill

This guide walks through setting up all required MCP servers for the `send-keep-summary` skill.

## Required MCP Servers

1. **Google Tasks MCP** - For retrieving task lists and tasks
2. **Google Keep MCP** - For retrieving Keep notes
3. **Resend MCP** - For sending formatted HTML emails

---

## 1. Google Tasks MCP Server

### Setup Instructions

1. **Install the server:**
   ```bash
   git clone https://github.com/MajorAaron/tasks-mcp.git
   cd tasks-mcp
   npm install
   npm run build
   ```

2. **Set up Google Cloud credentials:**
   - Go to [Google Cloud Console](https://console.cloud.google.com)
   - Create a new project or select an existing one
   - Enable the **Google Tasks API**
   - Create OAuth 2.0 credentials (Desktop app type)
   - Download the credentials JSON file

3. **Configure Claude Desktop:**

   Edit your `claude_desktop_config.json`:
   ```json
   {
     "mcpServers": {
       "google-tasks": {
         "command": "node",
         "args": ["/ABSOLUTE/PATH/TO/tasks-mcp/dist/index.js"],
         "env": {
           "GOOGLE_APPLICATION_CREDENTIALS": "/path/to/credentials.json"
         }
       }
     }
   }
   ```

---

## 2. Google Keep MCP Server

### Setup Instructions

1. **Install via pipx:**
   ```bash
   pipx install keep-mcp
   ```

2. **Get your Google Master Token:**

   ⚠️ **Warning:** This token has broad access to your Google account. Keep it secure!

   - Go to [Google App Passwords](https://myaccount.google.com/apppasswords)
   - Create a new app password
   - Run this Docker command:
     ```bash
     docker run --rm -it breph/ha-google-home_get-token
     ```
   - Enter your Gmail address and the app password you just created
   - Copy the master token it generates

3. **Configure Claude Desktop:**

   Edit your `claude_desktop_config.json`:
   ```json
   {
     "mcpServers": {
       "keep-mcp": {
         "command": "pipx",
         "args": ["run", "keep-mcp"],
         "env": {
           "GOOGLE_EMAIL": "your-email@gmail.com",
           "GOOGLE_MASTER_TOKEN": "your-master-token-here"
         }
       }
     }
   }
   ```

### Optional: Unsafe Mode

By default, keep-mcp only modifies notes it created (with `keep-mcp` label). To modify all notes:
```json
"env": {
  "GOOGLE_EMAIL": "your-email@gmail.com",
  "GOOGLE_MASTER_TOKEN": "your-master-token-here",
  "UNSAFE_MODE": "true"
}
```

---

## 3. Resend MCP Server

### Setup Instructions

1. **Create a Resend account:**
   - Sign up at [resend.com](https://resend.com)
   - Create an API key from the dashboard
   - (Optional) Verify your domain to send emails to any address

2. **Install the server:**
   ```bash
   git clone https://github.com/resend/mcp-send-email.git
   cd mcp-send-email
   npm install
   npm run build
   ```

3. **Configure Claude Desktop:**

   Edit your `claude_desktop_config.json`:
   ```json
   {
     "mcpServers": {
       "resend": {
         "command": "node",
         "args": ["/ABSOLUTE/PATH/TO/mcp-send-email/dist/index.js"],
         "env": {
           "RESEND_API_KEY": "re_your_api_key_here"
         }
       }
     }
   }
   ```

---

## Complete Configuration Example

Your final `claude_desktop_config.json` should look like this:

```json
{
  "mcpServers": {
    "google-tasks": {
      "command": "node",
      "args": ["/ABSOLUTE/PATH/TO/tasks-mcp/dist/index.js"],
      "env": {
        "GOOGLE_APPLICATION_CREDENTIALS": "/path/to/credentials.json"
      }
    },
    "keep-mcp": {
      "command": "pipx",
      "args": ["run", "keep-mcp"],
      "env": {
        "GOOGLE_EMAIL": "your-email@gmail.com",
        "GOOGLE_MASTER_TOKEN": "aas_et/your-token-here"
      }
    },
    "resend": {
      "command": "node",
      "args": ["/ABSOLUTE/PATH/TO/mcp-send-email/dist/index.js"],
      "env": {
        "RESEND_API_KEY": "re_your_api_key_here"
      }
    }
  }
}
```

---

## Verification

After setting up all three servers:

1. **Restart Claude Desktop** completely
2. **Run `/mcp`** to verify all servers are connected
3. **Test each server:**
   - Ask Claude to "list my Google task lists"
   - Ask Claude to "find my Keep notes"
   - Ask Claude to "what email address can I send from via Resend?"

---

## Troubleshooting

### Google Tasks not working
- Verify Google Tasks API is enabled in Cloud Console
- Check that OAuth credentials are type "Desktop app"
- Ensure absolute paths in config are correct

### Google Keep not working
- Verify master token is correct
- Check that pipx is installed: `pipx --version`
- Try reinstalling: `pipx reinstall keep-mcp`

### Resend not working
- Verify API key is valid in Resend dashboard
- Check that sender email is verified in Resend
- For sending to external addresses, verify your domain

### General MCP issues
- Run `claude mcp` to check server status
- Check Claude Desktop logs (Help → View Logs)
- Ensure all paths use absolute paths, not relative

---

## Security Notes

- ⚠️ **Never commit** your `claude_desktop_config.json` with credentials
- ⚠️ **Google Master Token** has broad account access - store securely
- ⚠️ Keep API keys in environment variables or secure vaults
- ⚠️ Consider using separate Google accounts for automation

---

## Using the Skill

Once all servers are configured, you can invoke the skill which will:

1. **Collect Google Tasks data:**
   - Retrieve all task lists
   - Filter completed tasks from the last 7 days
   - Organize incomplete tasks as backlog

2. **Collect Google Keep notes:**
   - Retrieve all notes for inclusion in the email

3. **Send formatted email:**
   - Generate a bold brutalist design HTML email
   - Include gradient header, task sections, and Keep notes
   - Send via Resend to your specified email address

The email will feature a vibrant, brutalist design with colored section headers, bold black borders, and organized sections for recently completed tasks, current backlog, and Keep notes.
