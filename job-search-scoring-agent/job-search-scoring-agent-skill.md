---
name: jd-evaluator
description: "evaluates job descriptions against inlined criteria, scores ownership/process/leadership/work, researches the company, and recommends pursue/conditional/skip. Searches Indeed, Dice, and ZipRecruiter for new postings against the same criteria. Tracks application outcomes over time."
---

JD Evaluator

Trigger Phrases

- evaluate jd [text, URL, or filename]
- jd [text, URL, or filename]
- review jd [text, URL, or filename]

If a URL is provided, fetch the page first. If a file is attached, read it. If text is pasted, work from that.



Rubric Version

RUBRIC_VERSION: 2026.05.28

This is the version of the inlined criteria (stop list, green flags, scoring rubric, banned words) used for every evaluation. When you change any of those, update this date to today.

Every save stamps this version into the Rubric Version field of the saved row. That lets you trace which postings were scored by which version of the rubric. If criteria change mid-pipeline, the outcomes stay attributable to the version that produced them.



Reference Files

All files live under /Users/jessicaparker/Claude Cowork/About Me/.

File
Purpose
jessica_writingstyle_guide.md
Voice rules for any drafted output
jessica_parker_resume_1_docx.pdf
Polished resume. Map JD requirements to her actual experience.
cv.md
Full career history. Use when the JD calls for something the polished resume cut for length.
aboutme.md
Working profile. Context on how she operates and the 100% room baseline.

If any file is missing at the expected path, stop and tell Jessica which one before proceeding.



Two-Phase Loading

Phase 0 — zero file reads.
The stop list and green flags are inlined. Run both against the JD text before opening any file. A hard stop or a green flag fail exits here with no file I/O.

Phase 1 — load on first score.
When a JD reaches Step 3 (scoring), load:
jessica_writingstyle_guide.md

Phase 2 — load on experience match only.
When a JD reaches Step 5 (score ≥ 24), load:
jessica_parker_resume_1_docx.pdf
cv.md
aboutme.md

Session cache rule: if these were already read earlier in this session, do not re-read them.



Step 1 — Stop List (Phase 0, no file reads)

Run before anything else. Check verbatim against these seven hard stops:

BRD anywhere in the posting
Stakeholder alignment listed as a primary responsibility
Approval process or governance in day-to-day responsibilities
"Collaborative" used more than twice (count it)
JD written by HR with no product vocabulary
Large enterprise, health system, or payer as the primary company type (unless explicitly a startup inside it)
Two PMs listed as owning the same product

One hit ends the review. Do not rationalize past it.

Output on hard stop — short form only:
STOP. [Company] – [Role]
Hard stop: [which item] — "[exact quote from JD]"
Recommendation: Skip
[One sentence on why this matters for Jessica]
Do not score. Do not research. Do not read any files.



Step 2 — Green Flag Count (Phase 0, no file reads)

If the stop list passes, count green flags from this list:

Product trio: PM, engineering, design as the core working unit
"Own" or "ownership" used specifically about the product and roadmap
Trade-offs mentioned as part of the decision-making work
Small or growing team
Technical or clinical founder
AI as a product capability being built (not "we use ChatGPT internally")
Discovery and delivery both listed as PM responsibilities
Salary range that is real and in her target band

Quote the JD text for each flag found.

Fewer than 3 flags → skip. Output:
[Company] – [Role]
Green flags: [n]/8 — [name the 1–2 that hit, or "none"]
Missing: [top 2–3 gaps]
Recommendation: Skip
Do not score. Do not read any files.



Step 3 — Score the Four Dimensions (load style guide only)

Score 1–10 for each dimension.

Ownership
10: One product, hers, she defines what gets built, owns the roadmap, budget gets approved not product calls
5: Ambiguous ownership, shared decision-making, "partnering with leadership on roadmap"
1: Implementation role, executive sign-off on user stories, BRD-driven

Process
10: Discovery and delivery both PM responsibilities, sprint ceremonies right-sized, no governance theater
5: Mentions agile but unclear how it runs in practice
1: Waterfall dressed as agile, full-day planning, 15+ gate approvals

Leadership
10: Technical or clinical founder, Head of Product with real PM background, PMs make trade-off calls
5: Leadership background unclear or mixed
1: Sales-founder calling product shots, "Head of Product" with no product history, layered approvals

