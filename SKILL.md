---
name: send-keep-summary
description: Generates and sends a formatted HTML email summary containing Google Tasks (completed tasks from last 7 days and current backlog) and Google Keep notes with modern clean design. Use this skill when the user wants to send their weekly task summary or productivity digest via email.
allowed-tools: mcp__google-tasks__list_task_lists, mcp__google-tasks__list_tasks, mcp__keep-mcp__find, mcp__resend__send-email, AskUserQuestion, Read
---

# Send Keep Summary Skill

This skill collects data from Google Tasks and Google Keep, then sends a beautifully formatted HTML email with a modern clean design.

## Configuration

```json
{
  "from": "aaron@majorsolutions.biz",
  "to": "aaron265@gmail.com",
  "subject": "Daily Digest"
}
```

**Update these values as needed for your email setup.**

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
2. Organize notes by categories if possible (extract from titles or labels)
3. Extract and process media from notes:
   - Look for YouTube URLs in note content (youtube.com or youtu.be links)
   - Extract video IDs from YouTube URLs
   - Generate thumbnail URLs using format: `https://img.youtube.com/vi/{VIDEO_ID}/maxresdefault.jpg`
   - Extract any image URLs or attachments from notes
   - Preserve links and titles associated with media
4. Separate recipe-related notes into their own category:
   - Look for recipe-related keywords in titles/content (recipe, cooking, baking, ingredients, etc.)
   - Or use recipe-specific labels/categories if available

### Email Configuration

1. Use the email settings from the **Configuration** section at the top of this file
   - `from`: aaron@majorsolutions.biz
   - `to`: aaron265@gmail.com
   - `subject`: Daily Digest
2. Use `mcp__resend__send-email` to send the formatted HTML email with these values

## Email Structure (in order)

1. **Header** - "Daily Digest" title with date/time and colored accent line
2. **Stats Section** - Card-based statistics (notes captured, tasks completed, links included, videos/media found)
3. **Content Sections** - Organized by category:
   - Recently Completed Tasks (grouped by task list)
   - Current Backlog (grouped by task list)
   - Recipes (recipe-related notes with media, if any)
   - Google Keep Notes (organized by category if available, excluding recipes)
   - Media Gallery (YouTube videos and other media found in notes)

## HTML/CSS Guidelines

### Requirements

- ✅ Use inline styles only
- ✅ Use proper email DOCTYPE: `<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">`
- ✅ Maximum width: 800px (responsive container)
- ✅ Use table-based layouts for email compatibility
- ✅ Include background colors and proper spacing
- ✅ Ensure font-family fallbacks (system-ui, -apple-system, sans-serif)

### Restrictions

- ❌ NO CSS Grid or Flexbox (use tables instead)
- ❌ NO pseudo-elements (::before, ::after)
- ❌ NO transform properties
- ❌ NO position: absolute/fixed
- ❌ NO @media queries (keep it simple and responsive with max-width)

## Design Style

**Modern Clean Design** with soft gradients and rounded corners:

### Color Palette
- **Background**: Light gradient #fafafa to #f5f5f5
- **Primary**: #4a5a7a (Deep blue-gray)
- **Accent**: #7db8b3 (Soft teal)
- **Border**: #e5e5e5 (Light gray)
- **Card background**: #ffffff (White)
- **Text primary**: #404040 (Dark gray)
- **Text muted**: #8c8c8c (Medium gray)

### Typography
- **Headings**: System fonts, semibold (600), tracking tight
- **Body text**: 14px, line-height 1.6
- **Small text**: 12px for metadata

### Components

**Header Section:**
```html
<table width="100%" cellpadding="0" cellspacing="0" style="border-bottom: 1px solid #e5e5e5; padding-bottom: 30px; margin-bottom: 40px;">
    <tr>
        <td>
            <div style="width: 96px; height: 4px; background: linear-gradient(90deg, #4a5a7a 0%, #7db8b3 100%); border-radius: 2px; margin-bottom: 20px;"></div>
            <h1 style="margin: 0; font-size: 40px; font-weight: 600; color: #404040; letter-spacing: -0.02em;">Daily Digest</h1>
            <p style="margin: 12px 0 0 0; font-size: 14px; color: #8c8c8c;">
                November 03, 2025 <span style="color: #d0d0d0;">•</span> 02:58 PM
            </p>
        </td>
    </tr>
</table>
```

**Stats Card:**
```html
<table width="32%" cellpadding="0" cellspacing="0" style="display: inline-block; vertical-align: top; background: #ffffff; border: 1px solid #e5e5e5; border-radius: 12px; padding: 24px; margin-right: 2%;">
    <tr>
        <td>
            <div style="display: inline-block; background: rgba(74, 90, 122, 0.1); border-radius: 8px; padding: 8px; margin-bottom: 12px;">
                <span style="color: #4a5a7a; font-size: 20px;">📝</span>
            </div>
            <div style="font-size: 36px; font-weight: 600; color: #404040; margin-bottom: 8px;">63</div>
            <div style="font-size: 12px; font-weight: 500; color: #8c8c8c;">Notes Captured</div>
        </td>
    </tr>
</table>
```

