# Capture — Changelog

All notable changes will be documented here.

Format: [MAJOR.MINOR.PATCH] — YYYY-MM-DD

---

## [1.0.2] — 2026-04-19

### Added
- **Natural language trigger phrases in `collection.json`.** API entries now include trigger arrays that map conversational phrases to capabilities, powering the routing layer introduced in agent-index-core 3.0.5. Members can say things like "capture this" or "what's on my plate today" instead of using `@ai:` alias syntax. Triggers are customizable per-member via `routing.json`.

---

## [1.0.1] — 2026-03-31

### Fixed
- All task workflows now correctly reference the **local** capture directory (`members/{member_hash}/capture/`) using native file tools (Read/Write). Previously, paths used a leading slash (`/members/{member_hash}/capture/`) which resolved to the remote filesystem, causing capture items to be stored on Google Drive instead of locally.
- Each task's initialize/load step now explicitly states the tool family (native Read/Write, not `aifs_*`) to prevent ambiguous storage routing.

### Changed
- Updated README directory structure to reflect local-first storage convention and added explanatory note about the local-first design.

---

## [1.0.0] — 2026-03-26

### Added
- Initial release
- `capture` task — quick-capture entry point with minimal friction
- `manage-items` task — full CRUD for captured items
- `manage-lists` task — create, edit, archive organizational lists
- `review` task — inbox processing and maintenance review
- `daily-view` task — quick synthesis of what needs attention today
- `capture-tutorial` skill — guided tour and Q&A
- Four item types: task, read-later, idea, reference
- Organization via lists, tags, and contexts
- Structured review process with inbox processing and maintenance modes
