# Decisions

This document explains why this skill is built the way it is. If you want to fork and adapt, this is the rationale behind the choices.

## Why a skill, not a prompt

A prompt is a single message. It does not persist. It is hard to version. It is hard to instrument.

A skill is a structured set of instructions Claude can load when triggered. It persists across sessions. It can be versioned. It supports conditional logic, file references, and output formats. It treats the work as a product, with discoverable triggers and predictable behavior.

For something used daily and worth tuning over time, the skill form pays off within the first few iterations.

## Why inlined criteria, not an external file

Earlier versions loaded a `jd-reference.md` file on every evaluation. Three problems:

1. The criteria changed less often than the file got loaded. Every run paid the token cost.
2. The file was the source of truth. The skill broke without it. Single point of failure.
3. The split between "skill logic" and "criteria" was artificial. Criteria are part of the skill.

Current state: criteria are inlined into the skill body. The external `jd-reference.md` exists as a versioned backup. It is not loaded at runtime.

Token cost savings: roughly 60% per evaluation run. More on skip-heavy days.

## Why phased file loading

The evaluation has natural exit conditions. Most JDs fail the stop list. Many that pass the stop list fail the green flag count. By the time scoring runs, the candidate pool is thinned significantly.

Loading user-profile files (writing style guide, working profile, resume, CV) on every evaluation wasted tokens on JDs rejected in the first ten seconds.

Current state: three phases.

- Phase 0: stop list and green flag count run on JD text only. Zero file reads.
- Phase 1: scoring loads the writing style guide.
- Phase 2: experience match loads the resume, CV, and working profile.

Token cost profile now matches the value tier of each evaluation. Skip evaluations cost almost nothing. Pursue evaluations load what they need.

## Why tiered output

A skip and a Pursue should not produce the same output volume.

Four tiers, scaling with how deep the evaluation went:

- Tier 1: hard stop. Three lines.
- Tier 2: green flag fail or score under 20. Five lines.
- Tier 3: skip with scores (20-23). Scores plus brief research plus one paragraph.
- Tier 4: full evaluation (24+). Full report with all sections.

This is also a respect-the-reader move. A skip does not deserve five hundred words.

## Why a stop list as hard exit

One hit ends the review. No rationalization.

The stop list captures things that, in practice, predict the role will not work. BRD in the posting. Stakeholder alignment as a primary responsibility. Approval governance in day-to-day work. "Collaborative" used too many times.

These are not aesthetic preferences. They are signals of bad-fit work environments. Allowing exceptions defeats the purpose.

Future work: override capture. If a stop list rule gets bypassed, capture the reason and the eventual outcome. Over time, the override data reveals where stated criteria and revealed criteria diverge.

## Why four scoring dimensions

Ownership. Process. Leadership. Work.

Each dimension captures a category where bad scores make the role bad regardless of other strengths.

- Ownership: do you own the product, or do you implement it?
- Process: is agile functional, or waterfall in disguise?
- Leadership: does product leadership have product experience?
- Work: is the work high-novelty, high-stakes, diagnostic?

A 9/10 in Work does not rescue a 2/10 in Ownership. The dimensions are not interchangeable.

Total is x/40. Threshold for full evaluation is 24. Threshold for Pursue is 30. These thresholds were picked empirically. Roles below 24 consistently turned out to be skips after company research anyway, so the threshold short-circuits work that was going to be wasted.

## Why a versioned rubric

The rubric will change. Criteria get refined as you learn what matters.

If the rubric changes mid-pipeline, you need to know which version scored which evaluation. Otherwise the outcome data is noise. You cannot tell whether Pursue calls converted because the rubric was calibrated or because the market was strong.

Current state: `RUBRIC_VERSION` is a date stamp at the top of the skill. Every save stamps that version onto the row. When you change criteria, update the date.

This is the most underrated piece of the architecture. It looks small. It is what makes the rest of the instrumentation falsifiable.

## Why outcome tracking

Every evaluation is a hypothesis. The recommendation is a prediction.

Without outcome data, the rubric stays unfalsifiable. Three questions stay unanswerable:

- Are your Pursue calls converting to interviews?
- Are your Skip calls the right skips?
- Which scoring dimension predicts conversion?

Current state: status field tracks the application lifecycle. Not applied → Applied → Acknowledged → Phone screen → Interview → Final round → Offer → Offer accepted or Offer declined or Rejected or Ghosted or Withdrew.

Status updates trigger separately from initial save. The skill finds the row by company name and updates status, status date, applied date (first transition only), and outcome notes.

After enough data, the rubric can be tested against reality. That is the learning loop.

## Why save to two places

Notion is structured. It supports filtering, views, and database queries. It is the right place for trend analysis.

Excel is portable. It is the backup. It opens fast on a laptop without a connector.

Saving to both means losing one does not lose the data. It also means the rubric can be analyzed in either tool depending on what you need.

The trade-off: writes have to stay in sync. If a status update goes to one but not the other, the data drifts. The skill writes to both on every save and every outcome update.

## Why a tiered salary signal hierarchy

Earlier versions of Step 4 told the skill to "cross-check Levels.fyi, Glassdoor, and Built In." Three sources treated as peers, no priority, no confidence indicator. The output was noise dressed as research.

