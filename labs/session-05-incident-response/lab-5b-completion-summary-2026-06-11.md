# Lab 5B Completion Summary and Next Session Marker

Date: 2026-06-11  
Session: 5 (Incident Response on AWS)  
Lab: 5B (Investigate an Incident & Write a Report)

## Completion Snapshot

Lab 5B is complete.

- Refreshed AWS SSO and confirmed admin access
- Created the incident evidence bucket and uploaded sensitive sample data
- Created the attacker IAM user and attached S3 read permissions
- Simulated attacker activity against the S3 bucket
- Investigated CloudTrail event history for attacker activity
- Contained the incident by deactivating the attacker access key
- Eradicated the threat by deleting the access key, policy, and user
- Removed the S3 evidence bucket during cleanup
- Drafted the incident report in the lab folder

## Troubleshooting Notes Captured

- AWS SSO token had expired and required aws sso login before continuing
- CloudTrail lookup-events returned only management events in the quick check
- PowerShell session needed AWS environment variables cleared before switching back to the admin profile
- Cleanup verified that attacker-simulation no longer exists

## Marker for Next Session

Next lab to start: Lab 5C (Automated Remediation)

Suggested first commands next session:
1. aws sso login --profile "AdministratorAccess-445606683716"
2. $env:AWS_PROFILE="AdministratorAccess-445606683716"
3. aws sts get-caller-identity
4. Review lab-5c-automated-remediation.md

## Status

Ready to resume with Lab 5C in the next session.
