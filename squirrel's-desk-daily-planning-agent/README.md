# Squirrel's Desk (Daily Planning Agent)

A daily briefing and planning system built on Claude. It reads from six live sources, makes routing and prioritization decisions, generates an HTML artifact that lives in the browser all day, and writes back to the source of truth.

Runs every morning. No manual input required.

[Squirrel's Desk live demo](https://htmlpreview.github.io/?https://github.com/jparkerpm-cmyk/jessica-parker-ai-pm/blob/main/squirrel%27s-desk-daily-planning-agent/squirrels-desk-demo.html)

---

## What It Does

**Morning briefing.** Reads from Obsidian, Todoist, Google Calendar, Gmail, weather, and news. Surfaces what matters. Flags what moved overnight.

**Plan My Day.** Locks the day's priorities. Generates the HTML briefing file. Writes the plan back to Obsidian.

**HTML artifact.** Lives in a browser tab. Stays open all day. Linked tasks, emails, and calendar items. One-click access to every item surfaced in the briefing.

**Write-back.** The activity log posts to the Obsidian daily debrief note. Claude's memory is not the source of truth. Obsidian is.

---

## Architecture

| Layer | Detail |
|---|---|
| Input sources | Obsidian, Todoist, Google Calendar, Gmail, Weather, News |
| Output destinations | HTML briefing file, Obsidian (write-back), Slack |
| Source of truth | Obsidian |
| Runtime dependency | None. Re-fetches everything fresh on each run. |
| Session dependency | None. Plan My Day runs in any conversation, any time. |

---

## HTML Artifact Layout

The artifact is divided into zones. Layout order top to bottom:

**Action zone** — soft green background. The only colored zone. Contains everything you act on.
- Today's Tasks and Today's Plan: two columns
- Inbox Check: full-width horizontal row of action emails below the columns
- What Today Looks Like: synthesis paragraph

**Cadence** — plain card. Daily, weekly, biweekly, monthly rhythm tracking.

**Schedule** — plain cards. Today's timeline, then Coming Up (next 6 days).

**Reading** — plain cards. From Your Inbox (newsletters and training), then Today's News.

**Reference** — plain cards. Carrying Over and Ideas to Come Back To.

**Activity Log** — note input, auto-logged task completions, Send to Debrief button.

---

## Design Constraints

Cognitive load reduction is a first-class design constraint. This system is built for a picture-based, ADHD brain. Every design decision is evaluated against one question: does this add friction to a daily-use tool?

**One colored zone.** The action zone is green. Everything else is plain. Color signals where the work is and gets out of the way.

**Weather gets its own color.** Soft blue. Distinct from the action zone. Reads as context, not work.

**Linked items.** One click to the source. No searching.

**No LLM in the task completion loop.** Checkboxes call the Todoist REST API directly. Fake DONE responses were the V6 bug. Fixed in V6, kept fixed since.

**All ambient content injected at build time.** Weather, news, newsletters, training. Nothing in the HTML live-fetches. Claude fetches at Plan My Day and bakes it in as constants.

---

## Version History

| Version | Change |
|---|---|
| V1 | Created Squirrel's Desk. Morning briefing and Plan My Day. Built by Stephanie Wnetrzak. |
| V2 | Added Gmail as a first-class input source. |
| V3 | Added the HTML artifact. Plan lives in a browser tab, not a thread. |
| V4 | Added Whimsical board. Served as a UX prototype for V5. Removed after. |
| V5 | Full UX redesign. Color. Linked items. Embedded calendar. Fixed Send to Debrief. Removed Whimsical board. |
| V6 | Fixed checkbox bug — tasks now close in Todoist via REST API. Gate changed from Anthropic key to Todoist token. |
| V7 | Added weather, news, newsletters, and training. Consume zone added to contain ambient content. |
| V8 | Zone-based layout redesign. Action zone tinted. Inbox Check moved to horizontal row. Synthesis moved above Cadence. Coming Up moved below Timeline. Squirrel emoji. Durham CT coordinates. |

---

## MCP Connections Required

- Obsidian
- Todoist
- Google Calendar
- Gmail
- Slack (debrief write-back)

---

## Obsidian Is Notes Only

No skill files. No templates. No structural documents live in Obsidian. Only debrief notes, the active plan, cadence tracking, and ideas that came from real work.

---

## Built By

Jessica Parker, BSN, RN. Senior Product Manager. Clinical, technical, and product. Site: jparkerpm.com.