The replacement walks four tiers in order, stopping at the highest tier with usable data. Each tier carries an explicit confidence level. Every output is tagged with the tier that produced it so the user knows whether the number is decision-grade or fallback.

- Tier 1: Posted range in the JD. Authoritative.
- Tier 2: Verified comp data (Levels.fyi, H1B/PERM disclosures, founder statements). High confidence.
- Tier 3: Curated aggregators (Built In, industry comp reports). Medium confidence.
- Tier 4: Self-reported aggregators (Glassdoor, Comparably, Payscale). Low confidence. Fallback only.

A $185k-$220k from a posted range is decision-grade. The same number from Glassdoor with three data points is not. Treating them as equal data produces wrong decisions.

Every signal is also compared against the Target Compensation Band defined at the top of the skill. The output explicitly notes in band, below band, or above band.

Future enhancement: a Tier 5 that compares the signal against the user's own accumulated pipeline data. After 30+ evaluated postings at similar stage and role level, the pipeline becomes the most accurate benchmark for what the target market is actually paying. Not yet built.

## What this does not do

Honesty about scope:

- Does not apply for you
- Does not write cover letters (separate skill for that)
- Does not negotiate salary
- Does not track interview prep
- Does not compare two JDs side by side (planned)
- Does not surface market trends across evaluations (planned, needs volume)
- Does not learn from outcomes automatically (the data is captured, the analysis is manual)

## The two-track future

This tool exists in two forms.

**Current form.** A personal skill, tuned to one user's profile and criteria. Useful for me. Forkable for others.

**Public form.** A general framework with separated engine and profile. New users build their own criteria through recognition-based onboarding. Show example stops and flags. Ask which apply, which they would add. This is architecture work, not a feature.

The two are different products. The current form is a tool I use. The public form is something someone else might build, or that I might build later if there is demand.

## Roadmap

The system works. Here is what is on deck to make it work better.

### Near-term

Low effort, immediate quality improvement.

**Interview question ranking.** Step 6 produces six tailored questions per Pursue. The lowest-confidence scoring dimension should drive question order. If ownership scored 5/10 because the JD was ambiguous, the ownership question leads. The other five trail.

**Additional job board sources.** Hiring Cafe and Wellfound added to the multi-platform search in Step A. Both navigate directly to the site first; WebSearch is the fallback if direct parameters are unavailable. Same parallel-search pattern as Indeed, Dice, and ZipRecruiter. Domain filtering runs manually after retrieval. Default keyword set broadened from "clinical AI" to "healthcare" broadly to match the expanded search surface. Result cap raised from 25 to 35 to accommodate the two additional platforms. Platform-specific trigger phrases added so either site can be searched independently.

**Outcome Notes overwrite trigger.** Current behavior appends with date prefix, never overwrites. If a typo needs fixing, no path. Build `replace notes [company] [text]` when the append-only behavior becomes a problem.

## Why a results summary table with lazy detail loading

The full evaluation output is thorough. It is also long. After reading five Tier 4 reports back to back, the decision of which role to pursue first requires holding too much in memory at once.

The summary table collapses each result to one row: company, role, platform, score, recommendation, salary signal, and the single clause that most explains the score. It is the default output after a job search run. No full reports render unless requested.

Detail is on demand. When the user says "show [company]" or "full report [company]", the Tier 3 or Tier 4 output for that result renders from what was already computed internally. No re-evaluation. No additional API call.

This also dropped the Quick Scan Table that previously appeared before full evaluations. That table served as a pre-filter view before the detail wall. With detail suppressed by default, the pre-filter is redundant. The summary table does both jobs.

Token impact: output tokens per job search run drop by roughly 4,000 to 5,000. Not the reason to make the change. The reason is that one table is a better reading experience than five full reports before you have decided what to read.

### Mid-term

Needs data accumulation or a larger build.

**Market view rollup.** Trend reports across evaluated JDs once volume builds. Score distribution, common stop list hits, common gaps, conversion rates by scoring dimension. Needs 30+ evaluations to be meaningful.

**Side-by-side JD comparison.** When two candidates land at the same time, the only way to compare them today is to re-read both reports. Build a comparison view with deltas surfaced. Recognition beats recall.

**Override capture.** Stop list overrides are not tracked. When a stop list rule gets bypassed, capture the reason and the eventual outcome. Over time, the override data reveals where stated criteria and revealed criteria diverge.

**Outcome-driven rubric refinement.** The instrumentation exists. The analysis does not. After enough outcomes accumulate, surface which scoring dimensions correlate with conversion and which do not. This is the learning loop made concrete.

### Strategic

Architectural work. Different scope from the items above.

**Engine and profile separation.** Current state: criteria, voice rules, and user context are intermixed with the scoring engine. A general-purpose version needs them decoupled. Engine handles scoring, research, and save logic. Profile holds the criteria and personal references.

**Recognition-based onboarding.** A new user cannot generate stop list and green flag criteria cold. The current skill assumes the user has already done the introspection. A general-purpose version needs to present example criteria and let new users pick what applies, building their own rubric through recognition rather than generation. This is product design work as much as engineering.

## License

MIT. Build on this.
