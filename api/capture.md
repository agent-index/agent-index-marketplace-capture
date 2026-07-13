---
name: capture
type: task
version: 1.0.1
collection: capture
description: Quick-capture entry point. Minimal friction — describe what you want to save and Claude structures it. Items land in the inbox by default or can be organized immediately.
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

The capture task is the front door of the system. Its job is to get things out of the member's head and into the registry as fast as possible. The member describes what they want to save — a task, an article, an idea, a piece of reference material — and Claude structures it into an item.

The design principle is minimal friction. The member should never have to think about taxonomy or metadata before they can capture something. All organization is optional at capture time and can be done later during a review.

### Inputs

The member describes what they want to capture. Everything else is optional.

### Outputs

New item(s) added to `items.json`.

### Cadence & Triggers

On demand, whenever something needs to be captured. This task should feel instant — no multi-step interview, no confirmation gate for simple captures.

---

## Workflow

### Step 1: Initialize

Read `items.json` from the member's **local** capture directory: `members/{member_hash}/capture/`. Use native file tools (Read/Write), not `aifs_*` — capture data is local-first and stays on the member's machine unless explicitly promoted to shared storage.

If it doesn't exist, initialize:
```json
{
  "last_updated": "{today}",
  "next_id": 1,
  "items": []
}
```

Also read `lists.json` to know available lists, and the member's setup responses to know their configured contexts.

---

### Step 2: Parse the Capture

The member's input might be anything from a single sentence to a detailed description with metadata. Parse it to extract:

**Always determine:**
- **Title** — a concise summary of the item. If the member gave a short phrase, that's the title. If they gave a paragraph, distill it.
- **Type** — infer from content:
  - Sounds like something to do → `task`
  - Is a URL, article, podcast, video, book, or content to consume → `read_later`
  - Is a thought, concept, or inspiration to develop → `idea`
  - Is information to keep for later reference → `reference`
  - If ambiguous: default to `task` for actionable-sounding things, `idea` for everything else

**Always capture:**
- **Why** — if the member provided context about why this matters or why they're capturing it, record it. If not, leave as `null`. Never prompt for this during quick capture — it should be provided naturally or not at all.

**Extract if provided (don't ask if not):**
- **List** — if the member names a list or it's obvious from context
- **Tags** — if the member includes hashtags or explicit labels
- **Context** — if the member mentions a situation (`@deep-work`, etc.)
- **Priority** — if the member indicates urgency or importance (1–5)
- **Due date** — if the member mentions a deadline
- **Source URL** — if the member provides a link
- **Estimated effort** — for read-later items: `quick` (< 10 min), `medium` (10–30 min), `long` (30+ min). Infer from content type if possible (tweet thread → quick, long-form article → medium, book → long).

**Anything not explicitly provided or inferable: leave as null.** The inbox exists for exactly this reason — items get organized later.

---

### Step 3: Handle Single vs. Multiple Items

If the member provided a single item: proceed to Step 4.

If the member provided multiple items in one message (e.g., "I need to buy groceries, also save this article about AI, and I had an idea about redesigning the onboarding flow"): parse each into a separate item. Present the parsed list:

> "I'll capture these as 3 items:
> 1. **Task:** Buy groceries
> 2. **Read later:** [article title/URL] — AI article
> 3. **Idea:** Redesign the onboarding flow
>
> Sound right?"

Accept corrections before writing. If the member says "yes" or similar, write all items.

---

### Step 4: Write

For a single, straightforward capture: write immediately without a confirmation gate. Confirm after writing:

> "Captured: **{title}** ({type}) → {list name or 'inbox'}"

If the item had any notable metadata extracted, mention it briefly:
> "Captured: **Read that article on quantum computing** (read-later, @commute) → inbox"

For the item structure in `items.json`:
```json
{
  "id": "CAP-{NNN}",
  "type": "{task|read_later|idea|reference}",
  "title": "{title}",
  "description": "{longer description if provided, else null}",
  "why": "{why this was captured, if provided}",
  "list": "{list slug or null for inbox}",
  "tags": [],
  "context": "{context or null}",
  "priority": null,
  "status": "inbox",
  "due_date": null,
  "source_url": "{URL or null}",
  "estimated_effort": "{quick|medium|long|null}",
  "linked_items": [],
  "notes": [],
  "created_date": "{today}",
  "last_updated_date": "{today}",
  "completed_date": null,
  "archived_date": null
}
```

Status defaults:
- `task` → `inbox` (becomes `active` when organized)
- `read_later` → `queued`
- `idea` → `captured`
- `reference` → `active` (references are immediately available, no lifecycle)

Increment `next_id`. Update `last_updated`. Write `items.json`.

---

### Step 5: Offer Next Actions (Briefly)

After capturing, offer very briefly — one line, not a menu:

If the item went to inbox: "Organize it now, or it'll be there for your next review."

If the member seems to be on a capture streak (multiple items in quick succession): don't offer anything — just confirm and wait for the next item. Speed matters more than organization during a capture burst.

---

## Directives

### Behavior

Speed is everything. The capture task should feel like dictation — the member talks, the item appears. Don't ask clarifying questions unless the input is genuinely ambiguous (e.g., you can't tell if it's one item or three).

For single, clear captures: write immediately, confirm after. No "should I save this?" confirmation gate.

For multiple items or ambiguous input: present the parsed interpretation briefly, get a thumbs-up, then write.

If the member provides a URL: try to extract a title and description from the URL content. If the URL is unreachable, use whatever the member said as the title.

If the member captures something and immediately provides organization ("save this article to my AI Research list at priority 2"): honor all of it in one step. Don't capture to inbox first and then organize — do it all at once.

If the member is clearly doing a brain dump ("I need to: do laundry, call the dentist, finish the proposal, review Sarah's PR, and buy birthday present for Mom"): parse all items, present the list, write on confirmation.

### Constraints

Never require metadata before capturing. The only required field is the title, which Claude derives from whatever the member says.

Never prompt for `why` during quick capture. If the member naturally provides context ("save this article — it has a great framework for thinking about pricing"), record it. If they just say "save this article," leave `why` as null.

Never re-ask about type if the member didn't specify one and your inference is reasonable. Wrong type is easily fixed later via manage-items.

### Edge Cases

If the member's capture is a single URL with no description: fetch the page title and first paragraph if reachable. Use the page title as the item title. Type: `read_later`.

If the member says something like "remind me to..." — treat as a task. If they say "I should look into..." — treat as an idea. If they say "save this for later" — treat as read-later if it's content, reference if it's information.

If `items.json` doesn't exist yet (first capture): initialize it, then proceed.

If the member captures something that sounds identical to an existing item: note it — "This looks similar to CAP-{XXX}: '{title}'. Want to add this as a new item anyway, or update the existing one?"
