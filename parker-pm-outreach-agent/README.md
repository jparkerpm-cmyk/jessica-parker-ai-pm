# Parker PM Outreach Agent

A semi-automated outreach research and drafting agent built for a fractional product management practice. Input one or more company names or URLs. The tool researches each company in parallel batches, scores fit against a defined ideal client profile, identifies the best outreach contact via Apollo and Hunter.io, and drafts a personalized cold email and LinkedIn InMail for each.

Built as a Claude Projects skill using batched sub-agents. Connects to Notion for pipeline management and outputs a structured Excel file for downstream email automation via Cowork.

Contact lookup uses a two-API stack. Apollo's `organization_top_people` endpoint surfaces the right person at a target company by domain. Hunter.io resolves the email when Apollo does not return one. API keys are prompted at runtime and never stored in the skill file.

Designed with deliberate human review gates at each stage. The agent researches, scores, and drafts. The user approves. Automated email sending is a future phase that does not get enabled until the scoring model is validated against real outcomes.

[Decision log](decisions.md) | [Skill definition](skill.md)
