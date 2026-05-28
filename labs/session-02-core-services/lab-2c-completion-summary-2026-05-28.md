# Lab 2C Completion Summary and Troubleshooting

Date: 2026-05-28  
Account: 445606683716  
Profile: AdministratorAccess-445606683716  
Region: us-east-1

## Completion Snapshot

Lab 2C deployment was completed end-to-end:
- Built serverless pipeline resources (S3 + Lambda + IAM + Function URL + event trigger).
- Deployed static website and validated browser-based processing flow.
- Verified transformed output file with word count and uppercase conversion.

Screenshot evidence was captured separately from this document, per request.

## Resources Created

- Input bucket: allison-pipeline-input-445606683716-20260528
- Output bucket: allison-pipeline-output-445606683716-20260528
- Website bucket: allison-pipeline-website-445606683716-20260528
- IAM role: workshop-pipeline-role
- Lambda function: workshop-presign
- Lambda function: workshop-processor
- Function URL (presign): https://2idtr4h4kjsxjgmispsxex2d3y0yiemf.lambda-url.us-east-1.on.aws/
- Website URL: http://allison-pipeline-website-445606683716-20260528.s3-website-us-east-1.amazonaws.com

## Validation Performed

- CLI end-to-end test using presigned URL upload and output fetch succeeded.
- Browser test succeeded and showed processed content.
- Output object confirmed in S3 output bucket:
  - processed-lab2c-success-proof.txt

## Troubleshooting Notes

1. Missing presign URL in website JavaScript
- Symptom: browser error parsing JSON (Unexpected token '<').
- Root cause: `PRESIGN_URL` in `index.html` was empty.
- Fix: set `PRESIGN_URL` to the actual Lambda Function URL and re-upload `index.html` to website bucket.

2. Long multi-line shell blocks with partial/truncated output
- Symptom: incomplete or confusing terminal output during combined setup.
- Root cause: shell context/verbosity and long command chaining.
- Fix: switched to explicit, short idempotent commands for Lambda creation and wiring checks.

3. IAM/Lambda propagation delays
- Symptom: temporary state transitions (`Pending`) or delayed event wiring effects.
- Fix: retried verification commands after resource creation and proceeded after state became ready.

## Cleanup Plan (Executed After Evidence Capture)

Cleanup was requested immediately after evidence capture. The following items were removed:
- S3 event notification on input bucket
- Both Lambda functions
- All three S3 buckets (after emptying)
- IAM role and attached policies
- Local folder: `~/Desktop/workshop-lab-2c`

## Final Verification

- Lambda functions removed:
  - workshop-presign: not found (expected after delete)
  - workshop-processor: not found (expected after delete)
- S3 buckets removed:
  - allison-pipeline-input-445606683716-20260528: not present
  - allison-pipeline-output-445606683716-20260528: not present
  - allison-pipeline-website-445606683716-20260528: not present
- IAM role removed:
  - workshop-pipeline-role: not found (expected after delete)
- Local project folder removed:
  - ~/Desktop/workshop-lab-2c: False (folder no longer exists)

Status: Lab 2C cleanup complete.
