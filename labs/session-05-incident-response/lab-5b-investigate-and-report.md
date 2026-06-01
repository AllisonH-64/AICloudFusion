# Lab 5B: Investigate an Incident & Write a Report

**Session:** 5 — Incident Response on AWS  
**Track:** Cloud Security & IR  
**Difficulty:** Intermediate  
**Estimated Time:** 30–35 minutes  
**Target Cert:** AWS Security Specialty

---

## Overview

In this lab, you will:

1. **Simulate a security incident** — create evidence of unauthorized access to an S3 bucket
2. **Investigate with CloudTrail** — use `lookup-events` to reconstruct what the attacker did
3. **Contain and eradicate** — revoke the attacker's credentials and remove the user
4. **Write an incident report** — document the incident using a professional template

By the end of this lab, you will understand how to investigate a cloud security incident using CloudTrail, reconstruct a forensic timeline, and produce documentation that meets industry standards. This is what incident responders do every day.

---

## Prerequisites

- ✅ Completed **Lab 1A** (AWS CLI installed and configured)
- ✅ AWS CLI authenticated — run `aws sts get-caller-identity` and confirm it returns your account info
- ✅ A text editor to create files (VS Code, Notepad, or any editor)

---

## Cost Notice

| Service | What It Is | Cost |
|---------|-----------|------|
| IAM | Identity and Access Management | Always Free |
| Amazon S3 | Cloud storage for files | Free within 5 GB / 2,000 PUT / 20,000 GET per month (12-month free tier) |
| CloudTrail | Event history (last 90 days) | Always Free (no trail creation needed) |

**Estimated cost for this lab: $0.00**

> **💡 Note:** CloudTrail event history (last 90 days of management events) is always available in every AWS account without creating a trail. You do NOT need to set up a trail for this lab — you can use `lookup-events` directly.

---

## Concepts

Before we start, here is what each concept means:

**NIST Incident Response Lifecycle** is the industry-standard framework for handling security incidents. It has six phases:
1. **Preparation** — Have tools, processes, and training ready before an incident occurs
2. **Detection & Analysis** — Identify that an incident is happening and understand its scope
3. **Containment** — Stop the incident from spreading (revoke credentials, isolate systems)
4. **Eradication** — Remove the threat completely (delete malicious users, keys, resources)
5. **Recovery** — Return to normal operations safely
6. **Lessons Learned** — Document what happened and improve defenses

**Forensic Timeline** is a chronological reconstruction of events during an incident. It answers: What happened? When? In what order? Who was responsible? CloudTrail provides the raw data to build this timeline.

**CloudTrail Event History** records every API call made in your AWS account for the last 90 days. It is always on — you do not need to enable it. Each event includes: who made the call, what they did, when they did it, from what IP address, and whether it succeeded or failed.

**Incident Report** is a formal document that records everything about a security incident: what happened, how it was detected, what actions were taken, and what should be improved. It serves as a legal record, a learning tool, and evidence for compliance audits.

**Chain of Custody** refers to maintaining the integrity of evidence. In cloud IR, this means preserving CloudTrail logs, not modifying affected resources before investigation, and documenting every action you take during response.

---

## ⚠️ Reminder: How to Read Commands in This Lab

Commands are inside gray code boxes. **📋 Copy and paste** them into your terminal.

**Placeholders** look like `<THIS>` — replace them (including the `<` and `>`) with your actual values.

Here are the placeholders you will use in this lab:

| Placeholder | What to Replace It With | Example |
|-------------|------------------------|---------|
| `<YOUR_PROFILE_NAME>` | Your AWS CLI profile name from Lab 1A | `AdministratorAccess-123456789012` |
| `<STUDENT>` | Your first name or initials (lowercase, no spaces) | `jdoe` |
| `<KEY_ID>` | The attacker's Access Key ID (Step 5) | `AKIAIOSFODNN7EXAMPLE` |
| `<SECRET_KEY>` | The attacker's Secret Access Key (Step 5) | `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY` |

---

## Lab Steps

### Step 1: Set Your AWS Profile

**Windows (PowerShell):**

📋 Copy and paste this command, **replacing `<YOUR_PROFILE_NAME>`**:

```powershell
$env:AWS_PROFILE="<YOUR_PROFILE_NAME>"
```

**macOS / Linux:**

```bash
export AWS_PROFILE="<YOUR_PROFILE_NAME>"
```

