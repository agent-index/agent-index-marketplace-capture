# agent-index-marketplace-capture

Personal capture and processing system for agent-index. Quick-capture tasks, read-later content, ideas, and reference material into one unified registry. Organize with lists, tags, and contexts. Process your inbox and review what needs attention.

## Core Concepts

**Items** — the universal unit. Everything captured is an item with one of four types: task (something to do), read-later (content to consume), idea (something to develop), or reference (information to keep). Types can be changed at any time.

**Why** — every item can capture why it was saved, not just what it is. "Save this article — it has a great framework for thinking about pricing" records both the content and the context that made it interesting.

**Lists** — user-defined containers for grouping items. Items with no list live in the inbox.

**Tags** — cross-cutting labels that span lists.

**Contexts** — situational labels like @deep-work, @quick-wins, @phone, @commute. Filter by what you can work on right now.

**Inbox** — the default landing spot for quick captures. Processed during reviews.

## API Tasks

| Task | Description |
|---|---|
| `capture` | Quick-capture entry point — minimal friction, just describe what to save |
| `manage-items` | Full CRUD — view, filter, sort, update, complete, archive, delete, link |
| `manage-lists` | Create, edit, archive, delete organizational lists |
| `review` | Inbox processing + maintenance review of stale/overdue items |
| `daily-view` | Quick synthesis — what needs attention today |
| `capture-tutorial` | End-user guide to the system |

## Directory Structure

Capture data is **local-first** — it lives on the member's machine under their local workspace, accessed via native file tools (Read/Write/Edit). Items are never written to the remote filesystem unless explicitly promoted to shared storage.

```
members/{hash}/capture/          ← local, native file tools
  items.json                     ← master item registry
  lists.json                     ← list definitions
  /attachments/                  ← files attached to items
    /{item-id}/
  /state/
    current-context.md           ← rolling context for Claude
    review-history.jsonl         ← log of review sessions
```

## Item Lifecycle by Type

- **Task:** inbox → active → in_progress → done
- **Read-later:** queued → in_progress → consumed
- **Idea:** captured → developing → developed (or parked)
- **Reference:** active (no lifecycle)

All items can be archived or deleted regardless of type.

## Version

1.0.3
