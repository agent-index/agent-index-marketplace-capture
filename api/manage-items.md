---
name: manage-items
type: task
version: 1.0.0
collection: capture
description: Full CRUD for captured items. View, filter, sort, update, complete, archive, delete, change type, add notes, and link items. Filter by type, list, tag, context, priority, status, or date range.
stateful: false
produces_artifacts: false
produces_shared_artifacts: false
dependencies:
  skills: []
  tasks: []
external_dependencies: []
reads_from: null
writes_to: null
---

## About This Task

The manage-items task is the workbench for everything in the capture registry. It provides full visibility and control — viewing, filtering, updating, completing, archiving, deleting, and connecting items. While the capture task is about speed, manage-items is about organization and control.

### Inputs

The member describes what they want to do — view items, update a specific item, complete a task, add notes, etc.

### Outputs

Updated `items.json`.

### Cadence & Triggers

On demand.

---

## Workflow

### Step 1: Load Registry

Read `items.json` and `lists.json` from `/members/{member_hash}/capture/`.

If the member's intent is clear from their invocation (e.g., "mark CAP-012 as done," "show me my read-later items"), proceed directly to the appropriate action.

If unclear, present a summary dashboard:

> **Capture Registry**
> Total items: {count} (excluding archived/deleted)
> Inbox: {count} — Tasks: {count} — Read-later: {count} — Ideas: {count} — References: {count}
> Overdue: {count} — Due this week: {count}
> {if any pending}: Items needing attention: {count}
>
> What would you like to do?

---

### Step 2: Route to Action

Available actions:

- **View/filter** → Step 3
- **Update an item** → Step 4
- **Complete an item** → Step 5
- **Archive an item** → Step 6
- **Delete an item** → Step 7
- **Change item type** → Step 8
- **Add notes** → Step 9
- **Link items** → Step 10
- **Bulk operations** → Step 11

---

### Step 3: View and Filter

The member can filter by any combination of:
- **Type**: `task`, `read_later`, `idea`, `reference`
- **List**: any list name, or `inbox` for unorganized items
- **Tag**: any tag
- **Context**: any context
- **Priority**: 1–5, or "unprioritized"
- **Status**: `inbox`, `active`, `queued`, `in_progress`, `captured`, `developing`, `done`, `consumed`, `developed`, `parked`, `archived`, `deleted`
- **Date range**: "created this week," "overdue," "due this month," "not updated in 30 days"
- **Search**: text search across title, description, why, and notes

Default sort: priority (1 first, unprioritized last), then due date (soonest first), then created date (newest first).

Present matching items in a compact format:

> | ID | Type | Title | Priority | Status | Due | List |
> |---|---|---|---|---|---|---|
> | CAP-001 | task | Buy groceries | 3 | active | Mar 25 | errands |
> | CAP-004 | read | AI pricing article | — | queued | — | ai-research |

For each item, if `why` is populated, show it as a subtitle: "Why: Had a great framework for thinking about pricing"

Default view excludes archived and deleted items. Member can explicitly request: "Show me archived items" or "Show me everything including deleted."

After presenting, ask: "Want to see details on any of these, or make changes?"

Detail view for a single item shows all fields: ID, type, title, description, why, list, tags, context, priority, status, due date, source URL, estimated effort, linked items, notes, all dates.

---

### Step 4: Update an Item

The member identifies an item and describes what to change. Updatable fields:

- **Title** — rename
- **Description** — add or revise
- **Why** — add or update the capture context
- **List** — move to a different list or to inbox
- **Tags** — add or remove
- **Context** — change or clear
- **Priority** — set or change (1–5, or clear)
- **Due date** — set, change, or clear
- **Source URL** — set or change
- **Estimated effort** — set or change (read-later items)
- **Status** — see status transitions below

Confirm changes, update `items.json`, set `last_updated_date` to today.

**Status transitions by type:**

Task statuses:
| From | Allowed To |
|---|---|
| inbox | active, in_progress, done |
| active | in_progress, done, deferred |
| in_progress | done, active, deferred |
| deferred | active, done |

Read-later statuses:
| From | Allowed To |
|---|---|
| queued | in_progress, consumed |
| in_progress | consumed, queued |

Idea statuses:
| From | Allowed To |
|---|---|
| captured | developing, parked |
| developing | developed, parked, captured |
| parked | developing, captured |