**Verify it works.** 📋 Copy and paste:

```
aws sts get-caller-identity
```

**✅ You should see** your account ID and role. If you get an error about an expired token, run `aws sso login --profile <YOUR_PROFILE_NAME>` first.

---

### Step 2: Create Your Project Folder

**Step 2a: Create the folder**

**Windows (PowerShell):**

📋 Copy and paste:

```powershell
mkdir ~\Desktop\workshop-lab-5b
cd ~\Desktop\workshop-lab-5b
```

**macOS / Linux:**

📋 Copy and paste:

```bash
mkdir ~/Desktop/workshop-lab-5b
cd ~/Desktop/workshop-lab-5b
```

> **What does this do?** Creates a new folder on your Desktop called `workshop-lab-5b` and moves your terminal into that folder.

**Step 2b: Verify you're in the right folder**

📋 Copy and paste:

```
pwd
```

**✅ You should see** a path ending in `workshop-lab-5b` (e.g., `C:\Users\YourName\Desktop\workshop-lab-5b` on Windows or `/Users/YourName/Desktop/workshop-lab-5b` on Mac).

> **💡 From now on, save ALL files you create in this lab to this folder.** When the lab says "save the file," save it here.

---

### Step 3: Simulate the Incident — Create the Evidence

You are going to simulate a security incident by creating resources and then accessing them with an "attacker" user. This generates real CloudTrail events that you will investigate later.

**Step 3a: Create an S3 bucket with sensitive data**

📋 Copy and paste, **replacing `<STUDENT>`** with your name/initials (lowercase, no spaces):

```
aws s3 mb s3://<STUDENT>-incident-evidence --region us-east-1
```

**✅ You should see:** `make_bucket: <STUDENT>-incident-evidence`

**Step 3b: Upload a "sensitive" file**

**Windows (PowerShell):**

📋 Copy and paste, **replacing `<STUDENT>`**:

```powershell
"CONFIDENTIAL: Employee salary data - Q4 2024 report. SSN: XXX-XX-XXXX" | Out-File sensitive-data.txt
aws s3 cp sensitive-data.txt s3://<STUDENT>-incident-evidence/sensitive-data.txt
```

**macOS / Linux:**

📋 Copy and paste, **replacing `<STUDENT>`**:

```bash
echo "CONFIDENTIAL: Employee salary data - Q4 2024 report. SSN: XXX-XX-XXXX" > sensitive-data.txt
aws s3 cp sensitive-data.txt s3://<STUDENT>-incident-evidence/sensitive-data.txt
```

**✅ You should see:** `upload: ./sensitive-data.txt to s3://<STUDENT>-incident-evidence/sensitive-data.txt`

**Step 3c: Create the attacker user**

📋 Copy and paste:

```
aws iam create-user --user-name attacker-simulation
```

**✅ You should see** JSON output with `"UserName": "attacker-simulation"`.

**Step 3d: Give the attacker S3 read access**

Open your text editor and create a **new, empty file**.

