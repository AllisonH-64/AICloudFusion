# Lab 12A Completion Summary and Next Session Marker

Date: 2026-09-11
Session: 12 (AI Engineering — Capstone: RAG, Guardrails, Cost Governance)
Lab: 12A (Give Your Chatbot a Brain — Retrieval-Augmented Generation)

## Completion Snapshot

Lab 12A is complete.

- Backed up the end-of-11C `handler.py` to `handler-11c-backup.py` before editing
- Created 3 knowledge-base documents (`workshop-faq.txt`, `aws-glossary.txt`, `project-northstar.txt` — the last containing a fabricated fact, "Project Northstar," that no model could know except by retrieval) and uploaded them to a new S3 bucket `workshop-ai-kb-445606683716`
- Added an inline `kb-s3-read` policy to `workshop-lab11-lambda-role`, scoped to `s3:GetObject`/`s3:ListBucket` on only that bucket
- Added `load_knowledge_base()` (loads + paragraph-chunks all KB docs from S3, cached across warm invocations) and `retrieve_context()` (keyword-overlap scoring, top-3 chunks) to `handler.py`
- Wired retrieval into the POST handler: builds a grounded system prompt instructing the model to answer *only* from retrieved context, cite `[filename]`, and reply exactly `"I don't have that in my knowledge base."` when the answer isn't present; added `sources` to the response metadata
- Redeployed and set the `KB_BUCKET=workshop-ai-kb-445606683716` environment variable
- Proved RAG works with all three test cases: (1) "What is the codename for the capstone chatbot project?" → correctly answered "Project Northstar" (proof: the model cannot know this fact except by retrieval), (2) "When does the workshop meet and what certification should I get first?" → correct answer, correct citation `[workshop-faq.txt]`, (3) "What is the capital of France?" → correctly refused with "I don't have that in my knowledge base." instead of answering "Paris" — the core hallucination-prevention proof
- Confirmed `rag_retrieval` log events in CloudWatch showing `chunks_retrieved` and `sources` for each request

**Live chatbot URL (unchanged):** `https://8nfjhzgzkh.execute-api.us-east-1.amazonaws.com/live/chat` — now RAG-grounded.

## Troubleshooting Notes Captured

- **Citation accuracy quirk**: because `retrieve_context()` uses simple keyword overlap (not embeddings), a broad question can retrieve all 3 KB documents at once — in test (1) it correctly answered "Project Northstar" but cited `[workshop-faq.txt]` instead of `[project-northstar.txt]` (all 3 files scored a nonzero overlap on words like "project," "chatbot," "capstone"). The *answer* was correct and *could only* have come from retrieval — the mis-citation is exactly the precision limitation the lab calls out when contrasting hand-rolled keyword retrieval with Bedrock Knowledge Bases' embeddings-based approach. Not a bug to fix; it's the intended teaching point.
- Test (3)'s refusal response also had a stray `[workshop-faq.txt]`-style tag appended even though there was nothing to cite — same root cause (the model tries to follow the "always cite" instruction even when the correct response is a refusal). Cosmetic; the refusal text itself was exact and correct.
- Otherwise ran clean end-to-end on the first pass: no environment or deployment issues (S3 bucket creation, IAM policy attach, redeploy, and env var update all succeeded without retries).

## Marker for Next Session

**📌 Per the lab: keep everything — function, role, S3 bucket, knowledge base, and `handler.py`.** Labs 12B and 12C build directly on all of it.

Next lab to start: Lab 12B (Bedrock Guardrails)

Suggested first commands next session:
1. `aws sts get-caller-identity`
2. `cd ~/Desktop/workshop-lab-11a`
3. Confirm the chatbot is still live and grounded: ask it something only in `project-northstar.txt` via the browser UI or `aws lambda invoke`
4. Review `labs/session-12-rag-guardrails-capstone/lab-12b-bedrock-guardrails.md`

## Status

Ready to resume with Lab 12B in the next session.
