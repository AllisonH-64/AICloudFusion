# Lab Progress and Troubleshooting Summary (Through Session 2)

Date: 2026-05-28  
Account ID: 445606683716  
Primary CLI profile: AdministratorAccess-445606683716  
Region baseline: us-east-1

## Executive Summary

All work through Session 2 has been completed and verified:
- Lab 1A (AWS CLI + Identity Center SSO) completed and validated.
- Lab 2A (EC2 launch + Session Manager exploration) completed.
- Lab 2B (EC2 <-> S3 via IAM role) completed.
- Lab 2A and 2B resources were cleaned up and verified deleted/terminated.

Current environment is ready to proceed to Session 3 (Cloud Security labs).

## What Was Completed

### Lab 1A: AWS CLI and SSO Setup
- Confirmed AWS CLI v2 installed and working.
- Configured and validated SSO profile.
- Verified authenticated identity with STS.

### Lab 2A: Launch and Connect to EC2
- Created IAM role: workshop-ec2-ssm-role.
- Created instance profile: workshop-ec2-ssm-profile.
- Launched EC2 instance: i-0aa16549e3a46108e (Name: workshop-session2).
- Confirmed Session Manager readiness and connected.
- Validated OS, memory, disk, and host identity from inside EC2.
- Cleanup completed:
  - Instance terminated.
  - IAM role and instance profile deleted.
  - Local lab folder removed.

### Lab 2B: EC2 to S3 with IAM Role
- Created S3 bucket: workshop-lab-2b-445606683716.
- Created IAM role: workshop-ec2-s3-role.
- Created instance profile: workshop-ec2-s3-profile.
- Launched EC2 instance: i-00c44b29f0977e5b9 (Name: workshop-s3-access).
- From EC2, uploaded hello-from-ec2.txt to S3.
- From local machine, uploaded local-file.txt to S3.
- From EC2, downloaded local-file.txt and verified file contents.
- Cleanup completed:
  - Instance terminated.
  - Bucket emptied and deleted.
  - IAM role and instance profile deleted.
  - Local lab folder removed.

## Evidence and Verification

### Identity verification
Command used:
- aws sts get-caller-identity

Result:
- UserId: AROAWPQB2MRCKCJSWSFLX:Jallison
- Account: 445606683716
- Arn includes: AWSReservedSSO_AdministratorAccess

### EC2 cleanup verification
Command used:
- aws ec2 describe-instances --instance-ids i-0aa16549e3a46108e i-00c44b29f0977e5b9 --region us-east-1 --query "Reservations[].Instances[].{Id:InstanceId,State:State.Name,Name:Tags[?Key=='Name']|[0].Value}" --output table

Result:
- i-0aa16549e3a46108e (workshop-session2): terminated
- i-00c44b29f0977e5b9 (workshop-s3-access): terminated

### S3 verification
Command used:
- aws s3 ls

Result:
- No Lab 2B bucket remains.
- Existing bucket still present: allison-cloudfusion-site-445606683716-20260526 (pre-existing, unrelated to Lab 2B cleanup).

## Troubleshooting Log and Resolutions

### 1) Identity Center login confusion (root vs user)
Issue:
- Sign-in initially worked only with root account.

Root cause:
- Identity Center user flow was not fully established (portal/user assignment/password path).

Resolution:
- Completed Identity Center user/password/invitation and proper access portal sign-in flow.
- Re-ran CLI SSO auth and validated with STS caller identity.

### 2) Session Manager plugin missing on Windows
Issue:
- aws ssm start-session failed with SessionManagerPlugin not found.

Root cause:
- AWS Session Manager plugin not installed locally.

Resolution:
- Installed plugin with:
  - winget install -e --id Amazon.SessionManagerPlugin --accept-package-agreements --accept-source-agreements
- Verified plugin executable and version.
- Added plugin path in session when needed and reconnected successfully.

### 3) IAM propagation delay for instance profile
Issue:
- Initial EC2 launch in Lab 2B failed with invalid iamInstanceProfile.name.

Root cause:
- Eventual consistency delay after creating profile/role attachment.

Resolution:
- Verified role/profile linkage using IAM get/list commands.
- Re-ran launch after propagation; launch succeeded.

### 4) Local folder deletion in-use error
Issue:
- Remove-Item failed for workshop-lab-2b because folder was in use.

Root cause:
- Shell current directory still inside the folder.

Resolution:
- Changed location to home directory and retried deletion; succeeded.

## Recommended Baseline Before Starting Lab 3

Run these commands once at the beginning of Session 3:

```powershell
$env:AWS_PROFILE='AdministratorAccess-445606683716'
aws sts get-caller-identity
```

If Session Manager is needed again in later labs, ensure plugin path is available in the shell:

```powershell
$env:Path += ';C:\Program Files\Amazon\SessionManagerPlugin\bin'
```

## Readiness for Session 3 (Cloud Security)

Status: Ready.
- Authentication: healthy
- Session 2 resources: cleaned
- Known blockers: none
- Residual note: one pre-existing S3 bucket remains (not a blocker)

## Suggested Session 3 Start Order

1. Lab 3A: IAM least privilege.
2. Lab 3B: groups, custom policy, MFA.
3. Lab 3C: roles and CloudTrail audit.

---
Prepared as a handoff record for transition from Session 2 to Session 3.
