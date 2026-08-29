# Lead Magnet Nurture Sequence

An n8n workflow that automatically nurtures a new lead with a 3-email sequence after they sign up for a free resource — no manual follow-up required.

## Overview

When someone fills out a lead capture form to get a free resource, this workflow personalizes and sends a short welcome/nurture email sequence automatically over the following days: a welcome email, a value/tips email, and a soft-offer email — each written by AI based on the lead's name and stated interest.

## Workflow Steps

1. **LeadForm** — A form trigger capturing:
   - Full Name (required)
   - Email Address (required)
   - Topic of Interest (optional)
2. **GenerateWelcomeEmail** — An LLM (`gpt-oss-120b` via Groq) writes a short, warm welcome email personalized with the lead's name and interest.
3. **RespondToLead** — Immediately shows the lead a confirmation page ("You're In!") so they don't have to wait for the email pipeline to finish.
4. **SendWelcomeEmail** — Sends the generated welcome email via SMTP.
5. **WaitBeforeValueEmail** — Pauses before the next email (demo: 10 seconds; production: 2 days).
6. **GenerateValueEmail** — AI writes a short, genuinely useful tips email related to the lead's interest.
7. **SendValueEmail** — Sends the tips email via SMTP.
8. **WaitBeforeOfferEmail** — Pauses again (demo: 15 seconds; production: 3 days).
9. **GenerateOfferEmail** — AI writes a warm, low-pressure offer/CTA email.
10. **SendOfferEmail** — Sends the offer email via SMTP.

## Tech Stack

- **n8n** (self-hosted, free tier) — workflow automation, form trigger, and Wait nodes for time-delayed sequencing
- **Groq API** (free tier) — LLM inference (`openai/gpt-oss-120b`) for all 3 personalized emails
- **Brevo SMTP** (free tier) — actual email delivery
- **LangChain nodes** (`@n8n/n8n-nodes-langchain`) — LLM chaining

## Setup Notes

- Requires a **Groq API** credential (for the `gpt-oss-120b` model node) and an **SMTP** credential (for the 3 email-sending nodes). Both are placeholders in the exported JSON and must be reconnected to your own credentials after importing.
- The sender email set in each "Send" node must be a **verified sender** in your SMTP provider (e.g. verified in Brevo) or sending will fail.
- The Wait node durations are set to short demo values (seconds) so the full sequence can be tested end-to-end quickly. For real-world use, change them to actual day-based delays (e.g. 2 days, 3 days) and **publish/activate** the workflow so it keeps running in the background between waits.

## Screenshots

- `workflow-screenshot.png` — the full workflow canvas after a successful end-to-end execution.
- `output-example.png` — the confirmation page a lead sees immediately after submitting the form.

## Possible Extensions

- Log every lead into a CRM or Google Sheet to track who's in which stage of the sequence.
- Add conditional branching (e.g. skip the offer email if the lead already replied or booked a call).
- A/B test different subject lines or email styles across leads.
- Add an unsubscribe/opt-out check before each send.

<img width="486" height="432" alt="output-example" src="https://github.com/user-attachments/assets/8e880023-cd67-4bfc-b16c-7fffaa36acc3" />

<img width="960" height="474" alt="workflow-screenshot" src="https://github.com/user-attachments/assets/76f33ed1-585c-4c88-b003-82c38ef8c183" />
