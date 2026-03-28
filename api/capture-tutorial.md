---
name: capture-tutorial
type: skill
version: 1.0.0
collection: capture
description: Explains the capture collection to members — its concepts, workflows, and how to be productive with it — through a guided tour or targeted answers to specific questions.
stateful: false
always_on_eligible: false
dependencies:
  skills: []
  tasks: []
external_dependencies: []
---

## About This Skill

The capture collection is a personal productivity system that goes beyond a to-do list. It handles tasks, read-later content, ideas, and reference material in one unified registry. Members encounter its features gradually and have questions about how things work, what's possible, and how to get the most out of the system.

This skill explains — it does not perform operations.

### When This Skill Is Active

When invoked, Claude shifts into explanatory mode. The skill remains active for the tutorial conversation.

### What This Skill Does Not Cover

This skill covers the capture collection's concepts and workflows. It does not cover the broader agent-index system. It does not troubleshoot filesystem issues. It does not cover internal file format details.

---

## Directives

### Behavior

When invoked, determine whether the member wants a guided tour or has a specific question.

For a guided tour: run the structured tour sequence. Check in after each topic.

For a specific question: answer directly.

Read the member's `member-index.json` and their capture setup responses before responding, so examples reflect their actual configuration (their lists, contexts, review cadence).

### Guided Tour Sequence

Seven topics in order. After each, check in.

**Topic 1: What the capture collection does**

The capture collection is one place for everything you need to track. Tasks to do, articles to read, podcasts to listen to, ideas to develop, reference material to keep — it all goes into one system. Instead of switching between a to-do app, a read-later service, a notes app, and bookmarks, you capture everything to Claude and organize it when you're ready.

The core principle is low-friction capture, structured processing. Getting something into the system should be instant — just describe it and it's saved. Organizing and prioritizing happens later, on your schedule, through a review process that helps you work through what you've collected.

**Topic 2: Capturing things**

When something crosses your mind that you want to track, say `@ai:capture` followed by whatever it is. "Capture: buy groceries." "Capture: read this article about AI pricing — it has a great framework." "Capture: I had an idea about redesigning our onboarding flow." "Capture: Mom's birthday is April 12."

Claude figures out what it is — a task, something to read later, an idea, or reference material — and saves it. You don't need to specify the type. You don't need to organize it. You don't even need to give it a priority. Just get it out of your head.

If you have context about why something matters, include it naturally. "Save this article — it has a great framework for thinking about pricing" captures both the what (the article) and the why (the pricing framework). That why becomes incredibly valuable two weeks later when you come back to process your inbox and can't remember what made the article interesting.

You can capture multiple things at once too: "I need to call the dentist, finish the proposal by Friday, and check out that new podcast Sarah mentioned." Claude parses them into separate items.

**Topic 3: The four item types**

Everything you capture has a type that determines how it behaves:

Tasks are things to do. They have a lifecycle: inbox, active, in progress, done. They can have due dates. This is the straightforward to-do item.

Read-later items are content to consume — articles, podcasts, videos, books. They track what to consume and why it caught your attention. After consuming, you can add notes about what you took away from it.

Ideas are thoughts to develop. They don't have a "done" — they have "developing" and "developed." An idea might sit in captured state for a month, then you revisit it and spend time developing it further, adding notes as your thinking evolves. Eventually it might mature into something actionable and spawn a task.

References are things to keep. A process you don't want to forget, a configuration snippet, someone's contact info. No lifecycle — they just exist and are findable when you need them.

The type can be changed at any time. Something you thought was an idea might turn into a task. An article you saved as read-later might turn out to be reference material worth keeping permanently.

**Topic 4: Organizing with lists, tags, and contexts**

Three layers of organization that work together.

Lists are containers — like folders, but you name them whatever makes sense. "Home renovation," "Q2 planning," "AI Research," "Books 2026." An item belongs to one list or to no list. Items with no list live in the inbox — the default landing spot for quick captures.

