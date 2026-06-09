# Lab 3C Completion Summary and Troubleshooting

Date: 2026-06-02  
Account: 445606683716  
Profile: 445606683716  
Region: us-east-1

## Completion Snapshot

Lab 3C (roles + CloudTrail audit logging + separation of duties) was completed through all CLI actions:
- Created and configured CloudTrail with multi-region logging to S3.
- Created developer and auditor IAM roles with separate inline policies.
- Assumed developer role and validated allowed vs denied actions.
- Assumed auditor role and validated read-only investigation access with denied modifications.
- Restored admin credentials after both role tests.

## Resources Created

- S3 bucket (CloudTrail logs): allison-lab3c-cloudtrail-logs-445606683716-20260602
- CloudTrail trail: workshop-audit-trail
- Trust policy file: ~/Desktop/workshop-lab-3c/role-trust-policy.json
- Developer policy file: ~/Desktop/workshop-lab-3c/developer-policy.json
- Auditor policy file: ~/Desktop/workshop-lab-3c/auditor-policy.json
- IAM role: workshop-developer-role
- IAM role: workshop-auditor-role
- Local working folder: ~/Desktop/workshop-lab-3c

## Validation Performed

### CloudTrail setup
- Trail created with `--is-multi-region-trail`.
- Logging started successfully (`start-logging` completed with no errors).

### Developer role tests
- Caller identity showed assumed role: `workshop-developer-role/dev-session`.
- S3 write test succeeded:
  - Uploaded `dev-test.txt` to `s3://allison-lab3c-cloudtrail-logs-445606683716-20260602/dev-test.txt`.
- Privilege escalation test failed as expected:
  - `aws iam create-user --user-name hacker-attempt`
  - Result: AccessDenied with explicit deny in identity-based policy.

### Auditor role tests
- Caller identity showed assumed role: `workshop-auditor-role/auditor-session`.
- CloudTrail lookup succeeded:
  - `aws cloudtrail lookup-events --max-results 5 ...`
  - Returned recent events including `CreateUser`, `CreateRole`, and `PutRolePolicy`.
- Modification test failed as expected:
  - `aws iam create-user --user-name auditor-hack`
  - Result: AccessDenied with explicit deny in identity-based policy.

### Credential hygiene
- Cleared temporary role credentials and restored admin profile between role tests and at end.
- Final identity verified as AdministratorAccess assumed role.

## Errors Encountered and Fixes

1. Role creation failed with `MalformedPolicyDocument` / invalid principal
- Symptom: `create-role` returned invalid principal `arn:aws:iam::`.
- Root cause: trust policy file was generated with a missing account ID in Principal ARN.
- Fix: rewrote `role-trust-policy.json` with explicit account root ARN:
  - `arn:aws:iam::445606683716:root`
  - Re-ran role creation and policy attachment successfully.

2. Terminal output truncation on long multi-command blocks
- Symptom: partial outputs made step confirmation ambiguous.
- Root cause: long command chains in this session terminal flow.
- Fix: switched to short, focused verification commands for each step.

## Console Evidence Status

- Step 14 (CloudTrail Event History console view): not captured in this file.
- CLI evidence confirms CloudTrail events are present and queryable.
- If required for grading, capture screenshots from CloudTrail Event history page after propagation delay.

## Current Status

- Lab 3C CLI workflow: complete.
- Account currently back on admin profile.
- Cleanup: complete and verified.

## Cleanup Status

Lab 3C cleanup is complete.

Deleted resources:
- CloudTrail trail: workshop-audit-trail
- IAM role: workshop-developer-role
- IAM role: workshop-auditor-role
- S3 bucket: allison-lab3c-cloudtrail-logs-445606683716-20260602
- Local folder: ~/Desktop/workshop-lab-3c

Verification results:
- `describe-trails` returned empty list for workshop-audit-trail
- `get-role` returned NoSuchEntity for both roles
- `aws s3 ls` returned NoSuchBucket for the CloudTrail bucket
- Local folder check returned False

## Next Suggested Action

Lab 3C is fully complete, including cleanup. Proceed to Session 4 when ready.
