---
name: parker-pm-outreach
description: "Semi-automated outreach researcher. Paste in companies (name, website, maybe LinkedIn URL). Via batched sub-agents: web-searches each one — funding, job postings, product news, patient-facing details. Scores each against Jessica's ICP (Series B/C health tech, patient access problem, product chaos signals). Drafts cold email and LinkedIn InMail per company. Finds best outreach contact via Apollo and Hunter.io APIs. Outputs a review dashboard per company. Strong fits (score 8+) are automatically saved to Notion outreach pipeline. All others require explicit save request."
---

# Parker PM Outreach Tool

## TL;DR

Trigger: `parker pm outreach [companies]` or `outreach [companies]`
- Prompts for Apollo and Hunter API keys at the start of every run (skipped if already provided this session)
- Researches each company in parallel batches of 3
- Posts one result per company as it finishes, does not wait for all to complete
- Scores each company against Jessica's ICP (1–10)
- Looks up best contact via Apollo then Hunter
- Drafts a cold email and LinkedIn InMail for each
- Strong fits (score 8+/10) are auto-saved to Notion after the full batch completes
- Possible/Weak fits: use `save [company name]` to manually add to Notion

---

## Trigger Phrases

```
parker pm outreach [company names or URLs]
outreach [company names or URLs]
```

Examples:
```
outreach Medefy Health, PointClickCare
parker pm outreach https://linktr.ee/Medefy_Health
```

---

## API Key Prompt (Runtime)

At the start of every outreach run, before research begins, prompt:

```
Before we start: I need your Apollo and Hunter API keys for contact lookup.

Apollo API key:
Hunter API key:

These are used only for this session and never stored.
```

If both keys were already provided earlier in this session, skip the prompt and use what was provided.

---

## What Gets Researched

For each company, run web searches covering:

- What the company does (2–3 sentences)
- Recent activity: funding, launches, hires, news in the last 12 months
- Product signals: what their product situation looks like from the outside
- ICP reasoning: specific fit or no-fit analysis against Jessica's ICP
- Best contact: looked up via Apollo and Hunter (see Contact Lookup section)

---

## Contact Lookup

### Target Titles (filter in priority order)

```
Founder
Co-Founder
Chief Product Officer
CPO
VP of Product
Head of Product
VP Engineering
Head of Engineering
CTO
Chief Technology Officer
Director of Product
```

### Lookup Logic

**Step 1: Apollo organization_top_people**

```
POST https://api.apollo.io/api/v1/organizations/top_people
Header: X-Api-Key: [apollo key]
Body: {"domain": "[company domain]"}
```

Filter returned people by target titles (case-insensitive partial match).
Take the best title match based on priority order above.

- If email is present on the matched person: use it. Confidence = high.
- If email is missing on the matched person: proceed to Step 2.
- If Apollo returns no results: proceed to Step 3.

**Step 2: Hunter Email Finder (Apollo found person, no email)**

```
GET https://api.hunter.io/v2/email-finder
Params: domain=[domain]&first_name=[first]&last_name=[last]&api_key=[hunter key]
```

- If Hunter returns an email: use it. Confidence = medium.
- If Hunter finds nothing: use name, title, and LinkedIn URL from Apollo. Email = Not found. Confidence = low.

**Step 3: Hunter Domain Search (Apollo returned nothing)**

```
GET https://api.hunter.io/v2/domain-search
Params: domain=[domain]&api_key=[hunter key]
```

Filter returned contacts by target titles. Use best title match.

- If email present: use it. Confidence = medium.
- If no title match but emails exist: use highest confidence result.
- If Hunter also returns nothing: all contact fields = Not found. Confidence = not found.

### Contact Output Format

```
Name: [name or Not found]
Title: [title or Not found]
Email: [email or Not found]
LinkedIn: [URL or Not found]
Confidence: [high / medium / low / not found]
Note: [source: Apollo + email present / Apollo + Hunter email finder / Hunter domain search / not found]
```

---

## Output Format Per Company

