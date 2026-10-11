# Test Scenarios & Results

Speak like a real customer: use fillers, full sentences, and don't answer in one word.
Priority **P1** = must record (covers the assignment's mandatory behaviours). **P2** = record if time allows.

Test variables used: customer_name = Rahul Sharma, agent_name = Priya, agent_gender = female, language_to_speak = English (do one Hindi call too).

| # | Pri | Scenario | What to say (example) | Expected outcome | Result | Recording / transcript |
|---|-----|----------|-----------------------|------------------|--------|------------------------|
| 1 | P1 | Happy path, eligible | "Yes, speaking." / "Sure, go ahead." / "Umm, it's a residential flat." / "It's in my name only." / "Yeah, originals are at home." / "I need around fifty lakhs." / "I'm salaried, salary comes to my bank." / "Worth about one crore." / "Ten years." | All 7 collected in order, then senior-expert handoff, call ends | | |
| 2 | P1 | Agricultural property | "It's agricultural land, my family farm." | Polite not-eligible message with brief reason, call ends, no further questions | | |
| 3 | P1 | Cash income | "I run a small shop, and most of my income is in cash." | Disqualified immediately, call ends | | |
| 4 | P1 | Existing loan (transfer) | "I already have a loan on this property." | Transfer-specialist message, call ends, no eligibility questions | | |
| 5 | P1 | EMI reduction (transfer) | "Actually I want to reduce my current EMI." | Transfer-specialist message, call ends | | |
| 6 | P1 | Loan above 75 lakh, accepts | "I need ninety lakhs." then "Yeah okay, seventy-five is fine." | Explains 75 lakh cap, asks to proceed, records 75 lakh, continues | | |
| 7 | P1 | Loan above 75 lakh, declines | "I need a crore." then "No, then I'm not interested." | Explains cap, asks, polite goodbye, ends | | |
| 8 | P1 | Busy customer | "Yes this is Rahul, but I'm in a meeting right now." | "What time should we call you back?" -> captures time, confirms, ends. No pitch | | |
| 9 | P1 | Out-of-order answers | "It's a residential house worth about 1.2 crore, jointly owned with my wife." | Stores type + value + joint; next question is ONLY original documents | | |
| 10 | P2 | Documents missing | "I only have photocopies." | Disqualified, call ends | | |
| 11 | P2 | Tenure too short | "Two years." | Disqualified, call ends | | |
| 12 | P2 | Tenure too long | "Twenty years." | Disqualified, call ends | | |
| 13 | P2 | Joint ownership accepted | "It's jointly owned with my brother." | NOT disqualified, continues | | |
| 14 | P2 | Interest rate diversion | "Before that, what interest rate do you offer?" | No rate invented; says senior expert will share; returns to the next missing question | | |
| 15 | P2 | Unrelated / RAG question | "Where's your nearest branch?" | Answers only if in RAG, else defers; returns to flow | | |
| 16 | P2 | Contradiction | "It's residential... actually no, it's agricultural land." | Latest answer wins -> disqualified | | |
| 17 | P2 | Multi-answer in one breath | "It's my own residential property, originals are available, I need 40 lakhs and I'd prefer 10 years." | Captures 5 facts; asks only for occupation + income mode, then market value | | |
| 18 | P2 | Interruption | Interrupt the agent: "Wait, what documents do you need?" | Answers briefly, returns to checklist | | |
| 19 | P2 | Ambiguous documents | "I think the papers are probably somewhere." | Does NOT assume yes; asks one clarification | | |
| 20 | P2 | Wrong person | "No, Rahul is not here, I'm his brother." | No offer details disclosed; asks for callback time; ends | | |
| 21 | P2 | Hindi call | Set language_to_speak = Hindi, run scenario 1 in Hinglish | Agent replies in Hindi with correct gender forms | | |
| 22 | P2 | Premature handoff check | Give 6 answers, skip tenure | Agent must ask tenure before handoff | | |

## What to check in every call (pass criteria)
- Agent speaks first and verifies the customer before anything else
- Never asks something the customer already answered
- Never continues after a disqualification or transfer trigger
- Never invents interest rates, fees or a market-value minimum
- Never says "qualified" before all 7 items are collected
- Short, natural turns; one question at a time
- Gender and language stay consistent

## Issues found and fixes made
(Fill this in as you test. Evaluators love seeing this.)

| Call # | Problem observed | Prompt change made | Re-test result |
|--------|------------------|--------------------|----------------|
| | | | |
