# Lab 11C Completion Summary and Next Session Marker

Date: 2026-09-11
Session: 11 (AI Engineering with Bedrock)
Lab: 11C (Token Budgets, Cost Monitoring & Guardrails)

## Completion Snapshot

Lab 11C is complete.

- Added a 500-character input-validation block to `handler.py`, returning a 400 with `{"error", "input_length", "max_allowed"}` before calling Bedrock at all
- Verified a short prompt still passes normally, and a ~528-character prompt is rejected with the expected 400 response — zero Bedrock cost incurred on rejection
- Created `token-transform.json` and a CloudWatch Logs metric filter (`token-usage`) extracting `total_tokens` from `bedrock_call_success` log lines into a custom metric `WorkshopAIChatbot/TotalTokensUsed`
- Ran 5 invocations to seed metric data, then created `workshop-ai-chatbot-dashboard` (4 widgets: invocations, latency, tokens/request, cumulative tokens) and the `ai-chatbot-token-spike` alarm (fires above 500 tokens on a single request)
- Verified end-to-end: a normal request works with reasonable token count and visible latency, the alarm reached `OK` state, and `get-metric-statistics` confirmed real data flowing (5-min window: Sum 443, Max 79 tokens)

## Troubleshooting Notes Captured

- `aws logs put-metric-filter --log-group-name "/aws/lambda/..."` initially failed with `InvalidParameterException ... logGroupName failed to satisfy constraint` — this is Git Bash on Windows (MSYS) auto-converting the leading `/aws/lambda/...` argument into a Windows path before the AWS CLI ever sees it. Fixed by prefixing the command with `MSYS_NO_PATHCONV=1`. (Same root cause as the `aws logs tail` issue hit back in Lab 11A — worth remembering for any future AWS CLI command with a leading-slash argument on this machine.)
- The alarm sat in `INSUFFICIENT_DATA` for a couple of minutes after creation until enough metric data points accumulated — expected per the lab's "wait 2 minutes" guidance, not an error.

## Marker for Next Session

**📌 Per the lab: this file documents the full teardown for Session 11 if stopping.** We are NOT stopping — continuing straight into Lab 12A, which requires this exact end-of-11C state (system prompt + `build_messages` history + input validation all present in `handler.py`).

Next lab to start: Lab 12A (RAG Knowledge Base) — see its own completion summary in `labs/session-12-rag-guardrails-capstone/`.

## Status

Complete. Immediately continued into Lab 12A in the same session — see `lab-12a-completion-summary-2026-09-11.md`.