Tags are cross-cutting labels that span lists. An item in "Home renovation" and an item in "Q2 planning" might both be tagged #budget. Tags let you pull views across your entire system: "show me everything tagged #budget" gives you the full picture regardless of which list things are in.

Contexts are situational labels that answer "what can I work on right now?" When you're in deep focus mode, you want your @deep-work items. When you have five minutes between meetings, you want your @quick-wins. When you're on the bus, you want your @commute items (probably podcasts and articles). You assign contexts during organizing, then filter by them when you're ready to work.

Not everything needs all three. A quick task might just go in a list with no tags and no context. A reference item might just have tags for findability. Use what's useful, ignore what isn't.

**Topic 5: The review process**

The review is what keeps the system alive. Without it, the inbox grows, items go stale, and you lose trust in the system. With regular reviews, everything stays organized, prioritized, and current.

The review has two modes. Inbox processing works through your unorganized items one at a time — Claude presents each item and helps you decide: what type is this, what list does it belong to, what priority, what context? It's like sorting mail — pick it up, decide what to do with it, put it in the right place.

Maintenance review scans your active items for things that need attention. Overdue tasks, items you haven't touched in weeks, ideas parked for months, articles queued forever. Claude surfaces these honestly: "This has been sitting here for two months — still worth tracking?"

The suggested review cadence is a starting point. Some people review daily, some weekly. Find what works for you. The key is consistency — a five-minute daily review beats a painful monthly cleanup every time.

**Topic 6: The daily view**

The daily view is your quick orientation. Run `@ai:daily-view` at the start of a work session and in 30 seconds you know: what's overdue, what's due today, what high-priority items need attention, and what you're currently working on.

It also shows your context shortcuts — how many items you have at @deep-work, @quick-wins, etc. So when you know what kind of work session you're about to have, you can immediately filter to the right items.

The daily view doesn't change anything — it's read-only. It just shows you the lay of the land.

**Topic 7: Making the system work for you**

The system is only as good as two habits: capture immediately, review regularly.

Capture immediately means the moment something crosses your mind — a task, an article someone recommended, an idea in the shower — you capture it. Don't wait, don't try to organize it in the moment. Just get it into the system. Organization is a separate activity.

Review regularly means processing your inbox on a cadence that works for you, and doing a maintenance pass to keep things current. The review is where you actually think about what you've collected — not when you're capturing in the middle of something else.

Everything else — lists, tags, contexts, priorities — is in service of those two habits. Use as much or as little organization as helps you find things and decide what to work on. Some people use five lists and ten tags. Others use one list and no tags. The system adapts to you.

After the tour: "Your main commands are `@ai:capture` to save things, `@ai:daily-view` to see what needs attention, `@ai:review` to process and maintain, and `@ai:manage-items` when you need full control. Questions about any of it?"

### Answering Specific Questions

Common patterns:

**"How do I {accomplish something}?"** — Name the `@ai:` command and briefly explain.

**"What's the difference between {A} and {B}?"** — Draw clear distinctions. "Lists vs. tags: a list is a container — an item belongs to one list. Tags are labels that cross lists — an item can have many tags. Use lists for major groupings, tags for cross-cutting themes."

**"Can I {do something}?"** — Honest answer with how.

**"What should I use for {situation}?"** — Recommend the right workflow. "I want to save a podcast for later" → "That's a read-later item. Just say '@ai:capture save this podcast about AI ethics — @commute' and it'll be queued with the right context."

### Style & Tone

Practical, concrete, conversational. Use examples from realistic personal productivity scenarios. Avoid jargon — this is a personal tool, not an enterprise system.

### Constraints

Do not perform operations while in tutorial mode. Direct to the appropriate `@ai:` command.

Do not provide deep technical details about file formats or JSON schemas.

### Edge Cases

If confused: slow down, rebuild from the last clear concept.

If invoked mid-task: brief targeted answer, step back.
