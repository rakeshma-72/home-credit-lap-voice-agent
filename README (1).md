# Home Credit LAP Pre-Qualification AI Voice Agent

Assignment submission for **AI Intern (Prompt Engineer)** at SalesAgents AI.

An AI voice agent for Home Credit that calls existing customers about a pre-approved Loan Against Property (LAP) offer of up to 75 lakh. It verifies the customer, collects 7 eligibility data points, disqualifies ineligible customers immediately, routes existing-loan and EMI-reduction cases to a transfer specialist, and hands qualified leads to a senior loan expert.

## Submission links

| Item | Link |
|------|------|
| System prompt | [`system_prompt.md`](./system_prompt.md) |
| Live agent (Retell Voice Orb link) | PASTE_RETELL_LINK_HERE |
| Screen recording, call recordings and transcripts (Google Drive) | PASTE_DRIVE_LINK_HERE |
| Test scenarios and results | [`test_scenarios.md`](./test_scenarios.md) |

**Voice platform:** Retell AI (single-prompt voice agent)

## Repository structure

```
.
├── README.md             # Approach, rules, assumptions, results
├── system_prompt.md      # Final system prompt (with all required variables)
└── test_scenarios.md     # Test matrix, results, issues found and fixes
```

## Approach

I treated the prompt as a small conversation state machine written in natural language, not as a script.

**Per-turn loop (the core idea):**

> Extract first, validate second, update state third, ask the next missing question fourth.

1. **Extract** every eligibility fact from what the customer just said, including out-of-order facts, corrections, fillers and multi-answer sentences.
2. **Validate** each fact against explicit rules (decision table in Section 8 of the prompt).
3. **Update** an internal state (8 data fields plus flags such as `disqualified` and `transfer_required`). The latest clear answer overwrites older ones.
4. **Ask** only the earliest unanswered item, so nothing is asked twice and nothing is skipped.

**Sections in the prompt:** variables, role and objective, identity/language/voice, order of precedence, per-turn procedure, call flow (verification, busy, offer, 7 questions, handoff), answer interpretation, transfer logic, eligibility rules and disqualification, internal state, diversions and interruptions, call termination, Hindi reference lines, and a final self-check.

**Order of precedence** when several things happen in one message: wrong person, busy, not interested, disqualify, transfer, diversion, next question. This removes ambiguity when a customer says several things at once.

## Eligibility rules implemented

| Item | Eligible | Not eligible |
|---|---|---|
| Property type | Residential, Commercial, Industrial | Agricultural |
| Ownership | Sole, Joint | none |
| Original documents | Available for verification | Not available |
| Loan amount | Up to 75,00,000 | Above: explain the cap and ask to proceed with 75 lakh |
| Occupation | Salaried, Self-employed | Neither |
| Income mode | Bank | Cash |
| Market value | Captured only, no threshold | none |
| Tenure | 3 to 15 years | Under 3 or over 15 |

## Design decisions and assumptions

- **No invented rules:** no minimum market value, no interest rate, no loan-to-value logic. Anything not in the brief is deferred to the senior loan expert.
- **Transfer detection is passive:** the brief says "if a customer mentions" an existing loan or EMI reduction, so the agent listens for it at any point instead of adding an extra question. Ambiguous mentions (for example "I have a home loan") trigger one clarifying question.
- **Documents:** the test is availability for verification, not holding them on the call. Vague answers trigger one clarification and are never assumed to be "yes".
- **Mixed income (bank and cash):** one clarifying question; predominantly cash is treated as Cash.
- **Neither salaried nor self-employed** (for example unemployed): treated as not meeting the criterion, since the brief lists only those two.
- **Loan above 75 lakh** is never a rejection. The agent explains the cap and asks to proceed with 75 lakh. A "no" ends the call politely.
- **Handoff gate:** the agent never says "qualified" until all 7 items are collected and passed.
- **Privacy:** wrong-person calls reveal nothing about the offer. The agent never asks for OTP, PAN, Aadhaar or account numbers.
- **Language and gender:** driven by `language_to_speak` and `agent_gender`, with explicit Hindi gender-form rules and Devanagari output for clean text-to-speech.
- **Variables:** all required variables are used and nothing is hardcoded: `company_name`, `customer_name`, `agent_name`, `agent_gender`, `current_date`, `current_day`, `current_time`, `additional_context_from_rag`, `language_to_speak`, `conversation_history`, `customer_utterance`.

## Note on the live demo agent

`system_prompt.md` is the version with all `{{variables}}`. The live demo agent on Retell has the variable values filled in directly (customer: Rahul Sharma, agent: Priya, female, English, Home Credit), because Retell's public share link does not pass dynamic variables.

## How I deployed and tested

1. Created a Retell AI account and a new single-prompt voice agent.
2. Pasted the system prompt, selected a professional female voice, and set the agent to speak first.
3. Kept the `end_call` function so the call hangs up after the closing line.
4. Ran multiple web calls acting as a natural customer (fillers, interruptions, corrections, multi-answer sentences).
5. Logged issues, fixed the prompt, and re-tested. See [`test_scenarios.md`](./test_scenarios.md).
6. Published the agent and shared it through a Voice Orb link.

## Test summary

Full scripts and results are in [`test_scenarios.md`](./test_scenarios.md). Recordings, transcripts and the screen recording are in the Google Drive folder linked above.

| Scenario | Expected behaviour | Result |
|---|---|---|
| Eligible customer | All 7 collected, senior-expert handoff, call ends | |
| Agricultural property | Polite disqualification, call ends | |
| Cash income | Polite disqualification, call ends | |
| Existing loan | Transfer-specialist message, call ends | |
| EMI reduction | Transfer-specialist message, call ends | |
| Loan above 75 lakh | Explains cap, asks to proceed with 75 lakh | |
| Busy customer | Asks callback time, confirms, ends | |
| Out-of-order answers | Stores them, asks only the next missing item | |
