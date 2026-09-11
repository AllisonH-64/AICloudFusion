# Lab 11A Completion Summary and Next Session Marker

Date: 2026-09-11
Session: 11 (AI Engineering with Bedrock)
Lab: 11A (Your First AI Chatbot — Call Amazon Bedrock from Lambda)

## Completion Snapshot

Lab 11A is complete.

- Verified AWS CLI authentication (`aws sts get-caller-identity`) and tested Amazon Bedrock Nova Micro directly with `aws bedrock-runtime converse`
- Created `workshop-lab11-lambda-role` with `AWSLambdaBasicExecutionRole` attached plus an inline `bedrock-invoke` policy scoped to only the Nova Micro model ARN
- Created `handler.py` — a single Lambda that serves the chat UI HTML on GET, calls Bedrock and returns AI responses on POST, and handles CORS preflight on OPTIONS
- Deployed the Lambda as `workshop-ai-chatbot-lab11` (Python 3.12, 256 MB, 30s timeout)
- Built the public endpoint with API Gateway REST API `workshop-ai-chatbot`: `/chat` resource with GET/POST/OPTIONS methods, `AWS_PROXY` integrations to the Lambda, `lambda:InvokeFunction` permission for API Gateway, deployed to the `live` stage
- Verified end-to-end in a browser: opened the chatbot URL, asked "What is S3?", got a correct AI response with latency/token metadata displayed
- Verified structured logging in CloudWatch: `request_received` → `bedrock_call_success` (with `api_latency_ms`, `input_tokens`, `output_tokens`, `total_tokens`) → `request_completed`

**Live chatbot URL:** `https://8nfjhzgzkh.execute-api.us-east-1.amazonaws.com/live/chat`

## Troubleshooting Notes Captured

- First pass at this lab was built with SAM/CloudFormation + an HTTP API (v2) + a separately-hosted static HTML file, before the lab document was pulled from upstream. It worked, but didn't match the lab: wrong resource names, REST API vs HTTP API, and critically the UI wasn't served by the same Lambda (so "share this URL" wouldn't have worked for someone else). Deleted that CloudFormation stack and S3 artifacts bucket, then rebuilt from scratch following the lab's exact CLI steps.
- Git Bash on Windows has no `zip` binary — used Python's `zipfile` module in place of `zip function.zip handler.py` (same effect; `Compress-Archive` is the PowerShell equivalent the lab documents).
- Waited ~10 seconds after `iam create-role` before creating the Lambda function, per the lab's IAM propagation warning — no `InvalidParameterValueException` (role not assumable) encountered.

## Marker for Next Session

**📌 Per the lab: keep the function, role, and API Gateway — do NOT run Cleanup.** Lab 11B updates this same chatbot.

Next lab to start: Lab 11B (Prompt Engineering)

Suggested first commands next session:
1. `aws sts get-caller-identity`
2. Confirm the chatbot is still live: open `https://8nfjhzgzkh.execute-api.us-east-1.amazonaws.com/live/chat` in a browser
3. Review `labs/session-11-ai-engineering/lab-11b-prompt-engineering.md`

## Status

Ready to resume with Lab 11B in the next session. Resources are intentionally left running (Nova Micro is fractions of a cent per request).
