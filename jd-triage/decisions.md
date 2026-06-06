# Decisions

This document explains why this skill is built the way it is. If you want to fork and adapt, this is the rationale behind the choices.

## Why a skill, not a prompt

A prompt is a single message. It does not persist. It is hard to version. It is hard to instrument.

A skill is a structured set of instructions Claude can load when triggered. It persists across sessions. It can be versioned. It supports conditional logic, file references, and output formats. It treats the work as a product, with discoverable triggers and predictable behavior.

For something used daily and worth tuning over time, the skill form pays off within the first few iterations.

## Why two tracks

FTE evaluation and fractional evaluation answer different questions.

FTE asks: is this a good fit for me as an employee? It cares about ownership, process, leadership quality, and work type. It needs company research, experience match, and interview questions. The recommendation is pursue, conditional, or skip.

Fractional asks: is this engagement worth taking? It does not care whether the company is well-run. It cares whether the deliverable is defined, whether the hours fit, and whether the work is interesting. No company research. No experience match. Scoping questions instead of interview questions. The recommendation is take it, discovery conversation, or pass.

These are different enough in logic, output format, and pipeline tracking that they warrant separate tracks rather than a shared rubric with conditionals.

The shared architecture (phased loading, versioned rubric, save-to-two-destinations, outcome tracking) applies to both.

## Why criteria live in an external file

Earlier versions hardcoded the stop list, green flags, and scoring rubric directly in the skill. Three problems:

1. Changing criteria required editing the skill file. Any edit risked breaking other logic.
2. The criteria were not versioned independently of the skill logic. A rubric change and a process change looked the same.
3. Adding a second track doubled the hardcoded criteria, making the skill long and hard to maintain.

Current state: all criteria (stop lists, green flag lists, scoring rubrics, compensation band, scoping questions) live in a versioned external reference file (`jd-reference-v[n].md`). The skill loads the highest-versioned file at runtime.

This means:
- Criteria changes are file edits, not skill edits.
- Each evaluation is attributable to the reference file version that scored it, independently of skill logic changes.
- Adding criteria for a new track is an addition to the reference file, not a rewrite of the skill.

The versioning pattern (`v[n]`) means you never edit a file that has already been used in evaluations. Drop a new version. The skill finds it automatically.

## Why versioned file resolution for resume and CV

The same logic that applies to the reference file applies to the resume and CV. These change over time. Hardcoding a specific filename means every update requires a skill edit.

Current state: the skill looks for the highest-versioned file matching `resume-v[n].pdf` and `cv-v[n].md`. Update your resume, increment the version, drop it in the folder. The skill finds it without touching the skill file.

## Why phased file loading

The evaluation has natural exit conditions. Most postings fail the stop list. Many that pass the stop list fail the green flag count. By the time scoring runs, the candidate pool is thinned significantly.

Loading user-profile files (style guide, profile, resume, CV) on every evaluation wastes tokens on postings rejected in the first ten seconds.

Current state: three phases.

- Phase 0: load the reference file. Run stop list and green flag count. Zero other file reads.
- Phase 1: scoring loads the style guide.
- Phase 2: experience match loads the resume, CV, and profile. FTE track only. Fractional track does not run experience match.

Token cost profile now matches the value tier of each evaluation. Hard stops cost almost nothing. Full Pursue evaluations load what they need.

## Why tiered output

A hard stop and a Pursue should not produce the same output volume.

FTE has four tiers scaling with evaluation depth. Fractional has five tiers (F1 through F5) scaled the same way.

Tier 1 / F1: hard stop. Three lines.
Tier 2 / F2: green flag fail or low score. Five lines.
Tier 3 / F3: skip or pass with scores. Scores plus brief notes.
Tier 4 / F4: conditional or discovery conversation. Full report with scoping questions.
Tier 5 / F5 (Fractional only): take it. Full report.

This is also a respect-the-reader move. A skip does not deserve five hundred words.

## Why a stop list as hard exit

One hit ends the review. No rationalization.

The stop list captures things that, in practice, predict the engagement will not work. Allowing exceptions defeats the purpose.

The FTE stop list and Fractional stop list are different. FTE stops on organizational signals (BRD in the posting, shared PM ownership, governance in day-to-day work). Fractional stops on engagement structure signals (W2 contract, required core hours, hours above the weekly ceiling, body-in-meetings with no scope).

Both stop lists live in the reference file, not the skill. They can be updated without touching the skill logic.

## Why the fractional scoring has three dimensions instead of four

Fractional evaluation is a workload and interest filter. The right dimensions are deliverable definition, hours fit, and interest fit. Organizational dimensions (ownership, process, leadership) do not apply because you are not joining the organization.

The deliverable dimension is the most important and is weighted accordingly in the recommendation thresholds. A high-interest, manageable-hours engagement with no defined deliverable is a discovery conversation, not a take it.

## Why the deliverable dimension scores a 2 for vague but aware

"We think we need product help" with genuine problem awareness is not the same as "we need a PM body in meetings." The first is a valid fractional entry point. The second is a misclassified W2 contract.

A score of 2 flags the posting as a discovery conversation candidate rather than a pass. The scoping call exists to resolve scope before committing. That is the right use of both parties' time.

## Why a discovery conversation tier exists

Most fractional evaluation frameworks are binary: take it or pass. The discovery conversation tier captures the middle case: something worth a short call to determine fit before committing.

