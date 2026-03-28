---
name: daily-view
type: task
version: 1.0.0
collection: capture
description: Quick synthesis of what needs attention today. Surfaces overdue items, today's due dates, high-priority tasks, and optionally the read-later queue and developing ideas.
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

The daily view is a quick orientation — "what should I focus on today?" It's not a full review, not a deep dive. It reads the registry and presents the most important things in a format you can scan in 30 seconds.

### Inputs

None required. The member can optionally scope: "show me today's view for my Work list" or "daily view, just tasks."

### Outputs

No files written. Display only.

### Cadence & Triggers

On demand. Typically once at the start of a work session. Can also be surfaced automatically at session start if the member configures it.

---

## Workflow

### Step 1: Load Registry

Read `items.json` and `lists.json` from `/members/{member_hash}/capture/`.

Apply scope filter if the member provided one.

---

### Step 2: Build the View

Gather items into sections, in this order:

**Section 1: Overdue** — tasks with due dates that have passed. Sort by due date (most overdue first).

**Section 2: Due Today** — tasks due today.

**Section 3: High Priority Active** — priority 1 and 2 items that are active or in progress, regardless of due date. Exclude items already shown in sections 1–2.

**Section 4: In Progress** — items currently marked `in_progress` (any type). Exclude items already shown above.

**Section 5: Inbox Count** — just the count of inbox items, not the items themselves. A nudge to process if the count is high.

**Section 6: Context Quick-Links** — if the member has contexts configured, show counts: "@deep-work: {N} items, @quick-wins: {N} items, @phone: {N} items." These are tappable — the member can say "show me my @quick-wins" to filter.

---

### Step 3: Present

> **Today — {day of week}, {date}**
>
> {if overdue}:
> **Overdue**
> {for each}: - {CAP-NNN}: {title} — due {date} ({N days ago})
>
> {if due today}:
> **Due Today**
> {for each}: - {CAP-NNN}: {title}
>
> {if high priority items}:
> **High Priority**
> {for each}: - {CAP-NNN}: {title} ({type}, priority {N})
>
> {if in progress}:
> **In Progress**
> {for each}: - {CAP-NNN}: {title} ({type})
>
> {if inbox count > 0}:
> **Inbox:** {N} items — process with '@ai:review'
>
> {if contexts configured}:
> **By context:** {for each context with items}: {context}: {N}
>
> {if nothing in any section}:
> "All clear today. Nothing overdue, nothing due, and no high-priority items. Nice."

Keep it concise. No descriptions, no notes, no "why" — just titles, types, priorities, and dates. The daily view is a scan, not a read.

---

### Step 4: Offer Actions

After the view, one line:

> "Want to work on something, mark anything done, or open your full item list?"

If the member picks an item: hand off to the appropriate action (complete it, open details, etc.) without requiring them to invoke manage-items explicitly.

---

## Directives

### Behavior

The daily view should be instant and scannable. Don't add commentary. Don't explain what each section means. Just present the data.

If the member asks a follow-up question about a specific item after seeing the daily view, answer it in context — don't make them invoke a different task.

If the member has a context configured and mentions it ("what are my quick wins?"), filter the view to that context.

### Constraints

Never write to any files. The daily view is read-only.

Never show archived or deleted items.

Never show more than 10 items per section. If a section has more than 10, show the top 10 and note: "+{N} more — filter with '@ai:manage-items' to see all."

### Edge Cases

If the member has no items at all: "Nothing captured yet. Say '@ai:capture' to start adding things."

If the registry is entirely read-later and ideas (no tasks): adapt the view — show queued read-later items and developing ideas instead of the task-focused default. Don't present an empty task view when the member clearly uses the system for content and ideas.

If the member invokes daily view mid-afternoon: still useful. Same view, same content. The name is "daily" but it's really "what needs attention right now."
