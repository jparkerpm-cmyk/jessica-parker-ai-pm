## Product Decision Log: Parker PM Outreach Agent

Built by: Jessica Parker
Type: Personal tool / fractional practice operations
Status: Shipped. In active use. Automation iterations in progress.

---

## What It Is

A semi-automated outreach research and drafting agent built for a fractional product management practice. The user inputs one or more company names or URLs. The tool researches each company in parallel, scores fit against a defined ideal client profile, identifies the best outreach contact, and drafts a personalized cold email and LinkedIn InMail for each. Results are reviewed by the user. Approved companies are saved to Notion and an Excel file with a single save command.

The tool runs on the Claude API using batched sub-agents. It connects to Notion for pipeline management and outputs an Excel file structured for downstream email automation via Cowork.

The user is me.

---

## Why I Built It

Fractional and consulting PM work is new territory. The work itself is not new. Finding clients is. Sales does not come from the same instincts as product management, and outreach at any volume requires research, personalization, and consistency across every company you contact.

The manual version of this process is not sustainable. Researching a company, finding the right contact, writing a personalized email that does not sound like every other cold outreach email, and tracking where each company is in the pipeline is a significant time investment per company before a single conversation happens.

The tool exists to compress that investment without sacrificing the personalization that makes outreach worth sending.

---

## Constraint-Driven Decisions

**Single input, parallel output**

The first iteration researched one company at a time and waited for each to complete before starting the next. That worked but it was slow. The user experience was sequential when the underlying task was parallelizable.

The second iteration deployed sub-agents to research multiple companies simultaneously. Sub-agents consume tokens quickly, so batching was the constraint. Three companies per batch was the starting point: enough parallelization to improve speed meaningfully, conservative enough to avoid hitting token limits mid-process. When tokens are exhausted the tool holds and waits before continuing the next batch.

Three is not a permanent number. It is the right starting point for testing. Batch size is a candidate for future iteration once the tool has more usage data.

**Output structure designed upfront**

The output format was not discovered through iteration. It was designed before the first build: ICP score, research summary, product signals, contact details, cold email, LinkedIn InMail, all per company.

The reasoning was straightforward. Outreach requires both email and LinkedIn depending on the contact's presence and preference. Designing for both from the start avoided rebuilding the output structure later. The ICP signal checklist was added to make the scoring transparent, not just a number. A score of 7 means more when you can see which signals drove it.

**Two save destinations, one command**

Approved companies save to two places simultaneously: Notion and an Excel file. The save trigger is a single command. Neither destination is optional.

Notion is the human-facing pipeline. It is where the practice lives: potential clients, active clients, project data, relationship history. It is built for review, not automation.

The Excel file is the automation layer. It is structured specifically for Cowork, the tool that will eventually handle automated email sending. The fields, column order, and sheet structure match what Cowork needs to process the file without manual reformatting.

The two destinations serve different purposes. Notion is for the PM managing the practice. Excel is for the system that will eventually run without the PM in the loop.

**Manual input over automated scraping**

The tool requires manual company input. The alternative was automated scraping: pulling potential clients from web sources, job boards, or funding databases without human input.

Automated scraping was evaluated and rejected at this stage for two reasons. First, it requires additional APIs with real dollar costs that are not justified until the tool has proven its value in the manual version. Second, and more important, the scoring and research quality needs to be validated before the input pipeline is automated. If the tool is pulling the wrong companies and the research is producing inaccurate scores, automated scraping compounds the problem at scale.

A future iteration will use job board connectors to surface potential clients from relevant postings. That enhancement is staged after the current validation phase is complete.

**Human review gate before every automation step**

This is the most deliberate structural decision in the build.

The tool does not send emails. It drafts them. The user reviews every draft before anything is sent. The save trigger is manual. The email sending is manual. The automation of email sending is a future phase that has not been enabled yet.

This was not a technical limitation. It was a product decision.

AI agents make it easy to build toward full automation from day one. The technology supports it. The temptation is to build the end state immediately. The problem is that full automation before validation means errors happen at scale before anyone catches them. A miscalibrated scoring model sending outreach to companies that do not fit. A drafted email that gets the company's situation wrong. A contact found with low confidence who turns out to be the wrong person.

Each phase of automation in this tool has to earn the right to run without a human in the loop. The research runs automatically. The scoring is reviewed by the user. The drafts are reviewed before sending. Automated email sending is the final phase and it does not get enabled until every preceding step has been validated in practice.

This is how I build any product. You do not hand off a step to automation until you understand exactly what it does and what it gets wrong.

**API-based contact lookup over web search**