📋 Copy and paste this entire block into the file:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowS3Read",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetObject",
                "s3:ListAllMyBuckets"
            ],
            "Resource": "*"
        }
    ]
}
```

Save the file as `attacker-policy.json` in your `workshop-lab-5b` folder.

Now attach the policy:

📋 Copy and paste:

```
aws iam put-user-policy --user-name attacker-simulation --policy-name S3ReadAccess --policy-document file://attacker-policy.json
```

**✅ No output means success.**

**Step 3e: Create access keys for the attacker**

📋 Copy and paste:

```
aws iam create-access-key --user-name attacker-simulation
```

**✅ You should see** JSON output with `AccessKeyId` and `SecretAccessKey`.

> **📝 Write down BOTH values immediately:**
>
> - **AccessKeyId:** ______________________________
> - **SecretAccessKey:** ______________________________

---

### Step 4: Simulate the Attack

Now switch to the attacker's credentials and perform unauthorized actions. This generates the CloudTrail events you will investigate.

**Step 4a: Set the attacker's credentials**

**Windows (PowerShell):**

📋 Copy and paste, **replacing the placeholders** with the values from Step 3e:

```powershell
$env:AWS_ACCESS_KEY_ID="<KEY_ID>"
$env:AWS_SECRET_ACCESS_KEY="<SECRET_KEY>"
$env:AWS_DEFAULT_REGION="us-east-1"
Remove-Item Env:\AWS_PROFILE
```

**macOS / Linux:**

📋 Copy and paste, **replacing the placeholders** with the values from Step 3e:

```bash
export AWS_ACCESS_KEY_ID="<KEY_ID>"
export AWS_SECRET_ACCESS_KEY="<SECRET_KEY>"
export AWS_DEFAULT_REGION="us-east-1"
unset AWS_PROFILE
```

**Step 4b: Verify you are now the attacker**

📋 Copy and paste:

```
aws sts get-caller-identity
```

**✅ You should see** output showing `attacker-simulation` — NOT your admin role.

**Step 4c: The attacker lists all S3 buckets (reconnaissance)**

📋 Copy and paste:

```
aws s3 ls
```

**✅ You should see** a list of buckets including your `<STUDENT>-incident-evidence` bucket.

**Step 4d: The attacker lists the contents of the sensitive bucket**

📋 Copy and paste, **replacing `<STUDENT>`**:

```
aws s3 ls s3://<STUDENT>-incident-evidence/
```

**✅ You should see** `sensitive-data.txt` listed.

**Step 4e: The attacker downloads the sensitive file**

📋 Copy and paste, **replacing `<STUDENT>`**:

```
aws s3 cp s3://<STUDENT>-incident-evidence/sensitive-data.txt stolen-data.txt
```

**✅ You should see:** `download: s3://<STUDENT>-incident-evidence/sensitive-data.txt to ./stolen-data.txt`

> **The attacker now has your confidential data.** In a real scenario, this data could be exfiltrated to an external server within seconds.

**Step 4f: Switch back to admin credentials**

**Windows (PowerShell):**

📋 Copy and paste, **replacing `<YOUR_PROFILE_NAME>`**:

```powershell
Remove-Item Env:\AWS_ACCESS_KEY_ID
Remove-Item Env:\AWS_SECRET_ACCESS_KEY
Remove-Item Env:\AWS_DEFAULT_REGION
$env:AWS_PROFILE="<YOUR_PROFILE_NAME>"
```

**macOS / Linux:**

📋 Copy and paste, **replacing `<YOUR_PROFILE_NAME>`**:

```bash
unset AWS_ACCESS_KEY_ID
unset AWS_SECRET_ACCESS_KEY
unset AWS_DEFAULT_REGION
export AWS_PROFILE="<YOUR_PROFILE_NAME>"
```

**Verify you are back to admin:**

📋 Copy and paste:

```
aws sts get-caller-identity
```

**✅ You should see** your admin role.

---

### Step 5: Wait for CloudTrail to Record Events

> **⏱️ CloudTrail takes 5–15 minutes to record events.** This is normal — events are not instant.
>
> **While you wait:** Read the Concepts section above if you have not already. Pay attention to the NIST IR Lifecycle — you will use it in your incident report.
>
> **After 5–10 minutes**, continue to Step 6.

---

### Step 6: Investigate with CloudTrail

Now you are the incident responder. Use CloudTrail to reconstruct what the attacker did.

📋 Copy and paste:

```
aws cloudtrail lookup-events --lookup-attributes AttributeKey=Username,AttributeValue=attacker-simulation --query "Events[].{Time:EventTime,Name:EventName,Source:EventSource}" --output table
```

**✅ You should see** a table showing the attacker's actions:

```
--------------------------------------------------------------
|                       LookupEvents                         |
+---------------------------+------------------+-------------+
|           Time            |      Name        |   Source    |
+---------------------------+------------------+-------------+
|  2024-XX-XXTXX:XX:XXZ    |  GetObject       |  s3...      |
|  2024-XX-XXTXX:XX:XXZ    |  ListBuckets     |  s3...      |
|  2024-XX-XXTXX:XX:XXZ    |  ListObjects     |  s3...      |
+---------------------------+------------------+-------------+
```

> **💡 If you see an empty table or very few events:** CloudTrail may need more time. Wait another 5 minutes and try again. S3 data events (GetObject, ListObjects) require a trail with data event logging enabled. You will definitely see the IAM events (CreateAccessKey, CreateUser). The S3 management events (like CreateBucket) should also appear.

> **What does this tell you?** You can now see exactly what the attacker did, in chronological order. This is your forensic timeline.

---

### Step 7: Contain — Deactivate the Attacker's Key