Reference statuses:
- References are always `active`. No status transitions.

---

### Step 5: Complete an Item

Shortcut for the most common action. The member says "done with CAP-005" or "finished reading that article."

For tasks: set status to `done`, set `completed_date` to today.

For read-later items: set status to `consumed`, set `completed_date` to today. Ask: "Any takeaways you want to note?" If yes, add to notes. If no, proceed.

For ideas: set status to `developed`, set `completed_date` to today. Ask: "Did this idea lead to anything — a task to create, a connection to note?" If yes, handle it. If no, proceed.

Confirm: "Completed: **{title}**"

---

### Step 6: Archive an Item

Archiving removes the item from active views but keeps it searchable. The member can archive any item regardless of status.

Set `status` to `archived`, set `archived_date` to today. Preserve all other fields.

Confirm: "Archived: **{title}**. It's still searchable — say 'show archived items' to find it."

Bulk archive is supported: "Archive all completed tasks" or "Archive everything in the old-project list."

---

### Step 7: Delete an Item

Deleting is permanent removal from the active registry. The item is marked as deleted but its record remains in `items.json` for ID continuity — it will not appear in any view including "show archived."

Set `status` to `deleted`. IDs are never reused.

Confirm before deleting: "Delete **{title}**? This is permanent — it won't appear in any view, including archives."

Bulk delete is supported but requires explicit confirmation: "Delete these {N} items? This is permanent."

---

### Step 8: Change Item Type

The member can change an item's type at any time. Common scenarios: an idea becomes a task, a read-later item turns out to be reference material.

When type changes, status resets to the default for the new type:
- → `task`: status becomes `active`
- → `read_later`: status becomes `queued`
- → `idea`: status becomes `captured`
- → `reference`: status becomes `active`

Confirm: "Changed **{title}** from {old type} to {new type}."

---

### Step 9: Add Notes

Notes accumulate over time — each note is timestamped and appended, never replacing previous notes.

The member identifies an item and provides the note. Claude adds it:
```json
{
  "date": "{today}",
  "content": "{note text}"
}
```

This is especially valuable for:
- Read-later items: key takeaways after consuming
- Ideas: development notes as thinking evolves
- Tasks: progress notes or context updates

Confirm: "Note added to **{title}**."

---

### Step 10: Link Items

Items can reference other items. Links are bidirectional — linking A to B also links B to A.

The member identifies two items to link. Optionally provides a relationship description: "This article inspired that idea" or "This task came from that idea."

Add each item's ID to the other's `linked_items` array. Store the relationship note if provided.

Link format in `linked_items`:
```json
["CAP-003", "CAP-015"]
```

Confirm: "Linked **{title A}** ↔ **{title B}**."

When viewing an item's details, linked items are shown with their titles and statuses.

---

### Step 11: Bulk Operations

The member can operate on multiple items at once:

- **Bulk move**: "Move all inbox items tagged #work to my Work list"
- **Bulk tag**: "Tag all items in the AI Research list with #ai"
- **Bulk priority**: "Set all items in the Q2 Planning list to priority 2"
- **Bulk archive**: "Archive all completed tasks older than 30 days"
- **Bulk delete**: "Delete all archived items"

For every bulk operation: present the affected items, get explicit confirmation, then apply.

---

## Directives

### Behavior

Manage-items should feel like a responsive workbench. If the member knows exactly what they want ("mark CAP-005 done"), do it immediately. If they're browsing ("show me my ideas"), present a clean view and let them explore.

When presenting items, prioritize readability. Don't show every field in list view — title, type, priority, status, due date, and list are sufficient. Details are for the single-item view.

When the member is updating multiple fields on one item, collect all changes and apply them together rather than writing after each individual change.

### Constraints

Never auto-archive or auto-delete. These are explicit member actions.

Never change an item's ID. IDs are permanent and auto-incrementing.

Never reuse a deleted item's ID.

Archived items must remain searchable. Only deleted items are truly hidden.

### Edge Cases

If the member references an item by title instead of ID: search by title, present matches, and confirm which one. If exactly one match, proceed.

If the member tries an invalid status transition: explain what transitions are available from the current status.

If `items.json` doesn't exist: initialize it and inform the member they have no items yet. Suggest '@ai:capture'.

If the registry is very large (100+ active items): encourage filtering rather than listing everything. Present the summary dashboard first.