The first version of the tool found contacts through web search: scraping LinkedIn, company pages, and whatever public sources surfaced a name and title. The results were inconsistent. Confidence was low. Emails almost never appeared. The contact block was the weakest part of every output.

The replacement is a two-API stack: Apollo for people discovery, Hunter.io for email resolution.

Apollo's `organization_top_people` endpoint returns the most prominent people at a company by domain. For the companies in this ICP — early-to-mid-stage health tech with 50 to 200 employees — that list reliably surfaces the founder, CTO, and VP of Product. The tool filters by target titles in priority order and takes the best match.

Apollo free tier does not include prospecting search across their full database. The `organization_top_people` endpoint is available on free and is the right tool for this use case. The goal is not to search millions of people. It is to find who runs product at a specific small company. Top people at the org is the right lens.

Hunter.io fills the email gap. When Apollo returns a person without an email, Hunter's email finder takes the name and domain and returns the most likely address with a confidence score. When Apollo returns nothing, Hunter's domain search returns all addresses found for that domain, filtered by title.

The combined stack produces a contact with a real email address in most cases. When it does not, the output says so explicitly rather than guessing.

API keys are never stored in the skill file. They are prompted at runtime at the start of every outreach session. This was a deliberate security decision. A skill file that lives in a repo, gets shared, or ends up in a backup is a credential exposure risk if keys are hardcoded. Runtime prompt eliminates that risk. The keys exist only in the session.

---

## What Broke in Actual Use

The Excel file dropped to the wrong location. Cowork is configured to read from a specific file path. The first version of the tool wrote the Excel file to a different location. The save completed successfully but the file was not where Cowork expected it. This is a plumbing issue, not a logic issue, and it is on the fix list before automated email sending is enabled.

Automated scraping hit a cost wall. The first instinct was to pull potential clients automatically from web sources. That required APIs with usage costs that were not justified at this stage of the tool's development. Manual input is the right call for now and is free. The limitation is accepted deliberately.

Web search contact lookup did not produce reliable results. Names surfaced inconsistently. Emails almost never appeared. Confidence was low enough that the contact block added noise more than signal. The API-based replacement is covered in the decision above.

---

## What I Would Build Next and Why

**Validate and recalibrate the ICP scoring**
The current scoring returns a 1–10 fit score. Companies above 5 move forward. That threshold is a starting assumption, not a validated number. The next iteration will review scored companies against real outreach outcomes to determine whether the model is identifying the right targets. Scoring recalibration happens before any further automation.

**Automated email sending via Cowork**
The Excel output is already structured for this. The trigger is not readiness of the technology. It is confidence in the scoring. When the ICP model has been validated against enough real companies, the email sending step gets automated. Not before.

**Fix the Excel file path**
Small fix, high priority. The file needs to land where Cowork expects it before automated sending is enabled.

**Job board connectors for automated company sourcing**
Claude has connectors for job boards that could surface potential clients from relevant postings without manual input. This enhancement is staged after the scoring validation phase is complete. Automating the input pipeline before the scoring model is calibrated would compound any errors.

**Batch size tuning**
Three companies per batch is the starting point. With more usage data, the right batch size becomes knowable. This is a low-priority tuning item until the core workflow is validated.

**Apollo paid tier evaluation**
The free tier `organization_top_people` endpoint covers the current use case well. If contact coverage gaps emerge at scale, the paid tier unlocks full prospecting search across Apollo's database. That evaluation is staged after the tool has enough usage data to know whether coverage is actually the constraint.

---

## What This Is Actually About

Every decision in this tool follows the same principle: automate one step at a time, validate before moving to the next, and keep a human in the loop until the system has earned the right to run without one.

But earning that trust is not the same across every step. Some steps can be trusted quickly. The cost of an error is low, the mistake is visible, and the fix is fast. Others take longer. And in healthcare, some steps should never be fully automated regardless of how well the system performs in testing. The consequences of a wrong output are not a bad email. They are a patient safety event, a PHI routing error, a compliance failure that cannot be undone.

The product decision is not just when to automate. It is which steps are candidates for automation at all, which require human judgment that no model should replace, and which need a structural gate that makes the error impossible rather than unlikely.

That thinking does not change based on the domain. The stakes change. The threshold for trust changes. The principle stays the same.

The gap between a working AI agent and a trustworthy one is not a technical gap. It is a product gap. Closing it requires the same diagnostic thinking as any other product problem: define what the system needs to do, build the smallest version that does it, observe what breaks, and earn each next step. In healthcare, some of those steps never get fully earned. That is not a failure of the technology. It is the right product decision.
