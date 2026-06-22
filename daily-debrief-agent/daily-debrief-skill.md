---
name: daily-debrief
description: "Runs your end-of-day debrief. Scans your session, messaging tool, and task manager before asking anything. Asks five questions covering your work tracks, admin, and carry-overs. Routes ideas and tasks. Writes a structured debrief to your notes tool. Generates a visual summary."
---

# Daily Debrief

Run my end-of-day debrief. Here is how.

---

## Configuration

Replace all `[YOUR_...]` placeholders before running this skill.

| Placeholder | What to put here |
|-------------|-----------------|
| `[YOUR_NOTES_TOOL_READ]` | MCP tool name for reading a note (e.g. `obsidian_read_note`) |
| `[YOUR_NOTES_TOOL_WRITE]` | MCP tool name for writing a note |
| `[YOUR_NOTES_TOOL_PATCH]` | MCP tool name for appending to a note |
| `[YOUR_NOTES_PATH]` | Folder path for daily debriefs (e.g. `Journal/Daily Debriefs/`) |
| `[YOUR_NOTES_IDEAS_FUN]` | Path to your fun ideas file |
| `[YOUR_NOTES_IDEAS_TOOLING]` | Path to your tooling/build ideas file |
| `[YOUR_NOTES_IDEAS_WAITING]` | Path to your waiting/blocked ideas file |
| `[YOUR_NOTES_WRITING_PATH]` | Path to your writing ideas folder |
| `[YOUR_TASK_TOOL_READ]` | MCP tool name for reading completed tasks |
| `[YOUR_TASK_TOOL_ADD]` | MCP tool name for adding a task |
| `[YOUR_TASK_LABELS]` | Your label-to-section mapping (see Step 1) |
| `[YOUR_MESSAGING_TOOL]` | MCP tool name for searching messages |
| `[YOUR_VISUAL_TOOL]` | MCP tool name for creating a visual board |
| `[YOUR_VISUAL_PARENT_ID]` | Parent folder ID in your visual tool |
| `[YOUR_WORK_TRACKS]` | The five question areas that match your work (see Step 2) |
| `[YOUR_MORNING_SECTION_HEADER]` | Header string to preserve from earlier in the file, if any |

---

## Step 1 — Scan before asking

Before asking anything, scan three places and compile quietly.

### Session scan

Look through the conversation and note anything worth capturing:

- documents written or updated
- decisions made
- work researched or evaluated
- tools built
- insights that landed

### Messaging scan

Check `[YOUR_MESSAGING_TOOL]` for all conversations from the last 24 hours.

Note anything worth capturing: documents, decisions, insights, potential action items, ideas worth keeping.

### Task manager scan

Pull today's completed tasks using `[YOUR_TASK_TOOL_READ]`.

Map task labels to debrief sections. Replace the example below with your own labels:

```
[YOUR_TASK_LABELS]

Example mapping:
Label "work-track-a"     → Work Track A section
Label "work-track-b"     → Work Track B section
Label "admin"            → Admin / Logistics section
Label "fun"              → Available When I Want Fun section
```

---

## Step 2 — Ask five questions in one message

Replace the five questions below with the work tracks that match your context. The structure matters: one question per track, all in one message.

```
[YOUR_WORK_TRACKS]

Example structure:
Track A: Anything moved here today outside our session?
Track B: Any progress, contacts, or evaluations?
Track C: What got done at your main job worth noting?
Admin: Any tasks knocked out that are not in your task manager?
Carry-over: What is sitting in your head that you have not done yet?
```

---

## Step 3 — After I respond, ask two follow-ups

> **Wins:** Anything that felt good, went well, or deserves a moment of credit?
>
> **Anything to remember:** Something that happened, something that clicked, something you do not want to lose.

If wins are skipped, surface one or two from the session anyway. Do not let them off the hook.

---

## Step 4 — Surface messaging items separately

In its own message, present everything from the messaging scan that looks like a potential action item or not-yet-formed idea.

For each one, ask: **task, idea (which bucket), or skip.**

**Tasks** get added to `[YOUR_TASK_TOOL_ADD]` with appropriate dates and labels.

**Ideas** route to one of three files using `[YOUR_NOTES_TOOL_PATCH]` in append mode:

