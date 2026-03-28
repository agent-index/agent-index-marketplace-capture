---
name: manage-lists
type: task
version: 1.0.0
collection: capture
description: Create, edit, archive, and organize lists. Lists are user-defined containers for grouping items — "Home renovation," "Q2 planning," "Books 2026." Items with no list live in the inbox.
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

Lists are the primary organizational containers in the capture system. They group related items together — a list might represent a project, a life area, a reading theme, or any other grouping that makes sense to the member. Items not assigned to a list live in the inbox.

This task manages the list registry — creating new lists, editing their details, archiving lists that are no longer active, and viewing what's in each list.

### Inputs

The member describes what they want to do with lists.

### Outputs

Updated `lists.json` in `/members/{member_hash}/capture/`.

### Cadence & Triggers

On demand.

---

## Workflow

### Step 1: Load Lists

Read `lists.json` from `/members/{member_hash}/capture/`. If it doesn't exist, initialize:
```json
{
  "last_updated": "{today}",
  "lists": []
}
```

Also read `items.json` to know item counts per list.

If the member's intent is clear from their invocation, proceed to the appropriate action.

If unclear, present the current lists:

> **Your Lists**
> {for each active list}:
> - **{display_name}** — {item count} items ({active count} active)
>   {description if present}
> Inbox: {inbox item count} items
>
> What would you like to do?

---

### Step 2: Route to Action

- **Create a list** → Step 3
- **Edit a list** → Step 4
- **Archive a list** → Step 5
- **Delete a list** → Step 6
- **View a list's contents** → Step 7

---

### Step 3: Create a List

Ask: "What would you like to call this list?"

Accept any non-empty string as the display name. Generate a slug: lowercase, spaces to hyphens, special characters removed.

Optionally ask: "Want to add a short description? Helps remember what this list is for." Accept or skip.

Check for slug collision with existing lists (including archived ones).

Write to `lists.json`:
```json
{
  "slug": "{slug}",
  "display_name": "{display_name}",
  "description": "{description or null}",
  "status": "active",
  "created_date": "{today}",
  "archived_date": null
}
```

Confirm: "List **{display_name}** created. Move items here with '@ai:manage-items' or capture directly to it: 'capture {thing} to {list name}'."

---

### Step 4: Edit a List

The member identifies a list and describes what to change:
- **Display name** — rename
- **Description** — add, change, or clear
- **Slug** — not directly editable (too many references). If the member wants a new slug, suggest creating a new list and moving items.

Confirm changes and write.

---

### Step 5: Archive a List

Archiving a list hides it from the default list view but preserves it and its items. Items in an archived list remain in `items.json` with their list assignment unchanged — they can still be found via search or explicit filtering.

Confirm: "Archive **{display_name}**? Items in this list won't appear in default views but are still searchable."

On confirmation: set `status` to `archived`, set `archived_date` to today.

The member can unarchive later: "Unarchive my {list name} list" — set `status` back to `active`, clear `archived_date`.

---

### Step 6: Delete a List

Deleting a list removes it permanently. Items in the deleted list are moved to the inbox (their `list` field is set to `null`).

Confirm: "Delete **{display_name}**? {N} items in this list will be moved to the inbox."

On confirmation: update all items with this list slug to `list: null`, remove the list from `lists.json`.

---

### Step 7: View List Contents

Present all active (non-archived, non-deleted) items in the specified list, using the same format as manage-items Step 3 (compact table with key fields).

Show the list's description at the top if it has one.

After presenting: "Want to manage any of these items, or do something else with this list?"

---

## Directives

### Behavior

Lists should be lightweight. Creating a list is a two-second operation — name it, done. Don't over-engineer the creation process.

When the member creates a list, if they have inbox items that obviously belong there, offer: "You have {N} items in your inbox. Want me to show them so you can move relevant ones to this list?"

### Constraints

Never auto-create lists. Lists are always explicitly created by the member.

The inbox is not a list — it's the absence of a list assignment. It cannot be renamed, archived, or deleted.

Never delete items when deleting a list. Items always survive — they move to the inbox.

### Edge Cases

If a list slug collides with an existing active list: ask for a different name.

If a list slug collides with an archived list: ask whether to unarchive the existing list or choose a different name.

If the member asks to "organize my inbox into lists": this is better handled by the review task — suggest '@ai:review' instead, which has a structured inbox processing flow.
