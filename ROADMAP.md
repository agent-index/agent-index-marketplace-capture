# Capture Collection — Roadmap

Current version: 1.0.1
Last updated: 2026-04-05

---

## Current State

v1.0 provides a personal capture and processing system: quick-capture with minimal friction, full CRUD for items across four types (task, read-later, idea, reference), organizational lists with tags and contexts, structured inbox processing and maintenance reviews, and a daily view for quick synthesis. A guided tutorial helps new members onboard. All data is local-first — stored on the member's machine, never on the remote filesystem.

### Known Limitations

- **No scheduled review reminders.** The review task runs on demand but doesn't proactively remind members to process their inbox. Members who forget to run reviews accumulate stale items.
- **No item linking across collections.** Captured items can't reference or link to projects, strategies, or other collection artifacts. A captured idea that becomes a project idea requires manual re-entry in the Projects collection.
- **Tags and contexts are free-form.** There's no org-level tag vocabulary or context standardization. Members may use inconsistent naming across their own items.
- **No recurring tasks.** Task-type items are one-shot. There's no way to define a recurring task that re-appears on a schedule.
- **Attachment storage is unmanaged.** Files in `/attachments/{item-id}/` are stored but not indexed, versioned, or size-limited. Large attachments could fill local storage without warning.

### Known Bugs

None currently tracked.

---

## Wishlist

### v1.1 — Quality of Life

- **Review scheduling.** Integration with agent-index scheduled tasks to prompt inbox reviews on a cadence (daily, every other day, weekly).
- **Item deduplication hints.** When capturing a new item, check for similar titles or URLs and suggest possible duplicates.
- **Attachment size limits.** Configurable per-item and total attachment storage limits with warnings.

### v1.2 — Cross-Collection Integration

- **Projects collection linking.** Promote a captured idea directly to a project idea (via `create-idea` in the Projects collection) without re-entering details.
- **Strategy collection linking.** Reference a captured read-later article as a strategy source.
- **Shared lists.** Allow lists to be promoted to the remote filesystem for team-level shared capture (e.g., a team reading list).

### v2.0 — Structural Changes (breaking)

- **Recurring tasks.** Task-type items with a recurrence rule that creates a new instance on a schedule. Requires changes to the item schema.
- **Tag taxonomy.** Optional org-level tag and context vocabulary that members can extend. Requires a new org-level configuration surface in collection setup.

---

## Design Notes

- The collection is deliberately local-first. Personal capture is private by nature — what someone saves, how they tag it, and what they think about it is their own business. Shared lists would be an opt-in promotion, not the default storage model.

- The four item types (task, read-later, idea, reference) are intentionally fixed at v1.0. Adding types is a tempting extension, but the existing four cover the vast majority of capture use cases. Custom types would add complexity to the review workflow, daily view, and lifecycle management without proportional benefit.

- The "why" field on items is a deliberate design choice. Most capture tools record what was saved but not why it was interesting. Capturing the context at save time prevents the common experience of reviewing a saved article weeks later and having no idea why you saved it.