📋 Copy and paste, **replacing `<KEY_ID>`** with the attacker's AccessKeyId from Step 3e:

```
aws iam update-access-key --user-name attacker-simulation --access-key-id <KEY_ID> --status Inactive
```

**✅ No output means success.** The attacker is now locked out.

---

### Step 8: Eradicate — Remove the Attacker

**Step 8a: Delete the access key**

📋 Copy and paste, **replacing `<KEY_ID>`**:

```
aws iam delete-access-key --user-name attacker-simulation --access-key-id <KEY_ID>
```

**Step 8b: Delete the user policy**

📋 Copy and paste:

```
aws iam delete-user-policy --user-name attacker-simulation --policy-name S3ReadAccess
```

**Step 8c: Delete the attacker user**

📋 Copy and paste:

```
aws iam delete-user --user-name attacker-simulation
```

**✅ No output for each command means success.** The threat has been eradicated.

---

### Step 9: Document — Write the Incident Report

Now write a formal incident report. This is a critical skill — every security incident must be documented.

**Step 9a:** Open your text editor and create a **new, empty file**.

**Step 9b:** 📋 Copy and paste this entire template into the file:

```markdown
# Incident Report

## Incident Summary

| Field | Value |
|-------|-------|
| **Incident ID** | IR-2024-001 |
| **Date Detected** | [TODAY'S DATE] |
| **Severity** | High |
| **Status** | Resolved |
| **Reported By** | [YOUR NAME] |

## Detection

**How was the incident discovered?**

[DESCRIBE HOW THE INCIDENT WAS DETECTED — e.g., "Automated scanner detected IAM access keys committed to a public GitHub repository" or "GuardDuty alert for unusual API activity"]

## Timeline of Events

| Time (UTC) | Event | Details |
|------------|-------|---------|
| [TIME FROM CLOUDTRAIL] | Attacker credentials created | Access key created for attacker-simulation |
| [TIME FROM CLOUDTRAIL] | Reconnaissance | Attacker listed all S3 buckets in the account |
| [TIME FROM CLOUDTRAIL] | Data access | Attacker listed contents of sensitive bucket |
| [TIME FROM CLOUDTRAIL] | Data exfiltration | Attacker downloaded sensitive-data.txt |
| [TIME YOU DEACTIVATED] | Containment | Access key deactivated |
| [TIME YOU DELETED] | Eradication | Attacker user and credentials deleted |

## Affected Resources

| Resource | Type | Impact |
|----------|------|--------|
| [YOUR BUCKET NAME] | S3 Bucket | Unauthorized read access |
| sensitive-data.txt | S3 Object | Downloaded by attacker (data exfiltration) |
| attacker-simulation | IAM User | Unauthorized user with S3 read access |

## Containment Actions

1. Deactivated the compromised access key (immediate — stopped further access)
2. Verified the key could no longer authenticate

## Eradication Actions

1. Deleted the compromised access key permanently
2. Removed the attacker's IAM policy
3. Deleted the attacker IAM user

## Root Cause

[DESCRIBE THE ROOT CAUSE — e.g., "Access keys were committed to a public repository due to lack of .gitignore rules and pre-commit hooks for secret scanning"]

## Lessons Learned & Recommendations

1. [RECOMMENDATION 1 — e.g., "Implement automated secret scanning in CI/CD pipeline"]
2. [RECOMMENDATION 2 — e.g., "Enable GuardDuty for continuous threat detection"]
3. [RECOMMENDATION 3 — e.g., "Use IAM roles and SSO instead of long-lived access keys"]
4. [RECOMMENDATION 4 — e.g., "Implement S3 bucket policies restricting access to known principals"]
```

**Step 9c:** Save the file as `incident-report.md` in your `workshop-lab-5b` folder.

**Step 9d:** Fill in the template with the information from your investigation:
- Replace `[TODAY'S DATE]` with today's date
- Replace `[YOUR NAME]` with your name
- Replace `[TIME FROM CLOUDTRAIL]` with the timestamps from Step 6
- Fill in the Root Cause and Lessons Learned sections with your own analysis

> **💡 Take 5 minutes to fill this in thoughtfully.** In a real job, incident reports are reviewed by management, legal, and compliance teams. They need to be clear, accurate, and actionable.

---

### Step 10: Console Checkpoint

Let's verify the investigation in the AWS Console:

