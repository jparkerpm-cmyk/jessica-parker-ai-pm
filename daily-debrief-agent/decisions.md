# Daily Debrief Agent — Product Decision Log

---

## The Problem

Work happens across multiple Claude sessions, tools, and threads in a single day. By end of day, the things that moved, the decisions made, the ideas that surfaced, and the carry-overs are scattered across session context, a task manager, a messaging tool, and memory.

Manual capture is inconsistent. Existing journal or debrief templates require too much active effort to complete reliably. The friction is the failure mode.

The goal: one clean record at the end of every day, with minimal active effort from the human.

---

## What Got Built

Two agents that function as a system:

**Daily Debrief** scans before it asks, asks five questions, follows up on two more, routes ideas and tasks, writes the debrief, and generates a visual.

**Daily Debrief Merge** handles the case where the primary debrief does not capture everything because work happened across multiple sessions.

---

## Decision Log

### Scan before asking

**Decision:** the agent scans the session, messaging tool, and task manager before asking any questions.

**Why:** if the agent asks first, the human has to remember what happened. The scan does the remembering. The questions become confirmation and addition, not cold recall.

**Tradeoff:** the scan adds latency at the start. Worth it. Cold recall is the failure mode for end-of-day capture.

---

### Five questions in one message

**Decision:** all five cadence questions are sent in a single message, not one at a time.

**Why:** one message means one response pass. Multiple messages create conversational overhead and interrupt the flow of answering. The human can answer all five in one pass or skip what does not apply.

**Tradeoff:** a wall of questions can feel like a form. Kept short by design. Each question is one sentence.

---

### Wins follow-up is mandatory

**Decision:** wins are asked as a follow-up, and if skipped, the agent surfaces one or two from the session anyway.

**Why:** wins are the section most likely to be skipped when the day was hard or unremarkable. They are also the section with the most value over time as a pattern signal. Making them mandatory and surfacing them from context removes the activation energy required.

**Tradeoff:** feels forced some days. The alternative is no wins section, which is worse.

---

### Slack items surface separately

**Decision:** action items and ideas from the messaging scan are presented in their own message with explicit routing options, not bundled with the debrief questions.

**Why:** bundling creates ambiguity about what needs a response. Slack items often need a routing decision that is different from the debrief questions. Separating them keeps the decisions clean.

**Tradeoff:** adds a message to the flow. Acceptable because each message has a clear purpose.

---

### Three idea buckets

**Decision:** ideas route to one of three files: Fun, Tooling Queue, or Waiting.

**Why:** not all ideas are the same kind of deferred work. Fun is pull-when-ready. Tooling Queue is intentional build work. Waiting is blocked by an external event. Mixing them produces a list that is never actionable. Three buckets keeps routing fast and retrieval useful.

**Tradeoff:** requires a judgment call on bucket assignment. Default to Fun if unclear. Better to surface it in the wrong bucket than lose it.

---

### Writing ideas go to notes, not task manager

**Decision:** writing ideas are appended to a notes folder, not added to the task manager.

**Why:** writing ideas are not tasks with due dates. Adding them to a task manager creates false urgency and list noise. A dedicated writing ideas folder in the notes tool is searchable and pressure-free.

**Tradeoff:** ideas in notes are easier to lose than tasks. Accepted. The alternative creates a worse problem.

---

### Debrief overwrites, not appends

**Decision:** the daily debrief writes to a dated file and overwrites it each run, rather than appending.

**Why:** a debrief run mid-day would produce a partial file. Running again at end of day should produce the complete record, not two partial records stacked. Overwrite produces one clean file per day.

**Tradeoff:** if the agent errors mid-write, the file could be lost. Mitigated by the merge agent, which can reconstruct from session context.

---

### Squirrel's Desk preservation

**Decision:** when overwriting the debrief file, the agent reads and re-appends any existing `## From Squirrel's Desk` section rather than losing it.

**Why:** the morning planning section is written earlier in the day. It should persist in the same file as a complete daily record. Overwriting without preserving it loses the morning context.

**Tradeoff:** adds read-before-write complexity. Required to make the daily file a full-day record.

---

### Visual summary format is chosen by the agent

**Decision:** the agent picks the visual format (sticky notes, flowchart, or mind map) based on what dominated the day. The human does not choose.

**Why:** asking the human to choose format adds a decision to end of day. The agent has the session context to make a reasonable call. Sticky notes is the default because most days do not have a clean sequential structure.

**Tradeoff:** the agent will occasionally pick the wrong format. The cost is low. A visual in the wrong format is still useful.

---

### Merge agent is a separate skill, not a mode

**Decision:** the merge behavior is a separate agent, not a flag or mode on the primary debrief.

**Why:** the merge use case is meaningfully different. It starts by reading an existing file, not by scanning a fresh session. The question logic is different. The write behavior is different. A separate skill makes the intent explicit and reduces the risk of the merge path running when it should not.

**Tradeoff:** two files to maintain. Accepted. Coupling them would make both worse.

---

### Merge timestamps stack

**Decision:** each merge run appends a new `Merged [HH:MM]` timestamp below the original `Captured` line. Previous merge timestamps are preserved.

**Why:** multiple merge runs in a day are possible. Stacking timestamps creates a visible audit trail of when the record was updated and how many times.

**Tradeoff:** the bottom of the file gets longer over time. Acceptable. Timestamps are reference, not content.

---

## What Changed Across Versions

**Debrief v1-v3:** single-pass question and write. No scan. No visual. Relied on human recall entirely.

**Debrief v4:** added session scan before questions. Win follow-up added.

**Debrief v5:** added visual board output. Added Slack scan. Added idea routing with three buckets.

**Debrief v6:** added Squirrel's Desk preservation on overwrite. Separated writing ideas routing. Formalized the scan-then-ask sequence. Visual format selection made agent-driven.

**Merge v1-v2:** simple append to existing file. No section mapping. No visual board update.

**Merge v3:** added cadence-aware section mapping. Added visual board update step.

**Merge v4:** added timestamp stacking. Formalized the read-check-merge-overwrite sequence. Added explicit guidance on what not to move across sections without confirmation.