| File | Purpose |
|------|---------|
| `[YOUR_NOTES_IDEAS_FUN]` | Novelty, side projects, pull-when-ready items |
| `[YOUR_NOTES_IDEAS_TOOLING]` | One-time builds that speed up recurring work |
| `[YOUR_NOTES_IDEAS_WAITING]` | Items blocked by an external event |

If the bucket is unclear, default to Fun. Better to surface it than lose it.

---

## Step 5 — Writing ideas

Ask if there are any ideas for posts, articles, or topics to keep in mind for future writing.

Append all writing ideas to `[YOUR_NOTES_WRITING_PATH]` using `[YOUR_NOTES_TOOL_PATCH]`.

Do not add writing ideas to the task manager. Only add actual actionable tasks with due dates there.

---

## Step 6 — Write the debrief

Path: `[YOUR_NOTES_PATH]debrief_YYYY-MM-DD.md`

Do not save a file to download. Write directly to the notes tool.

Follow these steps in order.

### 6a — Read the existing file (if any)

Call `[YOUR_NOTES_TOOL_READ]` on today's debrief path.

Store the full result. If the call errors or returns empty, the file does not exist — skip to 6c.

### 6b — Check for a morning section to preserve

Look at the stored content for `[YOUR_MORNING_SECTION_HEADER]`.

- If found: copy everything from that header to the end of the file into a holding variable. Call it `MORNING_SECTION`. Do not alter it.
- If not found: `MORNING_SECTION` is empty.

### 6c — Write the new debrief

Compile the debrief using the format below.

Call `[YOUR_NOTES_TOOL_WRITE]` with the debrief content only. This creates or overwrites the file. Do not add `MORNING_SECTION` here.

### 6d — Re-append the morning section (only if it existed)

If `MORNING_SECTION` is not empty, call `[YOUR_NOTES_TOOL_PATCH]` on the same file path with `MORNING_SECTION` as the content in append mode.

If `MORNING_SECTION` was empty, skip this step.

**Result:** the file contains the new debrief at the top and the preserved morning section at the bottom, untouched.

### Debrief format

```markdown
## Daily Debrief — [Day, Month DD, YYYY]

## What I Did Today

**In Session**
[Bullet list from the session scan — specific and concrete]

**From Messaging**
[Bullet list of substantive items from the 24-hour scan]

**Outside the Session**

**[Work Track A]**

**[Work Track B]**

**[Work Track C]**

**Admin / Logistics**

**Tasks Completed Today**



**Cadence Progress**
[What moved on recurring rhythms today. Include carry-forward visibility.
 Examples: "2 of 3 weekly outreaches done, 1 remaining", "biweekly block: complete"]

**One-Time Queue Progress**
[Anything that moved on one-time projects. Note progress, not just completion.]

**Available When I Want Fun**
[Only include if something was actually pulled from the fun pile today. Skip entirely if not.]



**Carrying Over to Tomorrow**
[Bullet list]



**Wins Today**
[From answers plus anything from the session worth crediting]



**Anything to Remember**
[Preserve voice here — this is a note to self, not a report]



Captured [HH:MM] on [Day, Month DD, YYYY]
```

After writing, confirm it landed and tell the user to check their notes.

---

## Step 7 — Visual summary

After the debrief is written, generate one visual board using `[YOUR_VISUAL_TOOL]`.

Do not ask — pick the right format and build it.

Choose based on what dominated the day:

| Format | When to use |
|--------|-------------|
| Sticky notes (default) | Most days — things happened, wins exist, loops are open, ideas came up |
| Flowchart | Day had clear sequential movement or a decision chain |
| Mind map | Last resort — unusually complex or branchy day where nothing else fits |

### Sticky notes (default)

Build four columns. Skip any column that has nothing in it.

| Column | What goes in |
|--------|--------------|
| What Moved | Completed or advanced today, including cadence and one-time progress |
| Wins | From the Wins Today section — specific, not generic |
| Open Loops | Carry-overs, unresolved, flagged for tomorrow |
| Ideas and Decisions | Ideas with bucket noted, decisions made or things cut on purpose |

### Flowchart

Show the main things that moved: what state they were in, what state they are in now. Five to eight nodes max. Only things that actually progressed.

### Mind map

Root: today's date. Branches: What Moved, Open Loops, Wins, Ideas, Decisions. Skip empty branches.

### After generating

Share the link in one line. Nothing else.
