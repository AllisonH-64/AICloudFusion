# Lab 5A Completion Summary and Next Session Marker

Date: 2026-06-09  
Session: 5 (Incident Response on AWS)  
Lab: 5A (Respond to a Compromised Access Key)

## Completion Snapshot

Lab 5A is complete.

- Created IAM user for compromise simulation: compromised-user
- Created and tested access key lifecycle
- Performed containment by setting key status to Inactive
- Verified containment behavior (deactivated key authentication failure)
- Performed eradication by deleting compromised access key
- Completed cleanup validation (user no longer exists)

## Troubleshooting Notes Captured

- Correct profile name used: AdministratorAccess-445606683716
- SSO token refresh required during setup (aws sso login)
- Common command issues resolved:
  - Missing required flag: --user-name
  - Typo correction: compromised-user (not comprised-user)
  - PowerShell query quoting for --query values

## Marker for Next Session

Next lab to start: Lab 5B (Investigate an Incident and Write a Report)

Suggested first commands next session:
1. aws sso login --profile "AdministratorAccess-445606683716"
2. $env:AWS_PROFILE="AdministratorAccess-445606683716"
3. aws sts get-caller-identity
4. mkdir ~\Desktop\workshop-lab-5b
5. cd ~\Desktop\workshop-lab-5b

## Status

Ready to resume with Lab 5B in the next session.
