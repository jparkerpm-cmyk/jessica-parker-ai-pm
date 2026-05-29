# Squirrel's Desk: Skill Reference

## What This Skill Does

Runs the daily briefing and planning ritual. Two parts: morning briefing and Plan My Day. Both re-fetch everything fresh. Nothing depends on session state.

---

## Part 1: Morning Briefing

### Trigger
Run at the start of the day, or any time you need to surface what matters right now.

### What It Reads
- Obsidian: yesterday's debrief note, any carry-overs, active cadence items
- Todoist: tasks due today, overdue items, high-priority items across active projects
- Google Calendar: today's events, any conflicts or back-to-backs worth flagging
- Gmail: unread threads from the last 24 hours, anything that changes today's priorities

### What It Produces
A synthesized briefing that surfaces:
- The One Thing: single highest-priority action for today
- Cadence items due: daily, weekly, biweekly, monthly, quarterly
- Tasks for today (linked to Todoist)
- Calendar events for today (linked)
- Emails that need action (linked)
- Anything that moved overnight

### What It Does Not Do
It does not lock the plan. That is Part 2.

---

## Part 2: Plan My Day

### Trigger
After reviewing the briefing, or any time you are ready to lock the day.

### What It Does
1. Takes the briefing output and your input on what you want to prioritize
2. Scopes the day: what is on, what is off, what gets moved
3. Generates the HTML briefing file
4. Writes the plan back to Obsidian

### HTML Artifact
The artifact opens in a browser tab. Non-linear layout. Everything linked. Stays open all day.

Contents:
- The One Thing (visually distinct, not a header)
- Task list with Todoist links
- Email list with Gmail links
- Calendar view for the day
- Send to Debrief button

### Write-Back
The plan and activity log post to the Obsidian daily debrief note for today.

---

## Runtime Rules

**No session dependency.** Plan My Day can run in any conversation, any time. It always re-fetches from Obsidian fresh.

**No stale state.** There is no memory of yesterday's plan. Obsidian is the source of truth.

**Obsidian is notes only.** No skill files, templates, or structural documents live in Obsidian. Only debrief notes, the active plan, cadence tracking, and ideas from real work.

---

## MCP Requirements

| Tool | Use |
|---|---|
| Obsidian | Read debrief notes, cadence, carry-overs. Write back plan and activity log. |
| Todoist | Read tasks due today, overdue, high-priority. |
| Google Calendar | Read today's events. |
| Gmail | Read unread threads from last 24 hours. |
| Slack | Post debrief summary (debrief skill). |

---

## Related Skills

- daily-debrief: End-of-day debrief. Routes outputs to Todoist, Obsidian, and Slack.
- daily-debrief-merge: Merges additions from other project folders into the daily debrief.
- monthly-review: End-of-month review of daily debriefs. Surfaces themes, decisions, and insights.

---

## Design Notes

Built for a picture-based, ADHD brain. Cognitive load reduction is the primary design constraint.

The HTML artifact uses the full screen non-linearly. Color differentiates sections without requiring you to read labels. Every item is linked to its source. No searching required.

The Send to Debrief button on the artifact routes directly to the debrief skill. One click from the end of the day to the debrief.

V5 removed the Whimsical board output. The HTML artifact absorbed the visual function it was serving. Fewer destinations, same coverage.
