# Lab 3B Completion Summary and Troubleshooting

Date: 2026-06-02  
Account: 445606683716  
Profile: 445606683716  
Region: us-east-1

## Completion Snapshot

Lab 3B (IAM Groups, Custom Policies, Explicit Deny, and MFA checkpoint) was completed through all CLI sections:
- Created IAM group for shared permissions.
- Created and attached a custom inline policy with both Allow and explicit Deny statements.
- Added a user to the group and validated inherited permissions.
- Proved policy behavior with live credential switching tests:
  - Read/list allowed.
  - Delete blocked by explicit Deny.

## Resources Created

- S3 bucket: allison-lab3b-groups-445606683716-20260602
- Test object: test-file.txt
- IAM group: workshop-s3-readers
- IAM user: workshop-group-member
- Group inline policy: workshop-s3-readonly-explicit-deny
- Local working folder: ~/Desktop/workshop-lab-3b
- Local policy file: ~/Desktop/workshop-lab-3b/custom-s3-policy.json

## Validation Performed

- Auth validated with STS under admin profile.
- Bucket created and object upload confirmed.
- Group creation confirmed.
- Group policy attachment confirmed.
- Group membership confirmed for workshop-group-member.
- Policy logic verified from IAM output:
  - AllowReadAccess = Allow
  - ExplicitDenyDestructiveActions = Deny
- Restricted user tests:
  - aws s3 ls succeeded and listed test-file.txt.
  - aws s3 rm failed with AccessDenied and explicit deny noted in error.
- Admin identity restored and verified via STS after testing.

## Errors Encountered and Fixes

1. Truncated terminal output during long command blocks
- Symptom: command output returned partial/incomplete lines.
- Root cause: multi-command one-liners sometimes truncated in this session terminal flow.
- Fix: switched to smaller, focused commands for reliable verification.

2. AccessDenied while trying to create/list access keys
- Symptom: iam:ListAccessKeys and iam:CreateAccessKey returned AccessDenied unexpectedly.
- Root cause: shell was still running under restricted user environment variables from prior test context.
- Fix: removed AWS_ACCESS_KEY_ID/AWS_SECRET_ACCESS_KEY/AWS_DEFAULT_REGION, restored AWS_PROFILE=445606683716, verified admin identity with STS, then retried key operations successfully.

## Console and Manual Steps

Step 10 (Groups checkpoint): verified via CLI-equivalent checks and ready to confirm in IAM console UI.

Step 11 (Root MFA): completed manually in AWS Console (user-confirmed). This step is console-only and not verifiable via CLI in this workflow.

## Current Status

- CLI portion of Lab 3B: complete.
- Root MFA setup: complete (user-confirmed).

## Cleanup Status

Lab 3B cleanup is complete and verified.

Deleted resources:
- S3 bucket: allison-lab3b-groups-445606683716-20260602
- IAM user: workshop-group-member
- IAM group: workshop-s3-readers
- Group inline policy: workshop-s3-readonly-explicit-deny
- Local folder: ~/Desktop/workshop-lab-3b

Verification results:
- Bucket lookup: NoSuchBucket
- User lookup: NoSuchEntity
- Group lookup: NoSuchEntity
- Local folder check: False

## Next Suggested Action

Lab 3B is fully complete. Proceed to Lab 3C or run cleanup when ready.