1. Go to [https://console.aws.amazon.com/](https://console.aws.amazon.com/)
2. Search for **CloudTrail** in the top search bar and click it
3. Click **Event history** in the left sidebar
4. In the filter dropdown, select **User name**
5. Type `attacker-simulation` and press Enter
6. You should see the events from the attacker's activity (CreateAccessKey, and any management events that were recorded)

**✅ Checkpoint:** You can see the attacker's activity in CloudTrail Event history. This is exactly how a security analyst investigates incidents in production — filtering by username, time range, or event type to reconstruct what happened.

---

## What You Just Did

You executed a complete incident response following the NIST framework:

1. **Preparation** — Set up the environment and tools
2. **Detection** — Identified the unauthorized access
3. **Containment** — Deactivated the attacker's credentials
4. **Eradication** — Removed the attacker completely
5. **Recovery** — Verified the threat is eliminated
6. **Lessons Learned** — Documented the incident and recommendations

You also practiced two critical IR skills:
- **Forensic investigation** — Using CloudTrail to reconstruct a timeline of attacker activity
- **Incident documentation** — Writing a professional report that could be used in a real organization

---

## Cert Prep Callout

**Target Certification:** AWS Security Specialty (SCS-C02)

The Security Specialty exam tests incident investigation heavily. You need to understand:
- How to use CloudTrail `lookup-events` to investigate incidents
- The difference between management events (always logged) and data events (require a trail)
- How to filter events by username, event name, resource type, and time range
- The NIST IR lifecycle and how it applies to AWS

**Sample question type:** "After detecting unauthorized access to an S3 bucket, a security engineer needs to determine what data was accessed. Which AWS service provides the most detailed record of API calls made to the bucket?"  
**Answer:** AWS CloudTrail (with data event logging enabled for S3)

---

## Troubleshooting

| Issue | What It Means | How to Fix It |
|-------|--------------|---------------|
| CloudTrail `lookup-events` returns empty results | Events have not been recorded yet (5–15 min delay) | Wait 5 more minutes and try again. CloudTrail is not real-time. |
| `get-caller-identity` still shows attacker after switching back | Env vars were not cleared | Run the switch-back commands in Step 4f again. Close and reopen your terminal if needed. |
| `An error occurred (EntityAlreadyExists)` | The user or bucket already exists | Delete the existing resource first and try again |
| S3 data events (GetObject) not showing in CloudTrail | Data events require a trail with S3 data event logging | This is expected — you will see management events (CreateUser, CreateAccessKey, CreateBucket). S3 data events need additional configuration. |
| Cannot delete attacker user | User still has policies or access keys | Delete in order: access key → user policy → user (Steps 8a, 8b, 8c) |

---

## Cleanup

**⚠️ Important:** Always clean up resources after completing a lab. Follow these steps in order.

### Step 1: Empty and Delete the S3 Bucket

📋 Copy and paste, **replacing `<STUDENT>`**:

```
aws s3 rm s3://<STUDENT>-incident-evidence --recursive
```

```
aws s3 rb s3://<STUDENT>-incident-evidence
```

**✅ You should see** `remove_bucket: <STUDENT>-incident-evidence`.

### Step 2: Verify the Attacker User Is Deleted

If you already completed Step 8, the attacker user should be gone. Verify:

📋 Copy and paste:

```
aws iam get-user --user-name attacker-simulation 2>&1
```

**✅ You should see** `NoSuchEntity` error — confirming the user is deleted.

> **If the user still exists**, run these commands in order:
> ```
> aws iam list-access-keys --user-name attacker-simulation
> aws iam delete-access-key --user-name attacker-simulation --access-key-id <KEY_ID>
> aws iam delete-user-policy --user-name attacker-simulation --policy-name S3ReadAccess
> aws iam delete-user --user-name attacker-simulation
> ```

### Step 3: Delete Local Files

Remove the project folder:

**Windows (PowerShell):**

```powershell
cd ~\Desktop
Remove-Item -Recurse -Force ~\Desktop\workshop-lab-5b
```

**macOS / Linux:**

```bash
cd ~/Desktop
rm -rf ~/Desktop/workshop-lab-5b
```

---

## Help

If you get stuck, post your question in the **Lab Help** channel on Microsoft Teams. Include:
1. The **step number** you are on
2. The **command** you ran (copy and paste it)
3. The **full error message** you received (copy and paste it)
4. Your **operating system** (Windows, Mac, or Linux)
