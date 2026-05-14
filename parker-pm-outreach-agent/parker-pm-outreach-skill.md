
name: parker-pm-outreach

description: "Create this as a skill to get a semi-automated outreach researcher. You paste in one or more companies (name and/or website, maybe LinkedIn URL) Via batched sub-agents: web-searches each one: recent funding, job postings, product news, patient-facing product details. Scores each against your ICP (Series B/C health tech, patient access problem, signs of product chaos or compliance gaps) Drafts a personalized outreach email per company using your email profile Drafts a personalized outreach LinkedIn InMail per company Finds potential best outreach contact. Outputs a review dashboard: score, research summary, draft email, draft InMail, possible best contacts with contact information, One-click to approve add to notion database and an excel spreadsheet. You send from Gmail"

## Parker PM Outreach Tool

**TL;DR**

Trigger: parker pm outreach [companies] or outreach [companies]
Researches each company in parallel batches of 3
Posts one result per company as it finishes, does not wait for all to complete
Scores each company against Jessica's ICP (1-10)
Drafts a cold email and LinkedIn InMail for each
Save trigger: save [company name] logs that company to Notion



**Trigger Phrases**

parker pm outreach [company names or URLs]
outreach [company names or URLs]

**Examples:**
outreach Medefy Health, PointClickCare
parker pm outreach https://linktr.ee/Medefy_Health

___

## What Gets Researched

For each company, Claude runs web searches covering:

What the company does (2-3 sentences)
Recent activity: funding, launches, hires, news in the last 12 months
Product signals: what their product situation looks like from the outside
ICP reasoning: specific fit or no-fit analysis against Jessica's ICP
Best contact: name, title, email, LinkedIn URL, confidence level, how found



## Output Format Per Company

[Company Name] · [website] · ICP Score: [X]/10 · [Strong fit / Possible fit / Weak fit]

**Research**
What they do: [2-3 sentences]
Recent activity: [funding, launches, hires, news — or "Nothing recent found"]
Product signals: [what their product situation looks like from outside]
ICP reasoning: [specific fit/no-fit reasoning against Jessica's ICP]

**Best contact**
Name: [name or Not found]
Title: [title or Not found]
Email: [email or Not publicly available]
LinkedIn: [URL or Not found]
Confidence: [high / medium / low / not found]
Note: [how found or why unavailable]

**ICP signals**
[✓ / ✗ / ?] Health tech
[✓ / ✗ / ?] Patient-facing product
[✓ / ✗ / ?] Series A-C
[✓ / ✗ / ?] Product chaos signals
[✓ / ✗ / ?] Right contact reachable
[✓ / ✗ / ?] AI-enabled

**Cold email**
Subject: [subject line]
[email body]

**LinkedIn InMail**
[inmail body]



## Save Trigger

**save [company name]**

Saves the company in two places at once with one click. Both happen together. Neither is optional.

**Save 1: Notion**
Logs to the Parker PM — Outreach Pipeline database in Notion.

**Save 2: Excel file (Cowork-ready)**
Creates or appends to outreach-pipeline.xlsx in /Users/jessicaparker/claudecowork/outputs/.
If the file already exists, append a new row. Do not overwrite existing rows.
This file is the source of truth for Cowork email processing.

**Fields saved to both destinations (same column order in xlsx):**
Company
Website
ICP Score
Fit
Status (default: Not contacted)
Contact Name
Contact Title
Contact Email
LinkedIn URL
Contact Confidence
What They Do
Recent Activity
Product Signals
ICP Reasoning
ICP Signals (comma-separated: health tech, patient-facing, series A-C, product chaos, right contact, AI-enabled — each prefixed with check, x, or question mark)
Cold Email Subject
Cold Email Body
LinkedIn InMail Body
Date Saved (auto: today's date, YYYY-MM-DD)

**Excel file rules:**
Sheet name: Outreach Pipeline
Row 1: Bold header row with column names above
Freeze row 1
Column widths: auto-fit to content, minimum 15 characters wide
No formulas needed. Plain data only.
After saving, present the file so it is downloadable.

**After both saves complete, confirm:**
Saved to Notion and outreach-pipeline.xlsx — [Company Name] added.

___

## Jessica's ICP (What the Score Is Based On)

**Target companies:**
Early-to-mid-stage health tech (Series A/B/C)
Patient-facing products: enrollment flows, prior auth, onboarding, telehealth, pharmacy, EHR, clinical workflow tools
Signs of product chaos: failed launch, HIPAA gaps, patient abandonment, broken agile, post-acquisition confusion, scope churn, manual processes that keep failing
Decision-makers: founders, CTOs, VPs of Product, Heads of Product, operators
AI-first or AI-enabled health tech is a strong positive signal



**Email and InMail Voice Rules**

Jessica's voice is strict and applies to all outreach drafts.

Direct. Confident. Short sentences. Fragments OK.

Banned words: may, just, very, really, basically, probably, certainly, could, maybe, utilize, leverage, impactful, innovative, exciting, groundbreaking, game-changer, delve, embark, craft, imagine, realm, revolutionize, tapestry, illuminate, landscape, literally, actually, furthermore, hence

Banned openers: "I hope this finds you well" / "I wanted to reach out" / "I came across your company" / "I'd love to connect"

Banned closers: "Looking forward to hearing from you" / "Don't hesitate to reach out" / "Feel free to"

No em dashes. No semicolons. No fake urgency.

**Email structure (5-8 sentences, mobile screen length):**
Specific true observation about their situation
Name the problem that creates
What Jessica does and why it is relevant
One small ask
Stop

**InMail structure (3-5 sentences, shorter than email):**
Specific observation
Problem or gap
Who Jessica is
Ask
Stop



## Notion Database

Database: Parker PM — Outreach Pipeline
Location: Freelance Business Hub
URL: https://www.notion.so/a118347f45e44c729ebded877c961645
Data source ID: f48f6aa1-f501-4770-9882-bcdfa2d69bfd
