---
name: jd-triage
description: "Triages job descriptions across two tracks: FTE (pursue/conditional/skip) and Fractional 1099 (take it/discovery conversation/pass). Criteria loaded from the highest-versioned jd-reference file. Searches job boards for FTE postings and contract platforms for fractional work. Tracks outcomes over time."
---

JD Triage (Public Reference Version)

About this version

This is a sanitized public reference of the skill. The criteria, file paths, and voice rules below are working examples from one user's setup. Replace with your own when forking.

What to keep when forking: the architecture (two-track evaluation, versioned external reference file, phased file loading, tiered output, outcome tracking, save-to-two-destinations).

What to replace: the stop list, green flags, scoring rubric anchors, voice rules, file paths, and any references to a specific user's profile.

The skill supports two tracks: FTE (full-time employment) and Fractional (1099 contract, corp-to-corp). Each track has its own stop list, green flag list, scoring rubric, recommendation thresholds, and output format. Criteria for both tracks live in an external versioned reference file, not inlined in the skill. See Reference Files below.



Trigger Phrases

evaluate jd [text, URL, or filename]
jd [text, URL, or filename]
review jd [text, URL, or filename]

If a URL is provided, fetch the page first. If a file is attached, read it. If text is pasted, work from that.

Track Declaration

Every triage session runs on one track. Declare the track before evaluating.

triage fte [text, URL, or filename] — runs FTE track
triage fractional [text, URL, or filename] — runs Fractional track
triage [text, URL, or filename] — if no track declared, scan the posting for employment type signal. If 1099, corp-to-corp, contract, or fractional language appears, default to Fractional track. If full-time employment language appears, default to FTE track. If ambiguous, ask the user which track before proceeding.

The active track determines which stop list, green flag list, scoring rubric, recommendation thresholds, and output format are used. Do not mix criteria across tracks.



Rubric Version

