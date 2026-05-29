# Squirrel's Desk: Decision Log

## What This Is

Squirrel's Desk is a daily briefing and planning system built on Claude. It reads from four live sources, synthesizes across them, makes routing decisions, generates an HTML artifact, and writes back to the source of truth. It runs every day.

This document records the decisions made across five versions and the reasons behind them.

---

## System Overview

Input sources: Obsidian, Todoist, Google Calendar, Gmail
Output destinations: HTML briefing file, Obsidian (write-back), Slack
Source of truth: Obsidian
Runtime: Any conversation. No session dependency. Re-fetches everything fresh on each run.

---

## Version History and Decisions

### V1 — Created Squirrel's Desk

**What was built:** Morning briefing that surfaces what matters. Plan My Day locks the plan and generates the HTML file.

**Decision:** Build the briefing before the debrief. The morning artifact is what lives in the browser all day and drives behavior. Start there.

**Constraint established:** Plan My Day can run in any conversation, any time, because it always re-fetches fresh from Obsidian. Nothing depends on being in the same session as the morning briefing.

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

Non-linear layout. The screen is two-dimensional. Use it. Components arranged spatially, not in a vertical stack.

Color added. Visual differentiation between sections without reading the labels.

Linked task items, emails, and calendar items. One click to the source. No searching.

Calendar view embedded. The day's shape visible in the artifact, not in a separate tab.

"The One Thing" redesigned. Removed header styling. Made it visually distinct as the single priority, not a section title.

"Send to Debrief" button fixed. Broken in prior versions. Fixed in V5.

Title added: Squirrel's Desk. The window has an identity.

Whimsical board removed. No longer needed. The HTML artifact absorbed what it was providing.

**Principle behind V5:** Cognitive load reduction is a design constraint, not a preference. Every friction point in a daily-use tool is a compounding tax. V5 treated the ADHD and dyslexic brain as the primary user, not an edge case.

---

## Standing Constraints

Obsidian is for notes only. No skill files, no templates, no structural documents. Only debrief notes, the plan, the cadence, and ideas that came from real work.

The system writes back to Obsidian daily. The activity log lives in the debrief note, not in Claude's memory.

Plan My Day has no session dependency. It re-fetches everything fresh. Stale state is not possible.

---

## What Has Not Changed

The two-part structure: morning briefing surfaces what matters, Plan My Day locks the plan.
Obsidian as the source of truth.
The HTML artifact as the daily runtime environment.