Work
10: High-novelty, high-stakes, diagnostic work, real ownership surface area
5: Mix of interesting and routine, surface area unclear
1: Maintenance role, ticket-taker, no diagnostic work in scope

Total: x/40.

Score < 20 → stop here. Output short skip:
[Company] – [Role]
Scores: Ownership [x] | Process [x] | Leadership [x] | Work [x] | Total [x]/40
Top reason: [one sentence]
Recommendation: Skip
Do not run company research, experience match, or interview questions.



Step 4 — Company Research (web searches, no new file reads)

Run only if score ≥ 20. Run all searches in parallel:

Stage: funding round, total raised, headcount range, year founded
Leadership: CEO, Head of Product, CTO backgrounds (clinical, technical, sales, or other)
AI signal: is AI in the product or internal tooling? Quote evidence from site, blog, or recent news.
Recent activity: launches, hires, funding, layoffs in the last 12 months
Salary signal: cross-check Levels.fyi, Glassdoor, and Built In for the role and location band
Stability flags: recent restructures, leadership churn, mass layoffs

If company research surfaces a blocker (sales-founder running product, mass layoffs in last 6 months, no AI in product despite the title), the score may drop below threshold. Adjust recommendation accordingly.



Step 5 — Experience Match (load resume + cv.md, score ≥ 24 only)

Cross-reference JD requirements against jessica_parker_resume_1_docx.pdf and cv.md:

Strong matches: JD requirement → specific role + result from her resume (quote both)
Stretch matches: adjacent experience, note what bridges the gap
Gaps: honest assessment; flag structural gaps (certification, specific industry, tool stack she has never touched) as real concerns



Step 6 — Interview Questions (score ≥ 24 only)

Use these six base questions. Tailor each with a specific hook from this JD before outputting.

On ownership: Walk me through the last time a PM pushed back on an executive request. What happened? No specific example = executive won.
On agile: How many people are in sprint ceremonies? Over 15 is a flag. Who can cancel or change a sprint?
On product leadership: How did your Head of Product get into product management? Blank or buzzword = product is not valued.
On decision-making: What does it look like when a PM disagrees with roadmap direction? Give me a recent example. Generalities mean the system is not legible.
On future boss: What makes a PM hard to manage? Listening for self-awareness and whether they trust PMs to lead.
On AI: Is AI a product capability you are building or a tool your team uses internally? You want building.



Step 7 — Recommendation

Three options. Pick one.

Outcome
Criteria
Pursue
Total ≥ 30 AND no soft stops AND clean company research
Conditional
Total 24–29 OR one-two soft flags an interview question can resolve
Skip
Stop list hit, < 3 green flags, total < 24, or company research surfaces a blocker



Tiered Output Format

Output scales with how far the evaluation ran.

Tier 1 — Hard Stop
STOP. [Company] – [Role] – [URL or "pasted JD"]
Hard stop: [item triggered] — "[quote]"
Recommendation: Skip
[One sentence reason]

Tier 2 — Green Flag Fail or Low Score (< 20)
[Company] – [Role] – [URL or "pasted JD"]
Recommendation: Skip | Total: [x]/40 or Green flags: [n]/8

[2–4 lines: what failed and why it matters for Jessica]

Tier 3 — Skip with Scores (20–23, after company research)
[Company] – [Role] – [URL or "pasted JD"]
Recommendation: Skip | Total: [x]/40

Scores
  Ownership [x]/10 — [one-line reasoning]
  Process   [x]/10 — [one-line reasoning]
  Leadership [x]/10 — [one-line reasoning]
  Work       [x]/10 — [one-line reasoning]

Company Research (brief)
  Stage: [funding/headcount] | AI Signal: [Product / Tool / Unclear] | Stability: [flags or none]

[One paragraph recommendation reasoning]

Tier 4 — Full Evaluation (Pursue or Conditional, score ≥ 24)
[Company] – [Role] – [URL or "pasted JD"]
Recommendation: [Pursue / Conditional] | Total: [x]/40

Stop List: Passed — no hard stops found

Green Flags Found ([n]/8)
  [Flag]: "[quote]"
  ...

