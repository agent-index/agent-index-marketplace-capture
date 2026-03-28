---
name: review
type: task
version: 1.0.0
collection: capture
description: Structured processing of the capture inbox and maintenance review of active items. Inbox processing organizes new captures. Maintenance review surfaces stale, overdue, and upcoming items that need attention.
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

The review task is what keeps the capture system healthy. Without regular reviews, the inbox grows, items go stale, and the system becomes a graveyard of forgotten captures. With regular reviews, the inbox stays processed, priorities stay current, and the member has a clear picture of what needs attention.

The review has two modes that can run independently or together:

**Inbox processing** — work through unorganized items in the inbox. For each item, decide: what type is it, what list does it belong to, what priority, what context? This is the GTD "processing" step.

**Maintenance review** — scan active items for things that need attention: overdue tasks, items not updated in a long time, ideas that have been parked for a while, read-later items that have been queued forever. This is the GTD "weekly review" step.

### Inputs

The member may invoke a specific mode ("process my inbox," "do a full review") or invoke generically.

### Outputs

Updated `items.json`. Updated `state/review-history.jsonl`.

### Cadence & Triggers

The member's configured review cadence determines how often Claude suggests a review. But the member can invoke at any time.

---

## Workflow

### Step 1: Load State

Read `items.json`, `lists.json`, and `state/review-history.jsonl` from `/members/{member_hash}/capture/`.

Read the member's setup responses to know their configured review cadence and contexts.

Determine what needs attention:
- Inbox item count
- Overdue task count
- Items not updated in 14+ days
- Ideas parked for 30+ days
- Read-later items queued for 30+ days
- Last review date (from review-history.jsonl)

---

### Step 2: Determine Mode

If the member specified a mode: use it.

If invoked generically, present what needs attention:

> **Review Summary**
> Inbox: {N} items to process
> Overdue: {N} tasks past due
> Stale: {N} items not touched in 14+ days
> Long-queued: {N} read-later items queued 30+ days
> Last review: {date or "never"}
>
> Want to process the inbox, do a maintenance review, or both?

If the inbox has items, default to starting there. If the inbox is empty, default to maintenance review.

---

### Step 3: Inbox Processing

Present inbox items one at a time, oldest first. For each item:

> **CAP-{NNN}: {title}** ({current type})
> {description if present}
> {why if present — "You said: {why}"}
> Captured: {created_date}
>
> What would you like to do with this?

Help the member decide on each item. Claude should proactively suggest based on the item's content:

- **Type** — confirm or change. "This sounds like a task — agree?"
- **List** — suggest a list if one is obvious from the item's content and the member's existing lists. "This seems like it belongs in your AI Research list?"
- **Priority** — suggest if the item has urgency signals. Otherwise: "Want to set a priority, or leave it unprioritized for now?"
- **Context** — suggest if obvious. A podcast → `@commute`. A deep article → `@deep-work`. A quick errand → `@errand`.
- **Due date** — ask only for tasks that seem time-sensitive.
- **Tags** — suggest if the item connects to existing tags.

The member can also:
- **Dismiss** — "This isn't worth tracking anymore." Archive or delete the item.
- **Quick-decide** — "Task, Work list, priority 3, done." Accept all at once.
- **Skip** — "Skip for now." Leave in inbox, move to next item.

After processing each item, move to the next. Show progress: "({N} of {M} inbox items processed)"

The member can stop at any time: "That's enough for now." Record progress and note how many inbox items remain.

After all inbox items are processed (or the member stops):

> "Inbox processing done. {N} items organized, {M} remaining."

---

### Step 4: Maintenance Review

Scan active items and surface what needs attention, grouped by urgency:

**Overdue tasks** (due date has passed, not completed):
> ⚠ **Overdue**
> {for each}: CAP-{NNN}: {title} — due {date} ({N days ago})
>
> For each: "Want to update the due date, complete it, or defer it?"

**Stale items** (active but not updated in 14+ days):
> **Needs attention** (not touched in 14+ days)
> {for each}: CAP-{NNN}: {title} ({type}) — last updated {date}
>
> For each: "Still relevant? Want to update, archive, or defer?"

**Long-queued read-later** (queued 30+ days):
> **Long-queued content**
> {for each}: CAP-{NNN}: {title} — queued since {date}
>
> For each: "Still want to read this? Keep, archive, or bump the priority?"

**Parked ideas** (parked 30+ days):
> **Parked ideas**
> {for each}: CAP-{NNN}: {title} — parked since {date}
>
> For each: "Worth revisiting? Reactivate, keep parked, or archive?"

**Upcoming due dates** (due in next 7 days):
> **Coming up this week**
> {for each}: CAP-{NNN}: {title} — due {date}
>
> Informational — no action required unless the member wants to adjust.

For each group, the member works through the items. They can act on each one, skip it, or say "these are all fine" to acknowledge a group without changing anything.

---

### Step 5: Review Summary and Write

After both modes (or whichever mode was run), present a summary:

> **Review Complete**
> Inbox items processed: {N} (of {M})
> Items updated: {N}
> Items archived: {N}
> Items completed: {N}
> Items remaining in inbox: {N}
>
> {if items remain}: "Your next review is suggested {based on cadence}."

Write all changes to `items.json`.

Append to `state/review-history.jsonl`:
```json
{
  "timestamp": "{ISO 8601}",
  "type": "{inbox_processing|maintenance|full}",
  "inbox_processed": {N},
  "inbox_remaining": {N},
  "items_updated": {N},
  "items_archived": {N},
  "items_completed": {N}
}
```

---

## Directives

### Behavior

Reviews should feel productive, not tedious. Keep the pace moving. If the member is clearly in a "quick decisions" mode, match that energy — don't over-explain each item.

For inbox processing: make suggestions proactively. Don't just present items and wait. "This looks like a task for your Work list, maybe priority 3?" gives the member something to react to rather than having to think from scratch.

For maintenance review: be honest about what's stale. If an item hasn't been touched in 60 days, say so plainly — "This has been sitting here for two months. Still worth tracking?"

The review should feel like a conversation with a sharp assistant who's looking at your whole capture system and pointing out what needs attention.

### Constraints

Never auto-archive or auto-complete items during reviews. Every change requires member acknowledgment.

Never make reviews feel mandatory. If the member says "I'm done" mid-review, stop immediately and save progress.

Never present more than one group of maintenance items at a time. Work through overdue first, then stale, then long-queued, etc. Let the member control the pace.

### Edge Cases

If the inbox is empty and there's nothing to review in maintenance: "Everything looks good. Inbox is clear, nothing overdue, no stale items. You're on top of it." Log the review.

If the inbox has 50+ items (a backlog): acknowledge it — "You've got {N} items in your inbox. Want to work through as many as you can, or focus on the most recent ones first?" Offer to do recent-first or oldest-first.

If the member hasn't done a review in a long time and everything is stale: don't present every single item. Summarize: "You have {N} stale items across {M} lists. Want to do a full sweep, or focus on a specific list or type?"

If the member processes an inbox item and immediately captures something related: handle it smoothly — capture the new item and continue the review without losing context.
