# Squirrel's Desk (Daily Planning Agent)

A daily briefing and planning system built on Claude. It reads from four live sources, makes routing and prioritization decisions, generates an HTML artifact that lives in the browser all day, and writes back to the source of truth.

Runs every morning. No manual input required.



## What It Does

**Morning briefing.** Reads from Obsidian, Todoist, Google Calendar, and Gmail. Surfaces what matters. Flags what moved overnight.

**Plan My Day.** Locks the day's priorities. Generates the HTML briefing file. Writes the plan back to Obsidian.

**HTML artifact.** Lives in a browser tab. Non-linear layout. Linked tasks, emails, and calendar items. Embedded calendar view. One-click access to every item surfaced in the briefing. This is the runtime environment for the day.

**Write-back.** The activity log posts to the Obsidian daily debrief note. Claude's memory is not the source of truth. Obsidian is.

---

## Architecture

|Layer| Detail|
|-----|-------|
|Input sources|Obsidian, Todoist, Google Calendar, Gmail|
|Output destinations|HTML briefing file, Obsidian (write-back), Slack|
|Source of truth|Obsidian|
|Runtime dependency|None. Re-fetches everything fresh on each run.|
|Session dependency|None. Plan My Day runs in any conversation, any time.|

---

## Design Constraints

Cognitive load reduction is a first-class design constraint. This system is built for a picture-based, ADHD brain. Every design decision is evaluated against one question: does this add friction to a daily-use tool?

Non-linear layout. The screen is two-dimensional. The artifact uses it.

Color for visual differentiation. No reading the label to know what section you are in.

Linked items. One click to the source. No searching.

No scrolling to find the plan. The artifact is in a browser tab. It is always there.

---

## Version History

Version
Change
V1
Created Squirrel's Desk. Morning briefing and Plan My Day.
V2
Added Gmail as a first-class input source.
V3
Added the HTML artifact. Plan lives in a browser tab, not a thread.
V4
Added Whimsical board. Served as a UX prototype for V5. Removed after.
V5
Full UX redesign. Non-linear layout. Color. Linked items. Embedded calendar. Fixed Send to Debrief. Removed Whimsical board.

---

## MCP Connections Required

- Obsidian
- Todoist
- Google Calendar
- Gmail
- Slack (debrief write-back)
- Whimsical (prior versions; removed in V5)

---

## Obsidian Is Notes Only

No skill files. No templates. No structural documents live in Obsidian. Only debrief notes, the active plan, cadence tracking, and ideas that came from real work.

---

## Built By

Jessica Parker, BSN, RN. Senior Product Manager. Clinical, technical, and product. Site: jparkerpm.com.

