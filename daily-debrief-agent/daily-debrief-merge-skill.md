---
name: daily-debrief-merge
description: "Merges new material from the current session into the existing daily debrief. Pulls the debrief from your notes tool, scans the session for anything new, maps it to the correct sections, and overwrites the file with the clean merged version. Also updates the visual board."
---

# Daily Debrief Merge

Run when work happened across multiple sessions and the primary debrief does not capture all of it. Here is how.

---

## Configuration

Uses the same placeholders as the daily-debrief skill. Replace all `[YOUR_...]` values before running.

| Placeholder | What to put here |
|-------------|-----------------|
| `[YOUR_NOTES_TOOL_READ]` | MCP tool name for reading a note |
| `[YOUR_NOTES_TOOL_WRITE]` | MCP tool name for writing a note |
| `[YOUR_NOTES_PATH]` | Folder path for daily debriefs |
| `[YOUR_VISUAL_TOOL_SEARCH]` | MCP tool name for searching visual boards |
| `[YOUR_VISUAL_TOOL_FETCH]` | MCP tool name for fetching board content |
| `[YOUR_VISUAL_TOOL_EDIT]` | MCP tool name for editing a board |
| `[YOUR_VISUAL_PARENT_ID]` | Parent folder ID in your visual tool |
| `[YOUR_WORK_TRACKS]` | Your section names, same as in the primary skill |

---

## Step 1 — Pull the existing debrief and scan the session

Use `[YOUR_NOTES_TOOL_READ]` to fetch:

```
[YOUR_NOTES_PATH]debrief_[today's date YYYY-MM-DD].md
```

If the file does not exist, create it using `[YOUR_NOTES_TOOL_WRITE]` at that path with this skeleton, then continue:

```
debrief_[YYYY-MM-DD]

[Work Track A]
—

[Work Track B]
—

[Work Track C]
—

Admin / Logistics
—

Cadence Progress
—

One-Time Queue Progress
—

Available When I Want Fun
—

Carrying Over to Tomorrow
—

Captured [HH:MM] on [Day, Month DD, YYYY]
```

Read it fully before doing anything else.

Then scan the current session and note anything worth capturing:

- documents written or updated
- decisions made
- work researched or evaluated
- tools built
- insights that landed

Note only what is new or different from what is already in the debrief. Compile quietly.

---

## Step 2 — Map new material to debrief sections

When scanning new material from this session, map each item to the section it belongs in:

```
[YOUR_WORK_TRACKS]

Example mapping:
Work Track A items     → Work Track A section
Work Track B items     → Work Track B section
Work Track C items     → Work Track C section
Admin items            → Admin / Logistics section
Recurring rhythm items → Cadence Progress section
One-time project items → One-Time Queue Progress section
Fun pile items         → Available When I Want Fun section
New carry-overs        → Carrying Over to Tomorrow section
```

---

## Step 3 — Merge quietly

For each section of the existing debrief, check what the new material adds or changes.

Expand where there is more detail. Adjust where something is inaccurate or incomplete.

Do not remove anything unless new material directly contradicts or replaces it. Keep one clean record. Preserve the user's voice throughout.

If a section does not exist in the current debrief but new material warrants it, add it in the correct position per the format order in the primary skill.

Do not move items across sections without confirming with the user.

---

## Step 4 — Overwrite the file

Use `[YOUR_NOTES_TOOL_WRITE]` to overwrite the file at:

```
[YOUR_NOTES_PATH]debrief_[today's date YYYY-MM-DD].md
```

Do not create a new file. Do not save a download.

### Timestamp tracking

Add a new line `Merged [HH:MM] on [Day, Month DD, YYYY]` under the original `Captured` line at the bottom.

If a previous `Merged` line exists, add the new one below it. Keep all previous `Captured` and `Merged` timestamps.

After writing, confirm it landed and tell the user to check their notes.

---

## Step 5 — Update the visual board

This step runs after the debrief is written. Do not skip it.

### 5a — Find today's board

Search for today's visual board using `[YOUR_VISUAL_TOOL_SEARCH]`.

Search term: today's date formatted as the board title set by the primary skill.

If no board is found, note it and skip the rest of Step 5. Do not create a new board.

### 5b — Fetch current board content

Use `[YOUR_VISUAL_TOOL_FETCH]` on the board found in 5a.

Read the current contents of each column:

- What Moved
- Wins
- Open Loops
- Ideas and Decisions

Note what is already there. Do not duplicate anything.

### 5c — Identify what to add

Review the new material from Step 1. Map each item to the column it belongs in.

**What Moved** — anything completed or advanced, including cadence and one-time progress items.

**Wins** — anything that felt good or deserves credit, not already captured.

**Open Loops** — anything unresolved, deferred, or flagged that is not already listed.

**Ideas and Decisions** — ideas or decisions from this session, including idea bucket assignments.

If there is nothing new to add to any column, note it and skip 5d.

### 5d — Add new items

Use `[YOUR_VISUAL_TOOL_EDIT]` to add the new items to the existing board.

- Add only to columns that have new content
- Leave all existing content exactly as it is
- New items go at the bottom of their respective columns
- Keep each item short — one line
- Match the style of what is already on the board

After editing, share the board link in one line. Nothing else.
