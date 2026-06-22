# Daily Debrief Agent

An end-of-day capture system built on Claude. Two agents, one problem: at the end of a fragmented day, nothing gets lost and there is one clean record.

---

## The Problem

Work happens across multiple sessions, tools, and threads. By end of day, the things that moved, the decisions made, the ideas that came up, and the carry-overs are scattered. A manual debrief catches some of it. Most capture systems require too much friction to use consistently.

This system automates the scan, asks the questions that matter, writes the record, and generates a visual summary. The human answers five questions. The agent does the rest.

---

## Two Agents

### Daily Debrief

The primary agent. Runs once at end of day.

**What it does:**
- Scans the session, a connected messaging tool, and a connected task manager before asking anything
- Asks five questions covering work tracks, admin, and carry-overs
- Follows up on wins and anything worth remembering
- Surfaces action items and ideas from the message scan separately, with routing options
- Asks for writing ideas
- Writes a structured debrief to a connected notes tool
- Generates a visual summary in a connected diagramming tool

**What goes in:** your answers to five questions

**What comes out:** one structured debrief note, one visual board, any new tasks routed to your task manager, any ideas routed to the right bucket in your notes tool

### Daily Debrief Merge

The secondary agent. Runs when work happened across multiple sessions or threads and the primary debrief does not capture all of it.

**What it does:**
- Pulls the existing debrief from the notes tool
- Scans the current session for anything new
- Maps new material to the correct debrief sections
- Merges without losing anything already captured
- Overwrites the file with the clean merged version
- Appends new items to the existing visual board

**What goes in:** the current session context

**What comes out:** the same debrief file, updated and timestamped, the same visual board with new items added

---

## Tool Stack

This system connects to four tools. Swap in your equivalents.

| Role | Tool Used | What It Does Here |
|------|-----------|-------------------|
| Notes | Obsidian (via MCP) | Stores the debrief file and idea buckets |
| Task manager | Todoist (via MCP) | Pulls completed tasks, adds new ones |
| Messaging | Slack (via MCP) | Scans the last 24 hours for capture |
| Visual board | Whimsical (via MCP) | Generates the daily picture |

---

## Debrief Structure

```
## Daily Debrief — [Day, Month DD, YYYY]

## What I Did Today

In Session with Claude
From Slack (or your messaging tool)
Outside the Session

[Work tracks — configure for your context]

Todoist Tasks Completed Today (or your task manager)

Cadence Progress
One-Time Queue Progress
Available When I Want Fun

Carrying Over to Tomorrow

Wins Today

Anything to Remember

Captured [HH:MM] on [Day, Month DD, YYYY]
```

---

## Configuration

Before running either skill, replace all placeholders with your own values. Placeholders are marked with `[YOUR_...]` throughout the skill files.

Key things to configure:

- Notes tool paths and folder structure
- Task manager labels and how they map to debrief sections
- Messaging tool and search scope
- Visual board tool and parent folder ID
- Work tracks (the five question areas) to match your actual work

---

## Files

| File | Purpose |
|------|---------|
| `README.md` | This file |
| `decisions.md` | Product decision log |
| `daily-debrief-skill.md` | Primary agent prompt |
| `daily-debrief-merge-skill.md` | Merge agent prompt |

---

## Version

Daily Debrief: v6
Daily Debrief Merge: v4