This is especially useful for postings with low deliverable definition but high interest, or postings that match the domain but have unclear hours. One conversation resolves the ambiguity faster than more research.

## Why a versioned rubric

The rubric will change. Criteria get refined as you learn what matters.

If the rubric changes mid-pipeline, you need to know which version scored which evaluation. Otherwise the outcome data is noise. You cannot tell whether Pursue calls converted because the rubric was calibrated or because the market was strong.

Current state: `RUBRIC_VERSION` is a date stamp at the top of the skill. Every save stamps that version onto the row. When you change the reference file or the skill logic, update the date.

This is the most underrated piece of the architecture. It looks small. It is what makes the learning loop falsifiable.

## Why outcome tracking

Every evaluation is a hypothesis. The recommendation is a prediction.

Without outcome data, the rubric stays unfalsifiable. Three questions stay unanswerable:

- Are your Pursue calls converting to interviews?
- Are your Take It calls converting to engagements?
- Which scoring dimension predicts conversion?

FTE and Fractional have separate status lifecycles because the outcomes are different. FTE tracks application stages. Fractional tracks engagement stages.

After enough data, the rubric can be tested against reality.

## Why save to two places

Notion is structured. It supports filtering, views, and database queries. It is the right place for trend analysis.

Excel is portable. It is the backup. It opens fast without a connector.

Saving to both means losing one does not lose the data.

The trade-off: writes have to stay in sync. The skill writes to both on every save and every outcome update.

## Why the Track field was added to the pipeline

FTE and Fractional evaluations share a pipeline database but are different things. Without a Track field, you cannot filter the pipeline by engagement type. You cannot ask which track is converting. You cannot keep the two pipelines from visually colliding.

Track is the first field in both the Notion database and the Excel file. It is set at save time based on the active track for that evaluation.

## Why a tiered salary signal hierarchy

Earlier versions told the skill to cross-check multiple salary sources simultaneously with no priority order. The output was noise dressed as research.

The replacement walks four tiers in order, stopping at the highest tier with usable data. Each tier carries an explicit confidence level. Every output is tagged with the tier that produced it.

- Tier 1: Posted range in the JD. Authoritative.
- Tier 2: Verified comp data (Levels.fyi, H1B/PERM disclosures, founder statements). High confidence.
- Tier 3: Curated aggregators (Built In, industry comp reports). Medium confidence.
- Tier 4: Self-reported aggregators (Glassdoor, Comparably, Payscale). Low confidence. Fallback only.

A posted range is decision-grade. The same number from Glassdoor with three data points is not. Treating them as equal produces wrong decisions.

## Why a results summary table with lazy detail loading

The full evaluation output is thorough. It is also long. After reading five full reports back to back, the decision of which opportunity to pursue first requires holding too much in memory at once.

The summary table collapses each result to one row: company, role, platform, score, recommendation, salary or hours signal, and the single clause that most explains the score. It is the default output after any search run. No full reports render unless requested.

Detail is on demand. When the user says "show [company]" or "full report [company]", the output renders from what was already computed internally. No re-evaluation. No additional API call.

Token impact: output tokens per search run drop significantly. The reason to make the change is that one table is a better reading experience than five full reports before you have decided what to read.

## What this does not do

Honesty about scope:

- Does not apply for you
- Does not write cover letters (separate skill for that)
- Does not negotiate salary or rates
- Does not track interview prep
- Does not compare two postings side by side (planned)
- Does not surface market trends across evaluations (planned, needs volume)
- Does not learn from outcomes automatically (the data is captured, the analysis is manual)

## Roadmap

The system works. Here is what is on deck to make it work better.

### Near-term

**Interview question ranking.** The lowest-confidence scoring dimension should drive question order. If ownership scored ambiguously, the ownership question leads.

**Outcome Notes overwrite trigger.** Current behavior appends with date prefix, never overwrites. Build `replace notes [company] [text]` when the append-only behavior becomes a problem.

**Fractional rate signal.** Analogous to the salary signal hierarchy for FTE. Posted rate is authoritative. Estimated market rate for fractional PM work at a given scope and hours level is the fallback.

### Mid-term

**Market view rollup.** Trend reports across evaluated postings once volume builds. Score distribution, common stop list hits, conversion rates by scoring dimension and by track. Needs 30 or more evaluations to be meaningful.

**Side-by-side comparison.** When two candidates land at the same time, build a comparison view with deltas surfaced. Recognition beats recall.

**Override capture.** Stop list overrides are not tracked. When a stop list rule gets bypassed, capture the reason and the eventual outcome. Over time, the override data reveals where stated criteria and revealed criteria diverge.

**Outcome-driven rubric refinement.** The instrumentation exists. The analysis does not. After enough outcomes accumulate, surface which scoring dimensions correlate with conversion. This is the learning loop made concrete.

### Strategic

**Engine and profile separation.** Current state: criteria, voice rules, and user context are intermixed with the scoring engine. A general-purpose version needs them decoupled. The reference file is a step in this direction, but the skill still has user-specific assumptions baked in.

**Recognition-based onboarding.** A new user cannot generate stop list and green flag criteria cold. A general-purpose version needs to present example criteria and let new users pick what applies, building their own rubric through recognition rather than generation. This is product design work as much as engineering.

## License

MIT. Build on this.
