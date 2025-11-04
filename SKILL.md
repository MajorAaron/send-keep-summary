---
name: send-keep-summary
description: Generates and sends a formatted HTML email summary containing Google Tasks (completed tasks from last 7 days and current backlog) and Google Keep notes with bold brutalist design. Use this skill when the user wants to send their weekly task summary or productivity digest via email.
allowed-tools: mcp__google-tasks__list_task_lists, mcp__google-tasks__list_tasks, mcp__keep-mcp__find, mcp__resend__send-email, AskUserQuestion
---

# Send Keep Summary Skill

This skill collects data from Google Tasks and Google Keep, then sends a beautifully formatted HTML email with a bold brutalist design.

## Data Collection Process

### Google Tasks

1. Use `mcp__google-tasks__list_task_lists` to get all task lists
2. For each task list:
   - Use `mcp__google-tasks__list_tasks` with `show_completed: true` to get all tasks
   - Calculate the date 7 days ago from today
   - Filter completed tasks: only include tasks with `status: "completed"` and `completed` date within the last 7 days
   - Separate incomplete tasks (status: "needsAction") for the backlog section
3. Organize data:
   - **Recently Completed Tasks**: Group by task list name, show completed tasks from last 7 days
   - **Current Backlog**: Group by task list name, show all incomplete tasks

### Google Keep Notes

1. Use `mcp__keep-mcp__find` to retrieve all notes

### Send Email

1. Use `mcp__resend__send-email` to send the formatted HTML email
2. Ask user for sender email (`from`) and recipient email (`to`) if not already known

## Email Structure (in order)

1. **Gradient Header** - Main title
2. **Recently Completed Tasks Section** - Tasks completed in last 7 days, grouped by task list
3. **Current Backlog Section** - All incomplete tasks, grouped by task list
4. **Google Keep Notes Sections** - Keep notes content

## HTML/CSS Guidelines

### Requirements

- ✅ Use inline styles only
- ✅ Use proper email DOCTYPE: `<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN">`
- ✅ Fixed width: 650px main container
- ✅ 3-column video grid using table cells with 33% width
- ✅ Unicode play button (▶) in text, NOT CSS pseudo-elements
- ✅ Favicons using Google's API: `https://www.google.com/s2/favicons?domain=DOMAIN&sz=64`

### Restrictions

- ❌ NO CSS Grid or Flexbox
- ❌ NO pseudo-elements (::before, ::after)
- ❌ NO transform properties
- ❌ NO advanced positioning

## Design Style

**Bold Brutalist Design** with vibrant colors:

- **Gradient header**: `linear-gradient(135deg, #FF6B6B 0%, #FFD93D 50%, #6BCF7F 100%)`
- **Black borders**: 4px solid #000
- **Section alternating**: White (#fff) and light gray (#f5f5f5)
- **Colored underlines**: Each section header gets unique color (FF6B6B, 4ECDC4, FFD93D, 95E1D3, F38181, AA96DA, FCBAD3)
- **Link cards**: White background, 4px black border, favicon + text
- **Video cards**: Border 4px solid black, thumbnail image, title bar with ▶ symbol
- **Stats section**: Black background (#000), yellow text (#FFD93D)
- **Task cards**: White background, 4px black border, title + metadata + notes

## Task Card Structure

```html
<!-- For each task in a list -->
<div style="background-color: #ffffff; border: 4px solid #000000; padding: 15px; margin-bottom: 10px;">
    <div style="font-weight: 700; font-size: 14px; color: #000000; margin-bottom: 5px;">
        Task Title
    </div>
    <div style="font-size: 12px; color: #666666; margin-bottom: 5px;">
        Due: Date (if applicable) | Completed: Date (for completed tasks)
    </div>
    <div style="font-size: 12px; color: #333333;">
        Task notes (if any)
    </div>
</div>
```

## Video Thumbnail Structure

```html
<table cellpadding="0" cellspacing="0" border="0" style="border: 4px solid #000000; background-color: #ffffff; width: 100%;">
    <tr>
        <td>
            <img src="https://img.youtube.com/vi/VIDEO_ID/hqdefault.jpg" width="100%" height="auto" alt="Video Title" style="display: block;" />
        </td>
    </tr>
    <tr>
        <td style="border-top: 4px solid #000000; padding: 10px; font-weight: 700; font-size: 12px; color: #000000; text-align: center;">
            ▶ Video Title
        </td>
    </tr>
</table>
```

## Implementation Steps

1. Collect all Google Tasks data (completed in last 7 days + incomplete backlog)
2. Collect all Google Keep notes
3. Generate HTML email with brutalist design
4. Ask user for sender and recipient email addresses
5. Send email using Resend MCP

## Required MCP Servers

This skill requires the following MCP servers to be configured:

- **google-tasks** - For retrieving task lists and tasks
- **keep-mcp** - For retrieving Keep notes
- **resend** - For sending formatted HTML emails

**Setup Instructions:** See `reference.md` in this skill directory for complete MCP server installation, configuration, verification, and troubleshooting instructions.
