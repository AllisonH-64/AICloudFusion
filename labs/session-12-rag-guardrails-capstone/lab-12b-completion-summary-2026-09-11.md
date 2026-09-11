# Lab 12B Completion Summary and Next Session Marker

Date: 2026-09-11
Session: 12 (AI Engineering — Capstone: RAG, Guardrails, Cost Governance)
Lab: 12B (Responsible AI — Bedrock Guardrails)

## Completion Snapshot

Lab 12B is complete.

- Created the four guardrail policy files: `content-policy.json` (Hate/Insults/Sexual/Violence/Misconduct at HIGH, Prompt Attack input HIGH/output NONE), `topic-policy.json` (denied topic "FinancialAdvice"), `pii-policy.json` (BLOCK on credit/debit card and US SSN), `word-policy.json` (managed profanity list)
- Created guardrail `workshop-ai-guardrail` (id `d4ofwohv8b8q`) from those four files, then published version `1` (app pins to the numbered version, not DRAFT)
- Added an inline `guardrail-apply` IAM policy to `workshop-lab11-lambda-role`, scoped to `bedrock:ApplyGuardrail` on only this guardrail's ARN
- Updated `handler.py`: added `GUARDRAIL_ID`/`GUARDRAIL_VERSION` env-var constants, a `blocked_pii_types()` helper that reads the guardrail trace, added `guardrailConfig` (with `"trace": "enabled"`) to the `bedrock.converse()` call, and added post-call handling that swaps in a PII-specific message when `stopReason == "guardrail_intervened"` and `blocked_pii` is non-empty (falls back to the guardrail's own blocked-message otherwise)
- Redeployed and set all three env vars together (`KB_BUCKET`, `GUARDRAIL_ID`, `GUARDRAIL_VERSION`) — `update-function-configuration` replaces the full set, so `KB_BUCKET` had to be re-specified to avoid silently losing the 12A RAG setup
- Verified all four required test cases: (1) normal AWS question → passes through, correct grounded/cited answer; (2) "Should I invest in Bitcoin?" → blocked with the guardrail's default message, 0 tokens consumed; (3) message containing a fake credit-card number → blocked with the custom PII-specific message, card number never echoed back; (4) explicit jailbreak attempt ("ignore all previous instructions...") → blocked by the PROMPT_ATTACK filter, proving the guardrail holds even when a system prompt alone could be argued out of it
- Confirmed `guardrail_intervened` log entries for all three blocked cases, with the specific PII type (`CREDIT_DEBIT_CARD_NUMBER`) recorded in the log for operators despite never being shown to the end user

**Live chatbot URL (unchanged):** `https://8nfjhzgzkh.execute-api.us-east-1.amazonaws.com/live/chat` — now guardrail-enforced.

## Troubleshooting Notes Captured

- No errors this lab — guardrail creation, version publish, IAM attach, and all four test cases worked on the first attempt.
- Re-confirmed the `MSYS_NO_PATHCONV=1` workaround is needed for `aws logs filter-log-events --log-group-name "/aws/lambda/..."` on this machine's Git Bash (same root cause noted in the 11C summary).
- Deliberately re-tested that `KB_BUCKET` survived the `update-function-configuration` call in Step 5d, since the lab explicitly warns this command replaces all env vars — confirmed via `get-function-configuration` that all three vars were present before running the guardrail tests.

## Marker for Next Session

**📌 Per the lab: keep everything.** Lab 12C assembles the complete stack and is also where the final teardown for the whole capstone lives.

Next lab to start: Lab 12C (Capstone — Cost Governance)

Suggested first commands next session:
1. `aws sts get-caller-identity`
2. `cd ~/Desktop/workshop-lab-11a`
3. Confirm the guardrail is still active: ask a financial-advice question via the browser UI and confirm it's blocked
4. Review `labs/session-12-rag-guardrails-capstone/lab-12c-capstone-cost-governance.md`

## Status

Ready to resume with Lab 12C in the next session — the final lab in this course.
