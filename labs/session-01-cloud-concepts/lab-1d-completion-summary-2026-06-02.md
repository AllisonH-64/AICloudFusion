# Lab 1D Completion Summary and Troubleshooting

Date: 2026-06-02  
Account: 445606683716  
Profile: 445606683716  
Region: us-east-1

## Completion Snapshot

Lab 1D side quest (CloudFront HTTPS for S3 static website) was completed end-to-end for deployment and validation:
- Created Origin Access Control (OAC) for private S3 origin access.
- Created CloudFront distribution with HTTPS and redirect-to-https behavior.
- Verified CloudFront endpoint in browser and confirmed expected routing behavior.
- Confirmed architecture intent: S3 origin behind CloudFront with OAC.

## Resources Created

- OAC ID: E13X5A31FOO5I1
- CloudFront Distribution ID: E2TWDLJK9BT7P5
- CloudFront Distribution ID (additional): EX0I2XCAWCGSP
- CloudFront Domain: dogrtcytqfaoj.cloudfront.net
- Local working folder: ~/Desktop/workshop-lab-1d

## Validation Performed

- Distribution deployment status reached Deployed before validation.
- HTTPS test completed on CloudFront domain.
- HTTP-to-HTTPS redirect test completed.
- Missing-page test completed on /this-page-does-not-exist.
- S3 website endpoint test executed to confirm direct path behavior after lock-down changes.

## Errors Encountered and Fixes

1. Profile naming mismatch across notes and commands
- Symptom: profile name confusion between AdministratorAccess-445606683716 and 445606683716.
- Root cause: mixed profile naming references from earlier labs.
- Fix: standardized session commands to --profile 445606683716 and verified identity with STS.

2. OAC JSON parsing failures from file input
- Symptom: create-origin-access-control failed with parse errors such as Expected '=' received EOF and malformed character issues.
- Root cause: Windows file formatting/BOM and shell formatting of file URI arguments.
- Fix: rewrote JSON files as ASCII (no BOM) and used clean file:// references.

3. PowerShell placeholder and command entry issues
- Symptom: commands failed when placeholders like <YOUR_DIST_ID> were pasted literally, and URLs were entered as shell commands.
- Root cause: placeholder text not replaced and raw URL invocation in PowerShell.
- Fix: replaced placeholders with real IDs, used actual variable values, and opened URLs with Start-Process.

4. Minor command typo during checks
- Symptom: Test-Path check failed due to typo.
- Root cause: est-Path typo.
- Fix: corrected to Test-Path and re-ran checks.

5. Distribution lookup query returned no rows early in troubleshooting
- Symptom: list-distributions with strict origin filter showed no matches initially.
- Root cause: over-constrained query while distribution state was changing.
- Fix: used broader list-distributions query and then targeted exact distribution ID for verification.

## Cleanup Status

Cleanup is complete and verified.

Deletion/verification results:
- Distribution E2TWDLJK9BT7P5: not found (deleted)
- Distribution EX0I2XCAWCGSP: not found (deleted)
- OAC E13X5A31FOO5I1: not found (deleted)
- Local folder ~/Desktop/workshop-lab-1d: not found (deleted)

Cleanup troubleshooting note:
- OAC deletion initially failed with OriginAccessControlInUse.
- Root cause: a second distribution (EX0I2XCAWCGSP) still referenced the same OAC.
- Fix: disabled and deleted the second distribution, waited for deploy completion, then deleted the OAC successfully.

## Final Notes

This session successfully implemented the core production pattern for static-site HTTPS on AWS:
- CloudFront as secure public entry point
- OAC-secured private S3 origin
- Protocol redirect and custom error handling behavior validated