```
[Company Name] · [website] · ICP Score: [X]/10 · [Strong fit / Possible fit / Weak fit]

Research
What they do: [2–3 sentences]
Recent activity: [funding, launches, hires, news — or "Nothing recent found"]
Product signals: [what their product situation looks like from outside]
ICP reasoning: [specific fit/no-fit reasoning against Jessica's ICP]

Best contact
Name: [name or Not found]
Title: [title or Not found]
Email: [email or Not found]
LinkedIn: [URL or Not found]
Confidence: [high / medium / low / not found]
Note: [source: Apollo + email present / Apollo + Hunter email finder / Hunter domain search / not found]

ICP signals
[✓ / ✗ / ?] Health tech
[✓ / ✗ / ?] Patient-facing product
[✓ / ✗ / ?] Series A-C
[✓ / ✗ / ?] Product chaos signals
[✓ / ✗ / ?] Right contact reachable
[✓ / ✗ / ?] AI-enabled

Cold email
Subject: [subject line]
[email body]

LinkedIn InMail
[inmail body]
```

---

## Auto-Save Behavior (Strong Fits)

After all companies in the batch have been researched and displayed:

Strong fit = ICP score 8 or higher.

For every strong fit company, automatically save to both Notion and the Excel file without waiting for a user request. After saving, confirm inline:

```
Auto-saved to Notion + outreach-pipeline.xlsx — [Company Name] (score: X/10 · Strong fit)
```

Then show a summary at the end of the batch:

```
Auto-saved X strong fit(s) to Notion: [Company A], [Company B]
Not saved (score below 8): [Company C] — say "save [Company C]" to add it
```

---

## Manual Save Trigger

```
save [company name]
```

For companies that were not auto-saved (Possible fit or Weak fit). Saves to both Notion and Excel.

After saving, confirm:

```
Saved to Notion and outreach-pipeline.xlsx — [Company Name] added.
```

---

## What Gets Saved (Both Destinations)

Fields saved to Notion and Excel (same column order in xlsx):

1. Company
2. Website
3. ICP Score
4. Fit
5. Status (default: Not contacted)
6. Contact Name
7. Contact Title
8. Contact Email
9. LinkedIn URL
10. Contact Confidence
11. What They Do
12. Recent Activity
13. Product Signals
14. ICP Reasoning
15. ICP Signals (comma-separated: health tech, patient-facing, series A-C, product chaos, right contact, AI-enabled — each prefixed with ✓, ✗, or ?)
16. Cold Email Subject
17. Cold Email Body
18. LinkedIn InMail Body
19. Date Saved (auto: today's date, YYYY-MM-DD)

---

## Excel File Rules

- File: `/Users/jessicaparker/claudecowork/outputs/outreach-pipeline.xlsx`
- Sheet name: `Outreach Pipeline`
- Row 1: Bold header row, frozen
- Column widths: auto-fit, minimum 15 characters wide
- If file exists: append rows, never overwrite existing data
- After saving: present the file so it is downloadable

---

## Jessica's ICP (Scoring Basis)

Target companies:
- Early-to-mid-stage health tech (Series A/B/C)
- Patient-facing products: enrollment flows, prior auth, onboarding, telehealth, pharmacy, EHR, clinical workflow tools
- Signs of product chaos: failed launch, HIPAA gaps, patient abandonment, broken agile, post-acquisition confusion, scope churn, manual processes that keep failing
- Decision-makers: founders, CTOs, VPs of Product, Heads of Product, operators
- AI-first or AI-enabled health tech is a strong positive signal

Score 8–10 = Strong fit. Score 5–7 = Possible fit. Score 1–4 = Weak fit.

---

## Email and InMail Voice Rules

Jessica's voice is strict. Applies to all outreach drafts.

Direct. Confident. Short sentences. Fragments OK.

Banned words: may, just, very, really, basically, probably, certainly, could, maybe, utilize, leverage, impactful, innovative, exciting, groundbreaking, game-changer, delve, embark, craft, imagine, realm, revolutionize, tapestry, illuminate, landscape, literally, actually, furthermore, hence

Banned openers: "I hope this finds you well" / "I wanted to reach out" / "I came across your company" / "I'd love to connect"

Banned closers: "Looking forward to hearing from you" / "Don't hesitate to reach out" / "Feel free to"

No em dashes. No semicolons. No fake urgency.

Email structure (5–8 sentences, mobile screen length):
1. Specific true observation about their situation
2. Name the problem that creates
3. What Jessica does and why it is relevant
4. One small ask
5. Stop

InMail structure (3–5 sentences, shorter than email):
1. Specific observation
2. Problem or gap
3. Who Jessica is
4. Ask
5. Stop

---

## Notion Database

- Database: Parker PM — Outreach Pipeline
- Location: Freelance Business Hub
- URL: https://www.notion.so/a118347f45e44c729ebded877c961645
- Data source ID: f48f6aa1-f501-4770-9882-bcdfa2d69bfd
