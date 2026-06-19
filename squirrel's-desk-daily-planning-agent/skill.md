# Squirrel's Desk: Skill Reference

## What This Skill Does

Runs the daily briefing and planning ritual. Two parts: morning briefing and Plan My Day. Both re-fetch everything fresh. Nothing depends on session state.

---

## Part 1: Morning Briefing

### Trigger
Run at the start of the day, or any time you need to surface what matters right now.

### What It Reads
- Obsidian: yesterday's debrief note, carry-overs, active cadence items, ideas across three inbox files
- Todoist: tasks due today, overdue items, high-priority items across active projects
- Google Calendar: today's events plus the next 6 days
- Gmail: action inbox (last 24 hours), newsletters (last 24 hours), training and docs (last 24 hours)
- Weather: current conditions and today's forecast for Durham, CT
- News: four categories — Local CT, National, Political, International

### What It Produces
A synthesized briefing that surfaces:
- The One Thing: single highest-priority action for today
- Cadence items due: daily, weekly, biweekly, monthly, quarterly
- Tasks for today (linked to Todoist)
- Calendar timeline for today and coming up for the next 6 days
- Action inbox emails (real people, needs a response)
- What today looks like: one synthesized paragraph across all inputs
- Newsletters and training items for the consume zone
- Today's news across four categories

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
The artifact opens in a browser tab. Stays open all day.

**Layout order:**

**Action zone** (green tinted background — the only colored zone):
- Today's Tasks and Today's Plan side by side as two columns
- Inbox Check: full-width horizontal row of action emails below the columns
- What Today Looks Like: synthesis paragraph below inbox check

**Neutral zones** (plain cards, labeled dividers):
- Cadence: daily, weekly, biweekly, monthly rhythm tracking
- Schedule: today's timeline + coming up (next 6 days)
- Reading: From Your Inbox (newsletters, training) and Today's News
- Reference: Carrying Over and Ideas to Come Back To
- Activity Log and Send to Debrief

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
| Obsidian | Read debrief notes, cadence, carry-overs, ideas. Write back plan and activity log. |
| Todoist | Read tasks due today, overdue, high-priority. Checkbox closes tasks via REST API. |
| Google Calendar | Read today's events and next 6 days. |
| Gmail | Read action inbox, newsletters, and training items from last 24 hours. |
| Slack | Post debrief summary (debrief skill). |

---

## Related Skills

- daily-debrief: End-of-day debrief. Routes outputs to Todoist, Obsidian, and Slack.
- daily-debrief-merge: Merges additions from other project folders into the daily debrief.
- monthly-review: End-of-month review of daily debriefs. Surfaces themes, decisions, and insights.

---

## Design Notes

Built for a picture-based, ADHD brain. Cognitive load reduction is the primary design constraint.

Only the action zone gets a background color (soft green). Everything else is plain. Color does one job — signals where the work is — and gets out of the way.

The weather strip gets its own color (soft blue). Distinct from the action zone. Reads immediately as context, not work.

The inbox check is a full-width horizontal row of email cards sitting directly below Tasks and Plan. Same position it always occupied as a third column — just horizontal now so the two work columns get full width.

Every item is linked to its source. No searching required.

The Send to Debrief button on the artifact routes directly to the debrief skill. One click from end of day to debrief.

Task checkboxes close tasks in Todoist directly via REST API. No routing through an LLM. No fake DONE responses.

Weather, news, newsletters, and training are all fetched by Claude at build time and injected as constants. Nothing in the HTML live-fetches them.
