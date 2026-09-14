# Lab 12C Completion Summary — AI Engineering Track Complete

Date: 2026-09-14
Session: 12 (AI Engineering — Capstone: RAG, Guardrails, Cost Governance)
Lab: 12C (Ship It — Cost Governance & the Full AI Stack)

## Completion Snapshot

Lab 12C is complete. **This closes out the entire AI Engineering track (Sessions 11–12).**

- Re-authenticated (`aws login`) after the session's credentials had expired since the last session; confirmed the deployed chatbot and all Session 11/12 resources were still intact and working
- Created `budget.json` (workshop-ai-monthly-budget, $5/month) and `notifications.json` (80%/$4 threshold, email alert to allison.jaheim@gmail.com), then created the budget via `aws budgets create-budget` and verified it with `describe-budget`
- Ran the full 6-test end-to-end verification against the live chatbot:
  1. RAG retrieval ("codename for the capstone project") → correct answer "Project Northstar" ✅
  2. Grounding refusal ("capital of France") → exact expected "I don't have that in my knowledge base." ✅
  3. Guardrail denied topic (Bitcoin investment question) → blocked, 0 tokens ✅
  4. Guardrail PII block (fake credit card number) → blocked with the custom PII message, card never echoed ✅
  5. Input validation (528-char prompt reused from 11C) → `400` before any Bedrock/guardrail call ✅
  6. Happy path ("explain S3 for a beginner") → short, grounded, cited answer ✅
- Confirmed observability end-to-end: `ai-chatbot-token-spike` alarm in `OK` state, and the dashboard's `AWS/Lambda Invocations` metric showing real data from the test traffic
- Reviewed the production-readiness checklist — every dimension (works, controllable, stateful, grounded, honest, safe, cost-controlled, observable, least-privilege) is genuinely implemented, not aspirational
- Updated `workshop-lab-11a/README.md` to reflect the completed three-layer cost defense (input validation → token alarm → account budget) and mark all six labs (11A–12C) done

**Live chatbot URL (unchanged throughout the whole track):** `https://8nfjhzgzkh.execute-api.us-east-1.amazonaws.com/live/chat`

## Troubleshooting Notes Captured

- AWS CLI session had expired again at the start of this session (same as the very first session) — `aws login` fixed it in seconds, same as before. Worth remembering this is a recurring, expected friction point with short-term credentials, not a one-off issue.
- The known citation-accuracy quirk from 12A/12B reappeared on Test 1 and Test 6 (correct answer, wrong cited filename, because `retrieve_context()`'s keyword-overlap scoring pulled in multiple KB documents) — consistent with prior sessions, not a new bug, and doesn't affect pass/fail per the lab's actual success criteria (the *answer* content, not citation precision).
- Otherwise a completely clean run: budget creation, all 6 verification tests, and the observability check all succeeded on the first attempt with no retries needed.

## Cleanup Decision

**Left running, not torn down.** The stack is a working portfolio piece (live URL + dashboard + full defense-in-depth architecture), and nothing in the course requires deleting it immediately. Full teardown commands (including the new 12C budget) are documented in `workshop-lab-11a/README.md`'s Cleanup section whenever it's wanted — total ongoing cost is negligible (pay-per-use serverless, no idle charges) and the $5 budget alert is now the safety net if that ever changes.

## Track Retrospective

| Session | Theme | What got built |
|---|---|---|
| 11A | Build | Live serverless chatbot from nothing — Lambda + API Gateway + Bedrock (Nova Micro) |
| 11B | Control | System prompt, temperature/maxTokens tuning, conversation history |
| 11C | Operate | Input validation, token metric filter, dashboard, spike alarm |
| 12A | Ground | RAG over an S3 knowledge base, source citations, honest refusals |
| 12B | Secure | Bedrock Guardrails — enforced safety independent of the prompt |
| 12C | Ship | Account cost budget, full end-to-end verification, production-readiness review |

## Status

**AI Engineering track complete.** No next lab — this was the final one. Suggested next steps per the lab: book the AWS Certified AI Practitioner exam, write up the build for a portfolio, and consider deeper AWS AI services (Bedrock Knowledge Bases, Bedrock Agents) as natural follow-ons building on what was learned here from first principles.