Scores
  Ownership  [x]/10 — [one-line reasoning with JD quote]
  Process    [x]/10 — [one-line reasoning]
  Leadership [x]/10 — [one-line reasoning]
  Work       [x]/10 — [one-line reasoning]

Company Research
  Stage: [funding round, raised, headcount, founded]
  Leadership: [CEO background] | [Head of Product background] | [CTO background]
  AI Signal: [Product / Tool / Unclear] — [evidence]
  Recent activity: [funding, launches, hires, news]
  Salary signal: [Levels/Glassdoor/Built In range] vs JD posted range [if present]
  Stability: [flags or none]

Experience Match
  Strong matches:
    [JD requirement] → [role + result from resume]
  Stretch matches:
    [JD requirement] → [what bridges the gap]
  Gaps:
    [JD requirement] → [assessment]

Interview Questions
  Ownership:         [question with JD hook]
  Agile:             [question with JD hook]
  Product leadership:[question]
  Decision-making:   [question with JD hook]
  Future boss:       [question]
  AI:                [question with JD hook]

Recommendation
[One paragraph. Direct. Why pursue or conditional. If conditional, list exactly what to clarify.]



Save Trigger

save [company name]

Saves the evaluated JD to two places simultaneously.

Save 1 — Notion
Logs to the Job Search Pipeline database. If the database ID is not available, prompt once and remember for the session.

Save 2 — Excel
Creates or appends to job-search-pipeline.xlsx in /Users/jessicaparker/Claude Cowork/Outputs/. Append only — never overwrite. Present file as downloadable after save.

Fields (same column order in xlsx):
Company | Role Title | JD URL or Source | Recommendation | Total Score | Ownership Score | Process Score | Leadership Score | Work Score | Stop List Result | Green Flag Count | Stage | Leadership Background | AI Signal | Salary Signal | JD Posted Salary | Stability Flags | Interview Questions (semicolon-separated) | Recommendation Reasoning | Rubric Version | Date Evaluated (YYYY-MM-DD) | Status (default: Not applied) | Status Date | Applied Date | Outcome Notes

Field rules at save time:
- Rubric Version: auto-stamp from the RUBRIC_VERSION line at the top of this skill (e.g., 2026.05.28)
- Date Evaluated: today's date in YYYY-MM-DD format
- Status: default "Not applied"
- Status Date: today's date (the date this status was set)
- Applied Date: blank at save time
- Outcome Notes: blank at save time

Excel rules: Sheet name: Job Search Pipeline. Row 1: bold, frozen. Column widths: auto-fit, minimum 15 characters. No formulas.

After both saves: Saved — [Company] – [Role] added to Notion and job-search-pipeline.xlsx (rubric version [version]).



Outcome Tracking

Trigger phrases:
- outcome [company] [status]
- update [company] [status]

Examples:
- outcome Truepill phone screen
- update Cencora rejected
- outcome GoodRx offer accepted

When triggered, find the existing row in both Notion and Excel by company name match. If multiple rows match (same company, different roles), prompt for which one before updating.

Update these fields:
- Status: the new status
- Status Date: today's date in YYYY-MM-DD format
- Applied Date: today's date if Status was "Not applied" and is moving to any other status (set once, on first transition out of "Not applied")
- Outcome Notes: prompt for optional notes if not included in the trigger. Append to existing notes with a date prefix, never overwrite.

Valid statuses:
Not applied (default for new evaluations)
Applied
Acknowledged
Phone screen
Interview
Final round
Offer
Offer accepted
Offer declined
Rejected
Ghosted
Withdrew

Plus from Save Search:
Filtered (results dropped in fast filter)
Low Signal (results with 0–2 green flags)

Confirm after update:
Updated — [Company] [Role]: [old status] → [new status] ([new status date])

If the company name does not match any existing row, do not create a new row. Output:
No evaluation found for [Company]. Did you mean a different company, or do you need to evaluate this JD first?



Voice Rules

Direct. Confident. Short sentences. Fragments OK.

Banned: may, just, very, really, basically, probably, certainly, could, maybe, utilize, leverage, impactful, innovative, exciting, groundbreaking, game-changer, delve, embark, craft, imagine, realm, revolutionize, tapestry, illuminate, landscape, literally, actually, furthermore, hence.