RUBRIC_VERSION: [today's date in YYYY-MM-DD format]

This is the version stamp applied at save time. It reflects the jd-reference file version that was active when the evaluation ran. Update this date any time you update the jd-reference file or change any process logic in this skill.

Every save stamps this version into the Rubric Version field of the saved row. That lets you trace which postings were scored by which version of the rubric. If criteria change mid-pipeline, the outcomes stay attributable to the version that produced them.



Reference Files

These files customize the skill to a specific user. Replace paths and filenames with your own. All files should live in the same folder.

File
Purpose
jd-reference-v[n].md
Stop lists, green flags, scoring rubrics, and compensation band for both tracks. Always load the highest-versioned file matching this pattern. If multiple versions exist, use the one with the largest n. If none exists, stop and tell the user before proceeding.
style-guide.md
Voice rules for any drafted output
resume-v[n].pdf
Polished resume. Always load the highest-versioned file matching this pattern. Map JD requirements to actual experience.
cv-v[n].md
Full career history. Always load the highest-versioned file matching this pattern. Use when the JD calls for something the resume cut for length.
profile.md
Working profile. Context on how the user operates and what a good work environment looks like.

If any file is missing at the expected path, stop and tell the user which one before proceeding.

Versioned file resolution rule: for any file using the v[n] pattern, find all files matching that pattern in the reference folder, load the one with the largest n. Drop in a new version when criteria change. The skill always finds the current one without editing the skill file itself.



Two-Phase Loading

Phase 0 — load jd-reference only.
Find the highest-versioned file matching jd-reference-v[n].md in your reference folder. Load it. Extract the stop list, green flag list, and scoring rubric for the active track. Also extract shared rules. Run Steps 1 and 2 against the posting using the loaded criteria. A hard stop or green flag fail exits here with no further file I/O.

Phase 1 — load on first score.
When a posting reaches Step 3 (scoring), load:
style-guide.md

Phase 2 — load on experience match only.
FTE track only. When a posting reaches Step 5 (score 24 or above), load:
Highest-versioned resume-v[n].pdf
Highest-versioned cv-v[n].md
profile.md
Fractional track does not run an experience match step. Phase 2 does not fire on Fractional track evaluations.

Session cache rule: if these were already read earlier in this session, do not re-read them.



Shared Rules (loaded from jd-reference file)

Before entering either track, the skill checks shared rules from the jd-reference file. These apply regardless of track. At minimum, shared rules should cover:

Employment type eligibility: which contract structures are acceptable and which are not.
Tax and compliance rules: any rules that govern how different engagement types interact (for example, restrictions on holding two forms of engagement from the same company in the same tax year).

Define your own shared rules in the jd-reference file under a Shared Rules section.



FTE TRACK EVALUATION



Step 1 — Stop List (loaded from jd-reference file)

Run before anything else. Use the FTE stop list loaded from the jd-reference file. Do not use hardcoded criteria.

One hit ends the review. Do not rationalize past it.

Output on hard stop — short form only:
STOP. [Company] – [Role]
Hard stop: [which item] — "[exact quote from JD]"
Recommendation: Skip
[One sentence on why this matters]
Do not score. Do not research. Do not read any additional files.

Example stop list items (replace with your own):
BRD anywhere in the posting
Stakeholder alignment listed as a primary responsibility
Approval process or governance in day-to-day responsibilities
"Collaborative" used more than twice
JD written by HR with no product vocabulary
Large enterprise as the primary company type (unless explicitly a startup inside it)
Two PMs listed as owning the same product



Step 2 — Green Flag Count (loaded from jd-reference file)

If the stop list passes, count green flags using the FTE green flag list from the jd-reference file. Do not use hardcoded criteria.

Quote the posting text for each flag found.

Fewer than 3 flags → skip. Output:
[Company] – [Role]
Green flags: [n] — [name the 1–2 that hit, or "none"]
Missing: [top 2–3 gaps]
Recommendation: Skip
Do not score. Do not read any additional files.

Example green flags (replace with your own):
Product trio: PM, engineering, design as the core working unit
"Own" or "ownership" used specifically about the product and roadmap
Trade-offs mentioned as part of the decision-making work
Small or growing team
Technical or clinical founder
AI as a product capability being built (not internal tooling)
Discovery and delivery both listed as PM responsibilities
Salary range that is real and in the user's target band



Step 3 — Score the Four Dimensions (load style guide)

Score 1 to 10 for each dimension using the FTE scoring rubric from the jd-reference file. Do not use hardcoded criteria.

Total: x/40.

Score under 20 → stop here. Output short skip:
[Company] – [Role]
Scores: Ownership [x] | Process [x] | Leadership [x] | Work [x] | Total [x]/40
Top reason: [one sentence]
Recommendation: Skip
Do not run company research, experience match, or interview questions.

Example scoring rubric (replace anchors with your own):

Ownership
10: One product, theirs, they define what gets built, own the roadmap, budget gets approved not product calls
5: Ambiguous ownership, shared decision-making
1: Implementation role, executive sign-off on user stories, BRD-driven

Process
10: Discovery and delivery both PM responsibilities, ceremonies right-sized, no governance theater
5: Mentions agile but unclear how it runs in practice
1: Waterfall dressed as agile, full-day planning, 15-plus gate approvals

Leadership
10: Technical or clinical founder, Head of Product with real PM background, PMs make trade-off calls
5: Leadership background unclear or mixed
1: Sales-founder calling product shots, layered approvals

Work
10: High-novelty, high-stakes, diagnostic work, real ownership surface area
5: Mix of interesting and routine, surface area unclear
1: Maintenance role, ticket-taker, no diagnostic work in scope



Step 4 — Company Research (web searches, no new file reads)

Run only if score 20 or above. Run all searches in parallel:

Stage: funding round, total raised, headcount range, year founded
Leadership: CEO, Head of Product, CTO backgrounds (clinical, technical, sales, or other)
AI signal: is AI in the product or internal tooling? Quote evidence from site, blog, or recent news.
Recent activity: launches, hires, funding, layoffs in the last 12 months
Salary signal: walk the tiered hierarchy below. Stop at the highest tier with usable data. Tag every output with the tier that produced it.

Tier 1 (authoritative, confidence: high)
Posted range in the JD itself. Extract verbatim. If present, this is the answer. Stop.

Tier 2 (verified, confidence: high)
Verified compensation data. Search in parallel:
Levels.fyi for this exact company
H1B/PERM disclosures via h1bdata.info or similar (relevant for visa-sponsoring companies only)
Direct founder or leadership statements about comp philosophy from blogs, podcasts, or interviews in the last 12 months

Tier 3 (curated, confidence: medium)
Built In salary data if the company is in their database. Industry-specific compensation reports.

Tier 4 (self-reported, confidence: low)
Glassdoor, Comparably, Payscale. Use only when Tiers 1-3 surface nothing. Tag the output explicitly as Tier 4.

If no tier surfaces usable data: output "No reliable salary signal available" and flag for interview-stage clarification.

Cross-reference the signal against the Target Compensation Band from the jd-reference file. Note explicitly: in band, below band, or above band.
Stability flags: recent restructures, leadership churn, mass layoffs

If company research surfaces a blocker, the score may drop below threshold. Adjust recommendation accordingly.



Step 5 — Experience Match (load resume + cv, score 24 or above only)

Cross-reference JD requirements against the highest-versioned resume-v[n].pdf and cv-v[n].md:

Strong matches: JD requirement → specific role + result from the resume (quote both)
Stretch matches: adjacent experience, note what bridges the gap
Gaps: honest assessment; flag structural gaps (certification, specific industry, tool stack never touched) as real concerns



Step 6 — Interview Questions (score 24 or above only)

Define your own base questions in the jd-reference file or inline here. Tailor each with a specific hook from the JD before outputting.

Example questions (replace with your own):
On ownership: Walk me through the last time a PM pushed back on an executive request. What happened?
On agile: How many people are in sprint ceremonies? Who can cancel or change a sprint?
On product leadership: How did your Head of Product get into product management?
On decision-making: What does it look like when a PM disagrees with roadmap direction? Give me a recent example.
On future boss: What makes a PM hard to manage?
On AI: Is AI a product capability you are building or a tool your team uses internally?



Step 7 — Recommendation

Three options. Pick one.

Outcome | Criteria
Pursue | Total 30 or above AND no soft stops AND clean company research
Conditional | Total 24 to 29 OR one to two soft flags an interview question can resolve
Skip | Stop list hit, fewer than 3 green flags, total under 24, or company research surfaces a blocker



FTE Tiered Output Format

Output scales with how far the evaluation ran.

Tier 1 — Hard Stop
STOP. [Company] – [Role] – [URL or "pasted JD"]
Hard stop: [item triggered] — "[quote]"
Recommendation: Skip
[One sentence reason]

Tier 2 — Green Flag Fail or Low Score (under 20)
[Company] – [Role] – [URL or "pasted JD"]
Recommendation: Skip | Total: [x]/40 or Green flags: [n]
[2 to 4 lines: what failed and why]

Tier 3 — Skip with Scores (20 to 23, after company research)
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

Tier 4 — Full Evaluation (Pursue or Conditional, score 24 or above)
[Company] – [Role] – [URL or "pasted JD"]
Recommendation: [Pursue / Conditional] | Total: [x]/40

Stop List: Passed — no hard stops found

Green Flags Found ([n])
  [Flag]: "[quote]"

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
  Compensation Research:
    Posted in JD: [range or "not posted"]
    Best External Signal: [source] [range] (Tier [n], [High/Medium/Low confidence])
    Vs target band: [in band / below band / above band]
  Stability: [flags or none]

Experience Match
  Strong matches:
    [JD requirement] → [role + result from resume]
  Stretch matches:
    [JD requirement] → [what bridges the gap]
  Gaps:
    [JD requirement] → [assessment]

Interview Questions
  [Category]: [question with JD hook]

Recommendation
[One paragraph. Direct. Why pursue or conditional. If conditional, list exactly what to clarify.]



FRACTIONAL TRACK EVALUATION

Fractional track does not run company research or experience match. It is a workload and interest filter, not an organizational fit evaluation. The question is not whether the company is well-run. The question is whether the engagement is worth taking.

Confirm 1099 or corp-to-corp structure before entering this track. Check shared rules from the jd-reference file first.



Fractional Step 1 — Shared Rules Check

Check employment type and any shared compliance rules from the jd-reference file before anything else. Hard stops defined in shared rules end the review regardless of track.



Fractional Step 2 — Stop List (loaded from jd-reference file)

Use the Fractional stop list from the jd-reference file. One hard stop ends the review.

Output on hard stop:
STOP. [Company] – [Role]
Hard stop: [which item] — "[exact quote]"
Recommendation: Pass
[One sentence on why]

Example fractional stop list items (replace with your own):
W2 contract structure
Required core hours or fixed availability with no flexibility
Hours exceed your weekly maximum with no room to negotiate scope down
Work type you have ruled out entirely
Body-in-meetings role with no defined purpose and no problem awareness



Fractional Step 3 — Green Flag Count (loaded from jd-reference file)

Count green flags using the Fractional green flag list from the jd-reference file. Two or more required to advance to scoring.

Fewer than 2 flags → Pass. Output:
[Company] – [Role]
Green flags: [n] — [name what hit, or "none"]
Missing: [top 2 gaps]
Recommendation: Pass

Example fractional green flags (replace with your own):
Defined project engagement with a specific deliverable and deadline
Ongoing support engagement with documented scope and known deliverable types
Discovery or diagnostic engagement with a named problem
Domain or subject matter you want to work in
Async and self-directed structure confirmed
Hours under your preferred weekly ceiling
Outcome-based or deliverable-based engagement structure



Fractional Step 4 — Score Three Dimensions (loaded from jd-reference file)

Score 1 to 10 for each dimension using the Fractional scoring rubric from the jd-reference file.

Deliverable: [x]/10
Hours: [x]/10
Interest: [x]/10
Total: [x]/30

Example scoring rubric for three dimensions (replace anchors with your own):

Deliverable
10: Specific project, specific outcome, specific deadline
7: Ongoing support with defined purpose, known deliverable types, clear scope boundaries
4: Partial definition. Some clarity on purpose, vague on what done looks like
2: No scope yet but genuine problem awareness and openness to discovery. Flag as discovery conversation candidate, not a pass.
1: No scope, no problem awareness, no definition of success. Body in meetings.

Hours
10: Well under your weekly maximum, async, fully self-directed
7: Moderate hours, async, flexible
4: Near your weekly maximum, async, flexible. Note total load before committing.
2: At or near your maximum with any scheduling rigidity
1: Exceeds your maximum or requires fixed availability. Hard stop.

Interest
10: Exactly the domain and problem type you want
5: Adjacent domain or problem type, acceptable
1: Domain or work type you have ruled out

Apply recommendation thresholds from the jd-reference file:
Take It: high total, strong deliverable score, hours confirmed acceptable
Discovery Conversation: moderate total or low deliverable score with high interest. Worth a scoping call.
Pass: low total, deliverable score of 1, hours hard stop, or ineligible contract structure



Fractional Step 5 — Scoping Questions

For any result that reaches Take It or Discovery Conversation, generate tailored scoping questions using the base questions from the jd-reference file. Hook each question to something specific in the posting.

Example base scoping questions (replace with your own):
On structure: Is this engagement 1099 or corp-to-corp? Is there a W2 option?
On deliverable: What does done look like? What are you expecting to have at the end of this engagement?
On hours: How many hours per week are you expecting?
On scheduling: Can work be done on a fully async, self-directed schedule?
On scope: Is this a defined project or ongoing support?
On problem clarity (for discovery conversation candidates): What made you start looking for fractional help? What is the thing you are trying to fix or build?



Fractional Output Format

Tier F1 — Hard Stop
STOP. [Company] – [Role] – [URL or "pasted posting"]
Hard stop: [item triggered] — "[quote]"
Recommendation: Pass
[One sentence reason]

Tier F2 — Green Flag Fail
[Company] – [Role] – [URL or "pasted posting"]
Green flags: [n] — [what hit or "none"]
Recommendation: Pass
[2 to 3 lines on what is missing and why it matters]

Tier F3 — Pass with Scores
[Company] – [Role] – [URL or "pasted posting"]
Recommendation: Pass | Total: [x]/30

Scores
  Deliverable [x]/10 — [one-line reasoning]
  Hours       [x]/10 — [one-line reasoning]
  Interest    [x]/10 — [one-line reasoning]

[One paragraph on why this does not meet threshold]

Tier F4 — Discovery Conversation
[Company] – [Role] – [URL or "pasted posting"]
Recommendation: Discovery Conversation | Total: [x]/30

Stop List: Passed
Green Flags Found ([n])
  [Flag]: "[quote]"

Scores
  Deliverable [x]/10 — [one-line reasoning]
  Hours       [x]/10 — [one-line reasoning]
  Interest    [x]/10 — [one-line reasoning]

Scoping Questions
  Structure:   [question with posting hook]
  Deliverable: [question with posting hook]
  Hours:       [question with posting hook]
  Scheduling:  [question with posting hook]
  Scope:       [question with posting hook]

Recommendation
[One paragraph. What makes this a discovery conversation rather than a take it. What the scoping call needs to resolve.]

Tier F5 — Take It
[Company] – [Role] – [URL or "pasted posting"]
Recommendation: Take It | Total: [x]/30

Stop List: Passed
Green Flags Found ([n])
  [Flag]: "[quote]"

Scores
  Deliverable [x]/10 — [one-line reasoning]
  Hours       [x]/10 — [one-line reasoning]
  Interest    [x]/10 — [one-line reasoning]

Scoping Questions
  Structure:   [question with posting hook]
  Deliverable: [question with posting hook]
  Hours:       [question with posting hook]
  Scheduling:  [question with posting hook]
  Scope:       [question with posting hook]

Recommendation
[One paragraph. Direct. Why take it. Flag anything that needs confirmation before committing.]



Save Trigger

save [company name]

Saves the evaluated posting to two places simultaneously.

Save 1 — Notion
Logs to the pipeline database. If the database ID is not available, prompt once and remember for the session.

Save 2 — Excel
Creates or appends to job-search-pipeline.xlsx in the user's outputs folder. Append only — never overwrite. Present file as downloadable after save.

Fields (same column order in xlsx):
Track | Company | Role Title | JD URL or Source | Recommendation | Total Score | Dimension 1 Score | Dimension 2 Score | Dimension 3 Score | Dimension 4 Score | Stop List Result | Green Flag Count | Stage | Leadership Background | AI Signal | Salary Signal | JD Posted Salary | Stability Flags | Scoping Questions or Interview Questions (semicolon-separated) | Recommendation Reasoning | Rubric Version | Date Evaluated (YYYY-MM-DD) | Status | Status Date | Applied Date | Outcome Notes

Track field values: FTE or Fractional

FTE dimension fields: Dimension 1 = Ownership, Dimension 2 = Process, Dimension 3 = Leadership, Dimension 4 = Work
Fractional dimension fields: Dimension 1 = Deliverable, Dimension 2 = Hours, Dimension 3 = Interest, Dimension 4 = blank

Recommendation field valid values:
FTE: Pursue, Conditional, Skip
Fractional: Take It, Discovery Conversation, Pass

Field rules at save time:
Track: FTE or Fractional based on active track
Rubric Version: auto-stamp from the RUBRIC_VERSION line at the top of this skill
Date Evaluated: today's date in YYYY-MM-DD format
Status: default "Not applied" for FTE, "Not contacted" for Fractional
Status Date: today's date
Applied Date: blank at save time
Outcome Notes: blank at save time

Excel rules: Sheet name: Job Search Pipeline. Row 1: bold, frozen. Column widths: auto-fit, minimum 15 characters. No formulas.

After both saves: Saved — [Company] – [Role] added to Notion and job-search-pipeline.xlsx (rubric version [version]).



Outcome Tracking

Trigger phrases:
outcome [company] [status]
update [company] [status]

When triggered, find the existing row in both Notion and Excel by company name match. If multiple rows match (same company, different roles), prompt for which one before updating.

Update these fields:
Status: the new status
Status Date: today's date in YYYY-MM-DD format
Applied Date: today's date if Status was "Not applied" and is moving to any other status (set once, on first transition out of "Not applied")
Outcome Notes: prompt for optional notes if not included in the trigger. Append to existing notes with a date prefix, never overwrite.

Valid statuses:

FTE:
Not applied (default)
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

Fractional:
Not contacted (default)
Discovery call scheduled
Discovery call complete
Proposal sent
Engaged
Engagement complete
Passed
Ghosted
Declined

Both tracks from Save Search:
Filtered (results dropped in fast filter)
Low Signal (results with fewer than the minimum green flags)

Confirm after update:
Updated — [Company] [Role]: [old status] → [new status] ([new status date])

If the company name does not match any existing row, do not create a new row. Output:
No evaluation found for [Company]. Did you mean a different company, or do you need to evaluate this posting first?



Voice Rules

These rules are an example. Replace with the voice and style that match your own writing preferences.

Direct. Confident. Short sentences. Fragments OK.

Banned: may, just, very, really, basically, probably, certainly, could, maybe, utilize, leverage, impactful, innovative, exciting, groundbreaking, game-changer, delve, embark, craft, imagine, realm, revolutionize, tapestry, illuminate, landscape, literally, actually, furthermore, hence.

No em dashes. No semicolons in prose. No hashtags. No asterisks in body copy. No softening. No hedging. If a posting is a skip or a pass, say so.



Job Search Function — FTE

Trigger Phrases

search jobs [optional keywords]
search fte [optional keywords]
find jobs [optional keywords]
hunt [optional keywords]

Default keywords (if none provided): adjust to match your target role and industry.

All results from this function run on the FTE track.



Step A — Search All Five Platforms (parallel)

Indeed
search: [keyword or default title]
location: "remote"
country_code: "US"
job_type: "fulltime"

Dice
Run two searches in parallel for your target titles. Do NOT add domain qualifiers as Dice keywords — those qualifiers return non-PM roles. Filter for domain relevance manually after retrieval.
workplace_types: ["Remote"]
employment_types: ["FULLTIME"]
posted_date: "SEVEN"

ZipRecruiter
query: [keyword or default title]
location_types: ["REMOTE"]
employment_types: ["FULL_TIME"]
seniority_classes: ["SENIOR"]
country_admin_code: "US"
Omit location parameter for remote-only.

Hiring Cafe
Navigate to https://hiring.cafe/ and search using default or user-provided keywords. Filter for remote and full-time. Fetch up to 10 results. If the site supports direct search parameters, use them; otherwise use WebSearch with site:hiring.cafe [keywords].

Wellfound
Navigate to https://wellfound.com/jobs and search using default or user-provided keywords. Filter for remote and full-time. Fetch up to 10 results. If the site supports direct search parameters, use them; otherwise use WebSearch with site:wellfound.com/jobs [keywords].

Pull up to 10 results per platform. Deduplicate by company + role title. Cap at 35 unique results. Keep the listing with the most complete JD text when duplicates appear.



Step B — Fast Stop-List Filter (title + snippet only, no full JD fetch)

Auto-drop without fetching if any stop list criteria from the jd-reference file appear in the search result. Also auto-drop on these search-specific conditions:

Title contains "Director of" or "VP of"
Title contains "Associate PM" or "APM"
Staffing agency or recruiter posting with no named employer

Log each drop with reason.



Step B2 — Snippet Green Flag Pre-Filter

Before fetching any full JD, scan the snippet for positive signal using the green flag list from the jd-reference file. If a result shows 0 visible green signals and the company is unknown, skip the fetch. Log as No Signal.



Step C — Full JD Fetch + Stop List + Green Flag Count

Proxy note: job board redirect URLs are often blocked. Use WebSearch to find the JD by company name + role title, then fetch from the company careers page or an aggregator (Ashby, Lever, Greenhouse, Himalayas, Ladders). If the JD cannot be surfaced, log as Fetch Blocked.

For each survivor, fetch the full JD and run the stop list from the jd-reference file. One hard stop = drop.

Then count green flags:
0 to 2 flags: log as Low Signal. Note flag count and top gap. No further evaluation.
3 or more flags: advance to scoring.



Step D — Score + Triage (3+ green flags only)

Run scoring only first for all results that cleared Step C. No company research yet.

Rank by total score. Take the top 5 only for full evaluation.

Results ranked 6 or below: log score and disposition. Skip company research, experience match, and interview questions.

Top 5:
Run company research in parallel (Step 4)
Run experience match (Step 5) — load highest-versioned resume and cv once, reuse for all 5
Run interview questions (Step 6)
Run recommendation (Step 7)



FTE Job Search Output Format

Run all evaluations to completion internally before outputting anything.

Default output — summary table only:

FTE Job Search Run — [date] — Keywords: [terms]
Platforms: Indeed, Dice, ZipRecruiter, Hiring Cafe, Wellfound
Total unique: [n] | Fast-filtered: [n] | Full JD fetched: [n] | Advanced to full eval: [n]

Company | Role | Platform | Score | Recommendation | Salary | Top Reason
[name] | [title] | [platform] | [x]/40 | Pursue / Conditional / Skip | [posted range or "no signal"] | [one clause]

Order by score descending. Include all results that reached scoring, even those below 24.

On-demand full report:
When the user says "show [company]" or "full report [company]", render the FTE Tier 3 or Tier 4 output for that result. Do not re-run the evaluation — use what was already computed internally.



Save Search — FTE

save search [keywords or "default"]

Saves all results that reached Step D to Notion and job-search-pipeline.xlsx. Track field set to FTE for all rows. Dropped results log as Status: Filtered. Low Signal results log as Status: Low Signal.



Job Search Function — Fractional

Trigger Phrases

search fractional [optional keywords]
hunt fractional [optional keywords]
search contract [optional keywords]
search 1099 [optional keywords]
search corp to corp [optional keywords]
search c2c [optional keywords]

Default keywords (if none provided): adjust to match your target engagement type and domain.

All results from this function run on the Fractional track.



Step FA — Search Platforms (parallel)

Dice
Run two searches in parallel for contract PM titles.
workplace_types: ["Remote"]
employment_types: ["CONTRACT"]
posted_date: "SEVEN"
Do NOT add domain qualifiers as Dice keywords. Filter for relevance manually after retrieval.

Indeed
search: [keyword or default]
location: "remote"
country_code: "US"
job_type: "contract"

ZipRecruiter
query: [keyword or default]
location_types: ["REMOTE"]
employment_types: ["CONTRACT"]
country_admin_code: "US"

Toptal
Navigate to https://www.toptal.com/product-managers and search using default or user keywords. Fetch up to 10 results. Use WebSearch with site:toptal.com [keywords] if direct search is not available.

Upwork
Use WebSearch with site:upwork.com "product manager" [keywords] remote to surface relevant postings. Fetch up to 10 results.

Pull up to 10 results per platform. Deduplicate by company + role. Cap at 35 unique results.



Step FB — Fast Stop-List Filter (title + snippet only)

Auto-drop without fetching if any of these appear:

W2 contract language in snippet ("W2 only", "must work as W2")
Staffing agency or body shop posting with no named employer
Hours explicitly stated above your weekly maximum in snippet
Required on-site or hybrid location

Log each drop with reason.



Step FC — Full Posting Fetch + Stop List + Green Flag Count

Fetch the full posting. Run the Fractional stop list from the jd-reference file. One hard stop = drop.

Count Fractional green flags:
0 to 1 flags: log as Low Signal.
2 or more flags: advance to scoring.



Step FD — Score + Triage (2+ green flags only)

Run Fractional scoring for all results that cleared Step FC.

Rank by total score. Take the top 5 only for full output.

Results ranked 6 or below: log score and disposition only.

Top 5: run scoping questions and recommendation.



Fractional Job Search Output Format

Run all evaluations to completion internally before outputting anything.

Default output — summary table only:

Fractional Search Run — [date] — Keywords: [terms]
Platforms: Dice, Indeed, ZipRecruiter, Toptal, Upwork
Total unique: [n] | Fast-filtered: [n] | Full posting fetched: [n] | Advanced to full eval: [n]

Company | Role | Platform | Score | Recommendation | Hours Signal | Top Reason
[name] | [role] | [platform] | [x]/30 | Take It / Discovery Conversation / Pass | [posted hours or "not stated"] | [one clause]

Order by score descending.

On-demand full report:
When the user says "show [company]" or "full report [company]", render the Fractional Tier F4 or F5 output. Do not re-run the evaluation.



Save Search — Fractional

save fractional search [keywords or "default"]

Saves all results that reached Step FD to Notion and job-search-pipeline.xlsx. Track field set to Fractional for all rows. Dropped results log as Status: Filtered. Low Signal results log as Status: Low Signal.



Notion Database

Database: [your pipeline database name]
Location: prompt the user once if ID is not available, remember for session.

Required Notion properties:
Track (Select: FTE, Fractional)
Company (Title)
Role Title (Text)
JD URL or Source (URL or Text)
Recommendation (Select: Pursue, Conditional, Skip, Take It, Discovery Conversation, Pass)
Total Score (Number)
Dimension 1 Score (Number)
Dimension 2 Score (Number)
Dimension 3 Score (Number)
Dimension 4 Score (Number)
Stop List Result (Select: Passed, Failed)
Green Flag Count (Number)
Stage (Text)
Leadership Background (Text)
AI Signal (Select: Product, Tool, Unclear)
Salary Signal (Text)
JD Posted Salary (Text)
Stability Flags (Text)
Scoping or Interview Questions (Text, long-form)
Recommendation Reasoning (Text, long-form)
Rubric Version (Text)
Date Evaluated (Date)
Status (Select: all valid statuses for both tracks)
Status Date (Date)
Applied Date (Date)
Outcome Notes (Text, long-form)
