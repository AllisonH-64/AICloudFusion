# Lab 11B Completion Summary and Next Session Marker

Date: 2026-09-11
Session: 11 (AI Engineering with Bedrock)
Lab: 11B (Prompt Engineering & System Prompts)

## Completion Snapshot

Lab 11B is complete.

- Created `payload.json` and reconfirmed the Lab 11A chatbot still worked via `aws lambda invoke`
- Added a system prompt (friendly AWS tutor, ≤3 sentences, redirect off-topic) to the `bedrock.converse()` call; lowered `maxTokens` 200→150 and `temperature` 0.7→0.5
- Verified on-topic questions get short beginner-friendly analogies, off-topic questions get politely redirected, and an explicit "give me 10 examples" override attempt still stayed brief (maxTokens as hard backstop)
- Swapped the system prompt twice more to prove "same model, same code, different prompt = different bot": a pirate persona (nautical metaphors, ends in "Arrr!") and a strict-interviewer persona (responds with a probing question instead of an answer)
- Added `build_messages(body, current_message)` and wired `messages=build_messages(body, user_message)` into the converse call for multi-turn conversation history
- Proved statelessness vs. memory: asking "What is my name?" with no `history` gets a generic non-answer; the same question with a hand-written `history` array gets "Your name is Allison" — input tokens jumped 33→62, confirming the history was actually sent

**Live chatbot URL (unchanged from 11A):** `https://8nfjhzgzkh.execute-api.us-east-1.amazonaws.com/live/chat`

## Troubleshooting Notes Captured

- At `temperature: 0.5`, the with-history "what is my name" test occasionally (~1 in 3 tries) got a vague analogy answer instead of directly stating the name, even though the correct context was present. This is model sampling variance, not a code or history-plumbing bug — confirmed by retrying twice and getting the correct direct answer both times, with identical (62) input-token counts throughout, proving the history was consistently included regardless of how the model chose to phrase its reply.
- The strict-interviewer persona didn't perfectly obey "one question only" (it added a sentence of preamble before the question) — again model imperfection with instruction-following, not a bug in the prompt wiring.
- No environment/tooling issues this lab — straightforward zip + `update-function-code` redeploy cycle each step.

## Marker for Next Session

**📌 Per the lab: keep the function and role.** Lab 11C builds on this same chatbot.

Next lab to start: Lab 11C (Token Budgets, Cost Monitoring & Guardrails)

Suggested first commands next session:
1. `aws sts get-caller-identity`
2. `cd ~/Desktop/workshop-lab-11a`
3. Review `labs/session-11-ai-engineering/lab-11c-ai-monitoring.md`

## Status

Ready to resume with Lab 11C in the next session.
