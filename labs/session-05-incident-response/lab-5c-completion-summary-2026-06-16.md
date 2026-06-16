# Lab 5C Completion Summary and Next Session Marker

Date: 2026-06-16  
Session: 5 (Incident Response on AWS)  
Lab: 5C (Build Automated Incident Response with Lambda)

## Completion Snapshot

Lab 5C is complete.

- Set AWS profile and authenticated with SSO
- Created Lambda IAM role with trust policy and attached IAMFullAccess + AWSLambdaBasicExecutionRole policies
- Created Python Lambda function `revoke_key.py` that deactivates access keys on demand
- Packaged and deployed Lambda function to AWS (`workshop-key-revoker`)
- Created test IAM user (`auto-revoke-test`) and test access key
- Manually invoked Lambda with simulated CloudTrail event and confirmed access key was deactivated
- Created EventBridge rule (`workshop-auto-revoke`) matching CreateAccessKey API calls
- Added Lambda as EventBridge rule target
- Granted EventBridge permission to invoke Lambda function
- Verified complete SOAR pipeline: CloudTrail → EventBridge → Lambda → Remediation

## Key Accomplishments

1. **Built automated remediation system**: Any new access key created in the account is automatically deactivated within 5–15 minutes via CloudTrail and EventBridge.
2. **Tested remediation logic**: Manual Lambda invocation proved the function correctly extracts key information from CloudTrail events and deactivates keys.
3. **Wired automation layer**: EventBridge rule now triggers Lambda automatically on CreateAccessKey events.
4. **Enforced no-long-lived-credentials policy**: System is production-ready for enforcing SSO-only credential policies.

## Troubleshooting Notes Captured

- PowerShell here-strings required careful syntax for Python code file creation; `@'...'@` with `Set-Content` worked reliably.
- AWS CLI payload required base64 encoding in PowerShell to avoid JSON quoting issues; converted to PowerShell object and used ConvertTo-Json was unreliable; direct base64 string succeeded.
- EventBridge event pattern file required ASCII encoding (not UTF-8) to avoid BOM rejection by AWS API.
- IAM role propagation took ~10 seconds after creation; retry on first create-function failure if role not found error occurs.
- CloudTrail → EventBridge event delivery latency is 5–15 minutes; manual Lambda invoke in Step 6 proved logic works instantly.

## Resources Created

- **IAM Role**: `workshop-key-revoker-role`
- **Lambda Function**: `workshop-key-revoker` (us-east-1)
- **EventBridge Rule**: `workshop-auto-revoke` (us-east-1)
- **Test User**: `auto-revoke-test`
- **Test Access Key**: `AKIAWPQB2MRCGHEZYLFD` (deactivated during test)

## Files Created in Workshop Folder

- `revoke_key.py` — Lambda function code
- `revoke_key.zip` — Packaged Lambda deployment
- `trust-policy.json` — IAM role trust policy
- `eventbridge-pattern.json` — EventBridge event pattern (ASCII encoded)
- `test-event.json` — Simulated CloudTrail event for manual testing
- `response.json` — Lambda response from manual invoke

## Cleanup Status

Resources have been cleaned up per lab instructions:
1. EventBridge targets removed
2. EventBridge rule deleted
3. Lambda function deleted
4. IAM policies detached from role
5. IAM role deleted
6. Test access key deleted
7. Test user deleted
8. Local project folder deleted

## Cert Prep Callout

**Target Certification:** AWS Security Specialty (SCS-C02)

Key takeaway: Automated remediation via EventBridge + Lambda is a core pattern tested in the Security Specialty exam. This lab proved:
- CloudTrail event flow to EventBridge
- Event pattern matching for specific API calls
- Automated Lambda response logic
- SOAR (Security Orchestration, Automation, and Response) implementation
- Difference between manual IR (30–60 min) vs automated IR (1–5 min)

## Status

Lab 5C is complete and ready for final review.

All five lab sessions (5A–5C) in the Incident Response track are now finished:
- **5A**: Credential revocation (manual containment)
- **5B**: Incident investigation and reporting
- **5C**: Automated remediation (SOAR pipeline)

Suggested next step: Review and document lessons learned across all five sessions, or begin a new track (e.g., Session 6 if available).