No em dashes. No semicolons in prose. No hashtags. No asterisks in body copy. No softening. No hedging. If a JD is a skip, say it is a skip.





Job Search Function

Trigger Phrases

search jobs [optional keywords]
find jobs [optional keywords]
hunt [optional keywords]

Default keywords (if none provided): titles "Senior Product Manager" OR "Principal Product Manager", remote, full-time, health tech OR clinical OR AI.



Step A — Search All Three Platforms (parallel)

Indeed
search: [keyword or default title]
location: "remote"
country_code: "US"
job_type: "fulltime"

Dice
Run two searches in parallel: keyword "Product Manager" and keyword "Principal Product Manager". Do NOT add health tech, clinical, or AI as Dice keywords — those qualifiers return engineering roles, not PM roles. Filter for health relevance manually after retrieval.
workplace_types: ["Remote"]
employment_types: ["FULLTIME"]
posted_date: "SEVEN"

ZipRecruiter
query: [keyword or default title]
location_types: ["REMOTE"]
employment_types: ["FULL_TIME"]
seniority_classes: ["SENIOR"]
country_admin_code: "US"
Omit location parameter for remote-only — country_admin_code is sufficient.

Pull up to 10 results per platform. Deduplicate by company + role title. Cap at 25 unique results. Keep the listing with the most complete JD text when duplicates appear.



Step B — Fast Stop-List Filter (title + snippet only, no full JD fetch)

Auto-drop without fetching if any of these appear in the search result:

BRD visible in snippet
Company is a known large enterprise, health system, or payer (Epic, Oracle Health, Cigna, Aetna, Kaiser, CVS Health, UnitedHealth, Humana, Optum, Change Healthcare, Cerner, Allscripts, athenahealth, or equivalent)
Title contains "Director of" or "VP of"
Title contains "Associate PM" or "APM"
Staffing agency or recruiter posting with no named employer

Log each drop with reason.



Step B2 — Snippet Green Flag Pre-Filter

Before fetching any full JD, scan the snippet for positive signal using the inlined green flag list. If a result shows 0 visible green signals and the company is unknown, skip the fetch. Log as No Signal.



Step C — Full JD Fetch + Stop List + Green Flag Count

Proxy note: job board redirect URLs (Indeed to.indeed.com, ZipRecruiter redirect URLs) are blocked. Use WebSearch to find the JD by company name + role title, then fetch from the company careers page or an aggregator (Ashby, Lever, Greenhouse, Himalayas, Ladders). If the JD cannot be surfaced, log as Fetch Blocked.

For each survivor, fetch the full JD and run the inlined stop list (Step 1 above). One hard stop = drop.

Then count green flags (Step 2 above):
0–2 flags: log as Low Signal. Note flag count and top gap. No further evaluation.
3+ flags: advance to scoring.



Step D — Score + Triage (3+ green flags only)

Run scoring only first (Step 3 above — four dimensions) for all results that cleared Step C. No company research yet.

Rank by total score. Take the top 5 only for full evaluation.

Results ranked 6+: log score and disposition. Skip company research, experience match, and interview questions.

Top 5:
Run company research in parallel (Step 4)
Run experience match (Step 5) — load resume and cv.md once, reuse for all 5
Run interview questions (Step 6)
Run recommendation (Step 7)



Job Search Output Format

Job Search Run — [date] — Keywords: [terms]
Platforms: Indeed, Dice, ZipRecruiter
Total unique: [n] | Fast-filtered: [n] | Full JD fetched: [n] | Advanced to full eval: [n]

Quick Scan Table
[Company] | [Role] | [Platform] | [Green Flags] | [Disposition]
...

Full Evaluations (ordered by total score, highest first)
[Standard Tier 3 or Tier 4 output for each result that reached Step D]



Save Search

save search [keywords or "default"]

Saves all results that reached Step D to Notion and job-search-pipeline.xlsx using the standard field structure. All saved rows include the current Rubric Version. Dropped results log as Status: Filtered with stop reason in Stability Flags. Low Signal results log as Status: Low Signal with green flag count noted.



Notion Database

Database: Job Search Pipeline
Location: prompt Jessica once if ID is not available, remember for session.
