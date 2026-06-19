# Squirrel's Desk: Decision Log

## What This Is

Squirrel's Desk is a daily briefing and planning system built on Claude. It reads from six live sources, synthesizes across them, makes routing decisions, generates an HTML artifact, and writes back to the source of truth. It runs every day.

This document records the decisions made across eight versions and the reasons behind them.

---

## System Overview

Input sources: Obsidian, Todoist, Google Calendar, Gmail, Weather, News
Output destinations: HTML briefing file, Obsidian (write-back), Slack
Source of truth: Obsidian
Runtime: Any conversation. No session dependency. Re-fetches everything fresh on each run.

---

## Version History and Decisions

### V1 — Original Build (Stephanie Wnetrzak PM)

**What was built:** Morning briefing that surfaces what matters. Plan My Day locks the plan and generates the HTML file.

**Built by:** Stephanie Wnetrzak. This is the foundation all subsequent versions iterated on.

**Constraint established in V1:** Plan My Day can run in any conversation, any time, because it always re-fetches fresh from Obsidian. Nothing depends on being in the same session as the morning briefing.

---

### V2 — Added Email

**What changed:** Added Gmail check to the morning briefing.

**Why:** The briefing was missing a real input. Email is where the day's interruptions live. Without it, the plan was incomplete before it started.

**Decision:** Treat email as a first-class input source alongside calendar and tasks. Not a separate step. Not optional.

---

### V3 — Added HTML Widget

**What changed:** Generated a scoped HTML briefing file instead of reading the plan from a thread.

**Why:** Searching for the plan in a chat thread is friction. The plan needs to live somewhere you can open without thinking. Browser tab. Always there.

**Decision:** The artifact is the deliverable. The thread is the process. Separate them.

---

### V4 — Added Whimsical Board

**What changed:** Added a visual Whimsical output to the debrief.

**Why:** The debrief needed a visual format. Text-only output was not holding.

**Outcome:** The Whimsical read-out was the inspiration for the V5 redesign. It showed what the HTML artifact was missing. Removed in V5 once the HTML incorporated the visual thinking.

**Decision in hindsight:** The Whimsical board was a prototype for V5 UX, not a permanent destination. Recognized and removed once it had served its purpose.

---

### V5 — Full UX Redesign

**What changed:** Rebuilt the HTML artifact from the ground up.

**Driver:** The previous layout was linear and required significant scrolling. For a picture-based, ADHD brain, linear scroll is friction by design. The artifact is used every day. Friction compounds.

**Decisions made:**

- Non-linear layout. The screen is two-dimensional. Use it.
- Color added. Visual differentiation between sections without reading the labels.
- Linked task items, emails, and calendar items. One click to the source. No searching.
- Calendar view embedded. The day's shape visible in the artifact, not in a separate tab.
- "The One Thing" redesigned. Removed header styling. Made it visually distinct as the single priority.
- "Send to Debrief" button fixed. Broken in prior versions.
- Title added: Squirrel's Desk. The window has an identity.
- Whimsical board removed. The HTML artifact absorbed what it was providing.

**Principle behind V5:** Cognitive load reduction is a design constraint, not a preference. Every friction point in a daily-use tool is a compounding tax. V5 treated the ADHD and dyslexic brain as the primary user, not an edge case.

---

### V6 — Checkbox Bug Fix + Gate Change

**What changed:** Task checkbox behavior fixed. Gate credential changed.

**The V6 bug:** Checkboxes were routing task completion through an LLM call that returned a fake DONE and never touched Todoist. Tasks appeared closed and were not.

**Fix:** Checkboxes now call `POST https://api.todoist.com/rest/v2/tasks/{id}/close` directly with a Bearer token. 204 means done. No LLM in the loop.

**Gate change:** The file no longer calls the Anthropic API anywhere. The gate now collects the Todoist API token instead of the Anthropic key. One credential, entered once, does real work.

---

### V7 — Consume Zone + New Content Sources

**What changed:** Added weather, news, newsletters, and training as content sources. Added a consume zone to contain them.

**Why:** The briefing was action-only. There was no place for ambient information — news, reading material — that contextualizes the day without demanding response. Adding it to the action zone would have polluted the signal. A separate zone after the action work keeps the top of the board clean.

**Decisions made:**

- Weather strip added directly under the header. Glanceable, one line, actionable note only.
- Today's News card: four categories in a 2x2 grid. Local CT, National, Political, International.
- From Your Inbox card: newsletters and training docs pulled from Gmail, separated from the action inbox.
- Consume zone placement: after What Today Looks Like, before Carrying Over. Read the world after you've handled it.
- Inbox order within consume zone: newsletters and training before news. Read what's in your world before reading about the world.
- All weather, news, and email content fetched by Claude at build time and injected as constants. Nothing in the HTML live-fetches.

---

### V8 — Layout Redesign + Location Fix

**What changed:** Restructured the HTML layout. Introduced zone-based visual design. Fixed location.

**Driver:** The V7 layout had too many borders at too many levels — section wrappers, cards inside them, row dividers inside cards. Nothing stood out because everything had the same visual weight.

**Decisions made:**

- **Zone-based design.** Only the action zone gets a background color (soft green `#EEF5E8`). All other zones are plain, separated by a thin labeled divider. Color signals where the work is and gets out of the way everywhere else.
- **Weather strip gets its own color** (soft blue `#EAF3FA`). Distinct from the action zone. Reads as context, not work.
- **Inbox Check moved from third column to horizontal row.** Tasks and Plan now get full column width. The inbox check sits below them as a full-width row of horizontal email cards, still in the action zone, same position in the page hierarchy.
- **Synthesis (What Today Looks Like) moved up.** Now sits below Inbox Check, above Cadence, inside the action zone. It is the conclusion of the action block, not a transition into the consume zone.
- **Coming Up moved below Timeline.** Calendar context flows: today's timeline, then what's next. Previously Coming Up was buried in a column.
- **Squirrel emoji added to header title.** 🐿️ Squirrel's Desk. Matches the artifact's identity.
- **Location corrected.** Durham, CT (41.4865, -72.6793). Was incorrectly set to Hartford area coordinates.

**Principle behind V8:** Visual weight should be proportional to importance. One color. One tint. Everything else gets out of the way.

---

## Standing Constraints

Obsidian is for notes only. No skill files, no templates, no structural documents. Only debrief notes, the plan, the cadence, and ideas that came from real work.

The system writes back to Obsidian daily. The activity log lives in the debrief note, not in Claude's memory.

Plan My Day has no session dependency. It re-fetches everything fresh. Stale state is not possible.

Task checkboxes write directly to Todoist via REST API. No LLM in the completion loop.

---

## What Has Not Changed

The two-part structure: morning briefing surfaces what matters, Plan My Day locks the plan.
Obsidian as the source of truth.
The HTML artifact as the daily runtime environment.
Cognitive load reduction as the primary design constraint.
