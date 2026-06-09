# Session 4 Completion Summary and Troubleshooting

Date: 2026-06-04  
Account: 445606683716  
Profile: AdministratorAccess-445606683716  
Region: us-east-1

## Completion Snapshot

Session 4 (Threat Detection and AWS Security Services) is complete.

- Lab 4A artifacts are present in the repo (GuardDuty completion evidence files).
- Lab 4B was completed with full CloudTrail setup, suspicious activity simulation, and forensic validation.
- Lab 4C was completed with GuardDuty + EventBridge + SNS automation and live email delivery validation.
- Lab 4C cleanup is complete and verified.

## Lab 4A Status

Evidence files present:
- lab-4a-completion-evidence.html
- lab-4a-completion-evidence.png

Notes:
- Session 4A outputs were already present before this summary.
- GuardDuty state at the end of Session 4 is clean (no active detectors), confirmed during 4C cleanup verification.

## Lab 4B Detailed Summary (CloudTrail Investigation)

### Resources Created

- Trail logs bucket: jallison-445606683716-lab4b-trail-20260604
- Evidence bucket: jallison-445606683716-lab4b-evidence-20260604
- CloudTrail trail: workshop-investigation-trail
- Local folder: C:\Users\jahol\Desktop\workshop-lab-4b

### Core Validation

- CloudTrail trail was created as multi-region and logging started successfully.
- Trail status confirmed IsLogging=true.
- Simulated suspicious user activity with IAM user callie-suspicious-user.
- Verified immutable audit evidence using lookup-events after key and user deletion.

### Forensic Evidence Captured

- CreateUser event observed for actor Jallison.
- DeleteAccessKey event observed:
  - Time: 2026-06-04T08:44:07-04:00
  - Actor: Jallison
  - Action: DeleteAccessKey
- DeleteUser event observed:
  - Time: 2026-06-04T08:44:13-04:00
  - Actor: Jallison
  - Action: DeleteUser

Conclusion:
- Even after deleting both key and user, CloudTrail retained auditable records, proving immutability and forensic traceability.

### Troubleshooting During Lab 4B

1. PowerShell command concatenation issue
- Symptom: Set-Location positional parameter error when cd and aws commands were pasted together.
- Fix: split into separate commands and reran setup.

2. IAM CLI argument formatting errors
- Symptom: required --user-name and Unknown options errors from malformed username argument.
- Fix: used explicit variable and standard format: --user-name callie-suspicious-user.

3. CloudTrail lookup command truncation
- Symptom: partial command text created malformed lookup query.
- Fix: reran full query lines exactly as written.

## Lab 4C Detailed Summary (Automated Security Alerts)

### Resources Created

- GuardDuty detector: d4cf496e09950e847312c0778bbd216e
- SNS topic: arn:aws:sns:us-east-1:445606683716:workshop-security-alerts
- EventBridge rule: guardduty-medium-plus-alerts
- EventBridge target: SNS topic target Id=1
- Local folder: C:\Users\jahol\Desktop\workshop-lab-4c

### Core Validation

- GuardDuty was enabled in us-east-1.
- SNS topic and email subscription were created and confirmed.
- EventBridge rule and target were configured and verified.
- GuardDuty sample findings were generated and confirmed present.
- Email delivery was validated:
  - Direct SNS test email received.
  - GuardDuty alert email received.

### Findings Confirmed

Sample finding observed:
- Id: fd1a42f9b71347b9b7227589f383acf3
- Type: UnauthorizedAccess:IAMUser/MaliciousIPCaller.Custom
- Severity: 5.0

### Troubleshooting During Lab 4C

1. EventBridge target parameter formatting in PowerShell
- Symptom: put-targets JSON parse/EOF errors.
- Fix: switched to shorthand target syntax Id=1,Arn=<topic-arn>, which succeeded.

2. Frequent AWS SSO token expiry during testing
- Symptom: session expired errors during test commands.
- Fix: reran aws sso login --profile AdministratorAccess-445606683716 and continued.

3. Sample finding command appeared empty
- Symptom: create-sample-findings returned no visible payload.
- Root cause: this command can return minimal/no output even when successful.
- Fix: validated success using list-findings and get-findings.

## Session 4 Cleanup Status

### Lab 4B Cleanup

Verified previously:
- CloudTrail trail deleted
- Trail and evidence buckets deleted
- Local folder removed

### Lab 4C Cleanup (Executed and Verified in this session)

Deleted:
- EventBridge targets from guardduty-medium-plus-alerts
- EventBridge rule guardduty-medium-plus-alerts
- SNS subscription(s) for workshop-security-alerts
- SNS topic arn:aws:sns:us-east-1:445606683716:workshop-security-alerts
- GuardDuty detector d4cf496e09950e847312c0778bbd216e
- Local folder C:\Users\jahol\Desktop\workshop-lab-4c

Verification:
- describe-rule returned not found
- get-topic-attributes returned not found
- list-detectors returned []
- local folder check returned not found

## Lab 5A Start Checkpoint

Readiness status: READY

- CLI authentication is known-good with your SSO profile workflow.
- Session 4 resources are cleaned up and do not block Lab 5A.
- Next start commands for Lab 5A:
  1. aws sso login --profile AdministratorAccess-445606683716
  2. $env:AWS_PROFILE="AdministratorAccess-445606683716"
  3. aws sts get-caller-identity
  4. mkdir ~\Desktop\workshop-lab-5a
  5. cd ~\Desktop\workshop-lab-5a

## Final Notes

Session 4 objectives were achieved end-to-end:
- Threat detection enablement and findings review
- CloudTrail forensic investigation and immutable audit proof
- Automated alerting pipeline with successful email delivery
- Clean resource teardown after validation
