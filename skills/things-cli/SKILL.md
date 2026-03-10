---
name: things-cli
description: |
  Manage todos and projects in Things app on macOS using URL schemes. When the user asks to create, update, complete, or search for todos, or open Things views, generate the appropriate URL scheme and execute it directly using `open` command. This skill handles:
  - Creating new todos with title, notes, tags, deadline, reminders
  - Creating projects with deadlines and areas
  - Completing, canceling, or updating existing todos (requires ID)
  - Opening Things views (Today, Inbox, Someday, etc.)
  - Searching for todos in Things
  
  Always execute the URL directly using bash, don't just show it to the user.
compatibility: macOS, Things app installed
---

# Things CLI Skill

This skill lets you manage todos and projects in Things app using URL schemes. When the user expresses a todo-related need, you should **directly execute** the URL using bash.

## Workflow

1. **User says what they want** (e.g., "帮我创建一个待办：明天开会")
2. **You generate the Things URL** based on the request
3. **You execute directly** using bash `open` command

Example:
```
User: 添加待办"明天开会"
You:   open "things:///add?title=明天开会&when=tomorrow"
```

**IMPORTANT**: Always execute the URL using bash, don't just show it to the user. Use:
```bash
open "things:///add?title=..."
```

## Table of Contents
1. [Quick Reference](#quick-reference)
2. [Getting Started](#getting-started)
3. [Creating Todos](#creating-todos)
4. [Creating Projects](#creating-projects)
5. [Updating Todos](#updating-todos)
6. [Updating Projects](#updating-projects)
7. [Searching](#searching)
8. [JSON Commands](#using-json-for-complex-operations)
9. [Reference Documentation](#reference-documentation)

## Quick Reference

| Action | Command |
|--------|---------|
| Create todo | `open "things:///add?title=..."` |
| Create project | `open "things:///add-project?title=..."` |
| Show view | `open "things:///show?id=today"` |
| Update todo | `open "things:///update?id=..."` |
| Search | `open "things:///search?query=..."` |

## Getting Started

### Authorization Token Setup

For commands that modify existing data, users need an authorization token:

1. Open Things app
2. Go to **Things → Settings → General → Things URLs → Manage**
3. Copy the token and provide it to the skill

If the user doesn't have a token, guide them through setting it up first.

### Opening Things Views

You can open specific views in Things using the `show` command:

| View | URL Parameter |
|------|----------------|
| Today | `things:///show?id=today` |
| Inbox | `things:///show?id=inbox` |
| Any Time | `things:///show?id=anytime` |
| Upcoming | `things:///show?id=upcoming` |
| Someday | `things:///show?id=someday` |
| Logbook | `things:///show?id=logbook` |
| All Projects | `things:///show?id=all-projects` |

To open Things with a specific view:

```bash
open "things:///show?id=today"
```

## Creating Todos

### Basic Todo Creation

Use the `add` command to create todos:

```
things:///add?title=Buy%20milk
```

With additional options:

```
things:///add?title=Buy%20milk&notes=Low%20fat&when=tomorrow&tags=Errand
```

### Key Parameters for `add`

| Parameter | Description | Example |
|-----------|-------------|---------|
| `title` | Todo title | `title=Buy%20milk` |
| `notes` | Notes content | `notes=Buy%20organic` |
| `when` | Start date/time | `when=tomorrow`, `when=evening`, `when=2026-03-15` |
| `deadline` | Due date | `deadline=2026-03-20` |
| `tags` | Comma-separated tags | `tags=Work,Important` |
| `list` | Project/area name | `list=Shopping` |
| `list-id` | Project/area ID | `list-id=xyz123` |
| `checklist-items` | Newline-separated checklist | `checklist-items=Item1%0aItem2` |

### Creating Multiple Todos

Use `titles` with newlines (%0a):

```
things:///add?titles=Milk%0aBeer%0aCheese&list=Shopping
```

### Creating Todos with Reminders

Use date-time string:

```
things:///add?title=Call%20doctor&when=2026-03-15@14:00
```

## Creating Projects

### Basic Project Creation

```
things:///add-project?title=Build%20treehouse
```

With options:

```
things:///add-project?title=Plan%20Birthday%20Party&area=Family&deadline=December%2031
```

### Key Parameters for `add-project`

| Parameter | Description |
|-----------|-------------|
| `title` | Project title |
| `notes` | Notes content |
| `when` | Start date |
| `deadline` | Due date |
| `tags` | Tags |
| `area` | Area name |
| `area-id` | Area ID |
| `to-dos` | Initial todos (newline-separated) |

## Updating Todos

To update existing todos, you need the todo's ID. Guide the user to:
- On Mac: Control-click todo → Share → Copy Link
- On iOS: Open todo → Share → Copy Link

The ID is the string after `things:///show?id=` in the link.

### Update Command

```
things:///update?id=TODO_ID&auth-token=AUTH_TOKEN&title=New%20Title
```

### Common Update Operations

| Operation | Parameter | Example |
|-----------|-----------|---------|
| Change title | `title` | `title=Buy%20bread` |
| Change notes | `notes` | `notes=New%20notes` |
| Append notes | `append-notes` | `append-notes=Additional%20info` |
| Set start date | `when` | `when=tomorrow` |
| Set deadline | `deadline` | `deadline=2026-03-20` |
| Add tags | `add-tags` | `add-tags=Important` |
| Replace tags | `tags` | `tags=Work,Personal` |
| Complete todo | `completed` | `completed=true` |
| Cancel todo | `canceled` | `canceled=true` |
| Move to project | `list` | `list=Shopping` |
| Add checklist items | `append-checklist-items` | `append-checklist-items=Item1%0aItem2` |

### Update Examples

Complete a todo:
```
things:///update?id=TODO_ID&auth-token=TOKEN&completed=true
```

Change deadline:
```
things:///update?id=TODO_ID&auth-token=TOKEN&deadline=2026-03-20
```

Move to project:
```
things:///update?id=TODO_ID&auth-token=TOKEN&list=Work
```

## Updating Projects

Similar to todo updates:

```
things:///update-project?id=PROJECT_ID&auth-token=AUTH_TOKEN&title=New%20Title
```

## Searching

Search for todos:

```
things:///search?query=keyword
```

Open search without query:
```
things:///search
```

## Using JSON for Complex Operations

For complex structures, use the JSON command:

```
things:///json?data=[{"type":"to-do","attributes":{"title":"Buy milk"}}]
```

### JSON Todo Example

```json
{
  "type": "to-do",
  "attributes": {
    "title": "Buy milk",
    "notes": "Low fat",
    "when": "tomorrow",
    "tags": ["Errand"],
    "checklist-items": [
      {"type": "checklist-item", "attributes": {"title": "Check expiry date"}}
    ]
  }
}
```

### JSON Project Example

```json
{
  "type": "project",
  "attributes": {
    "title": "Vacation Planning",
    "area": "Personal",
    "items": [
      {"type": "heading", "attributes": {"title": "Flights"}},
      {"type": "to-do", "attributes": {"title": "Search flights"}},
      {"type": "to-do", "attributes": {"title": "Book tickets", "when": "today"}}
    ]
  }
}
```

## Workflow for User Requests

1. **Understand the request** - What does the user want to do?
2. **Build the appropriate Things URL** - Use the commands and parameters below
3. **Execute directly with bash** - Don't just show the URL to the user, run it:
   ```bash
   open "things:///add?title=..."
   ```

**IMPORTANT**: Always execute URLs directly. Don't ask the user to click a link.

## Handling IDs

When user wants to update/delete existing items but doesn't have IDs:

1. Ask them to get the ID using the Share → Copy Link method
2. Or use `things:///search?query=...` to find the item first
3. The ID appears in the copied link

---

## Reference Documentation

For complete and detailed API reference, see: `references/things-url-scheme.md`

This reference contains the full official documentation from Things support, including:
- All command parameters and data types
- x-callback-url support
- Authorization token details
- JSON command for complex operations
- Complete examples for all operations