**Section Header:**
```html
<table width="100%" cellpadding="0" cellspacing="0" style="margin: 80px 0 32px 0;">
    <tr>
        <td style="width: auto;">
            <h2 style="margin: 0; font-size: 24px; font-weight: 600; color: #404040; letter-spacing: -0.01em; white-space: nowrap;">Career & Professional</h2>
        </td>
        <td style="width: 100%; padding-left: 16px;">
            <div style="height: 1px; background: linear-gradient(90deg, #e5e5e5 0%, transparent 100%);"></div>
        </td>
    </tr>
</table>
```

**Note Item:**
```html
<table width="100%" cellpadding="0" cellspacing="0" style="background: #ffffff; border: 1px solid #e5e5e5; border-radius: 12px; padding: 16px 20px; margin-bottom: 12px; position: relative;">
    <tr>
        <td style="padding-left: 12px; border-left: 4px solid rgba(74, 90, 122, 0.2); border-radius: 0 12px 12px 0;">
            <p style="margin: 0; font-size: 14px; line-height: 1.6; color: #8c8c8c;">
                Your note content here
            </p>
        </td>
    </tr>
</table>
```

**Task Card:**
```html
<table width="100%" cellpadding="0" cellspacing="0" style="background: #ffffff; border: 1px solid #e5e5e5; border-radius: 12px; padding: 16px 20px; margin-bottom: 12px;">
    <tr>
        <td>
            <div style="font-weight: 600; font-size: 14px; color: #404040; margin-bottom: 8px;">
                Task Title Here
            </div>
            <div style="font-size: 12px; color: #8c8c8c; margin-bottom: 6px;">
                Due: Nov 5, 2025 | Completed: Nov 3, 2025
            </div>
            <div style="font-size: 12px; color: #5c5c5c; line-height: 1.5;">
                Task notes or description (if any)
            </div>
        </td>
    </tr>
</table>
```

**Video Card (3-column grid using tables):**
```html
<table width="100%" cellpadding="0" cellspacing="0" style="margin-bottom: 24px;">
    <tr>
        <td width="32%" style="padding-right: 2%;" valign="top">
            <table width="100%" cellpadding="0" cellspacing="0" style="background: #ffffff; border: 1px solid #e5e5e5; border-radius: 12px; overflow: hidden;">
                <tr>
                    <td>
                        <div style="position: relative; width: 100%; padding-bottom: 56.25%; background: #f5f5f5;">
                            <img src="THUMBNAIL_URL" alt="Video Title" style="position: absolute; width: 100%; height: 100%; object-fit: cover;" />
                            <div style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); width: 56px; height: 56px; background: rgba(255,255,255,0.95); border-radius: 50%; display: flex; align-items: center; justify-content: center;">
                                <span style="color: #404040; font-size: 20px;">▶</span>
                            </div>
                        </div>
                    </td>
                </tr>
                <tr>
                    <td style="padding: 16px;">
                        <div style="font-size: 14px; font-weight: 500; color: #404040; line-height: 1.4;">
                            Video Title
                        </div>
                    </td>
                </tr>
            </table>
        </td>
        <!-- Repeat for 2 more video cards -->
    </tr>
</table>
```

**Link Item:**
```html
<table width="100%" cellpadding="0" cellspacing="0" style="background: #ffffff; border: 1px solid #e5e5e5; border-radius: 12px; padding: 16px 20px; margin-bottom: 8px;">
    <tr>
        <td width="40" style="padding-right: 16px;">
            <div style="width: 40px; height: 40px; background: rgba(74, 90, 122, 0.1); border-radius: 8px; display: flex; align-items: center; justify-content: center; font-size: 20px;">
                🍱
            </div>
        </td>
        <td style="width: 100%;">
            <span style="font-size: 14px; font-weight: 500; color: #404040;">Link Title Here</span>
        </td>
        <td width="20" style="padding-left: 12px;">
            <span style="color: #8c8c8c; font-size: 16px;">→</span>
        </td>
    </tr>
</table>
```

## Email Template Structure

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Daily Digest</title>
</head>
<body style="margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif; background: linear-gradient(180deg, #fafafa 0%, #f5f5f5 100%);">
    <table width="100%" cellpadding="0" cellspacing="0" border="0" style="background: linear-gradient(180deg, #fafafa 0%, #f5f5f5 100%); padding: 48px 16px;">
        <tr>
            <td align="center">
                <table width="100%" cellpadding="0" cellspacing="0" border="0" style="max-width: 800px; background: transparent;">
                    <tr>
                        <td style="padding: 0 24px;">

                            <!-- HEADER -->
                            [Header section code here]

                            <!-- STATS -->
                            [Stats cards here]

                            <!-- CONTENT SECTIONS -->
                            [Sections with tasks and notes here]

                        </td>
                    </tr>
                </table>
            </td>
        </tr>
    </table>
</body>
</html>
```

## Implementation Steps

1. Read email configuration from the Configuration section above
2. Collect all Google Tasks data (completed in last 7 days + incomplete backlog)
3. Collect all Google Keep notes
4. Calculate statistics (number of notes, tasks completed, etc.)
5. Generate HTML email with modern clean design using the component structures above
6. Send email using Resend MCP with config values (from, to, subject)

## Required MCP Servers

This skill requires the following MCP servers to be configured:

- **google-tasks** - For retrieving task lists and tasks
- **keep-mcp** - For retrieving Keep notes
- **resend** - For sending formatted HTML emails

**Setup Instructions:** See `reference.md` in this skill directory for complete MCP server installation, configuration, verification, and troubleshooting instructions.
