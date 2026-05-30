# Lab 5A: Respond to a Compromised Access Key

**Session:** 5 — Incident Response on AWS  
**Track:** Cloud Security & IR  
**Difficulty:** Beginner  
**Estimated Time:** 20–25 minutes  
**Target Cert:** AWS Security Specialty

---

## Overview

In this lab, you will:

1. **Create an IAM user** simulating a user whose credentials were stolen
2. **Create access keys** for the user
3. **Simulate detection** — you are alerted that the credentials were found on a public GitHub repository
4. **Contain the incident** — deactivate the compromised access key
5. **Prove containment works** — test that the deactivated key can no longer authenticate
6. **Eradicate the threat** — permanently delete the access key

By the end of this lab, you will understand the **incident response lifecycle** — Detect → Contain → Eradicate → Recover — and how credential revocation is the first containment action in any cloud security incident.

---

## Prerequisites

- ✅ Completed **Lab 1A** (AWS CLI installed and configured)
- ✅ AWS CLI authenticated — run `aws sts get-caller-identity` and confirm it returns your account info

---

## Cost Notice

| Service | What It Is | Cost |
|---------|-----------|------|
| IAM | Identity and Access Management | Always Free |

**Estimated cost for this lab: $0.00**

---

## Concepts

Before we start, here is what each concept means:

**Incident Response (IR)** is the process of detecting, containing, and recovering from a security event. When credentials are stolen, a server is compromised, or data is exposed — incident response is what you do next.

**The IR Lifecycle** follows a standard pattern used by every security team:
1. **Detect** — Discover that something is wrong (alert, log analysis, external report)
2. **Contain** — Stop the bleeding immediately (revoke credentials, isolate systems)
3. **Eradicate** — Remove the threat completely (delete compromised keys, terminate malicious resources)
4. **Recover** — Return to normal operations (issue new credentials, restore services)

**Credential Revocation** is the most common containment action in cloud security. When an access key is compromised, you deactivate it immediately to prevent further unauthorized access.

**Deactivate vs. Delete:**
- **Deactivate** (set to `Inactive`) — The key still exists but cannot authenticate. This is **reversible** — you can reactivate it if the alert turns out to be a false alarm.
- **Delete** — The key is permanently removed. This is **irreversible** — you cannot recover it.

In real incident response, you **deactivate first** (immediate containment), investigate, and then **delete** once you confirm the compromise is real.

**Access Keys on GitHub** — One of the most common cloud security incidents is developers accidentally committing AWS access keys to public GitHub repositories. Automated scanners find these keys within minutes and use them to spin up cryptocurrency miners or steal data. AWS has a partnership with GitHub to detect this and notify you, but the damage can happen in seconds.

---

## ⚠️ Reminder: How to Read Commands in This Lab

Commands are inside gray code boxes. **📋 Copy and paste** them into your terminal.

**Placeholders** look like `<THIS>` — replace them (including the `<` and `>`) with your actual values.

Here are the placeholders you will use in this lab:

| Placeholder | What to Replace It With | Example |
|-------------|------------------------|---------|
| `<YOUR_PROFILE_NAME>` | Your AWS CLI profile name from Lab 1A | `AdministratorAccess-123456789012` |
| `<KEY_ID>` | The Access Key ID returned when you create the access key (Step 4) | `AKIAIOSFODNN7EXAMPLE` |
| `<SECRET_KEY>` | The Secret Access Key returned when you create the access key (Step 4) | `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY` |

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
mkdir ~\Desktop\workshop-lab-5a
cd ~\Desktop\workshop-lab-5a
```

**macOS / Linux:**

📋 Copy and paste:

```bash
mkdir ~/Desktop/workshop-lab-5a
cd ~/Desktop/workshop-lab-5a
```

> **What does this do?** Creates a new folder on your Desktop called `workshop-lab-5a` and moves your terminal into that folder.

**Step 2b: Verify you're in the right folder**

📋 Copy and paste:

```
pwd
```

**✅ You should see** a path ending in `workshop-lab-5a` (e.g., `C:\Users\YourName\Desktop\workshop-lab-5a` on Windows or `/Users/YourName/Desktop/workshop-lab-5a` on Mac).

> **💡 From now on, save ALL files you create in this lab to this folder.** When the lab says "save the file," save it here.

---

### Step 3: Create the Compromised User

Create an IAM user that simulates a developer whose credentials were stolen.

📋 Copy and paste:

```
aws iam create-user --user-name compromised-user
```

**✅ You should see** JSON output with the user details, including `"UserName": "compromised-user"`.

> **What does this do?** Creates a new IAM user called `compromised-user`. In a real scenario, this would be an existing developer or service account whose credentials were exposed.

---

### Step 4: Create Access Keys for the User

📋 Copy and paste:

```
aws iam create-access-key --user-name compromised-user
```

**✅ You should see** JSON output containing:

```json
{
    "AccessKey": {
        "UserName": "compromised-user",
        "AccessKeyId": "AKIA...",
        "SecretAccessKey": "wJalr...",
        "Status": "Active"
    }
}
```

> **📝 Write down BOTH values immediately:**
>
> - **AccessKeyId:** ______________________________
> - **SecretAccessKey:** ______________________________
>
> ⚠️ The SecretAccessKey is shown **only once**. You will need both values later in this lab.

---

### Step 5: Verify the Key Is Active

📋 Copy and paste:

```
aws iam list-access-keys --user-name compromised-user --query "AccessKeyMetadata[].{Id:AccessKeyId,Status:Status}" --output table
```

**✅ You should see:**

```
-----------------------------------------
|           ListAccessKeys              |
+----------------------+----------------+
|          Id          |    Status      |
+----------------------+----------------+
|  AKIA...             |  Active        |
+----------------------+----------------+
```

The key is **Active** — it can be used to authenticate API calls.

---

### Step 6: 🚨 Simulate Detection

> **SCENARIO:** You receive an urgent alert from your security team:
>
> *"ALERT: AWS access key `AKIA...` belonging to user `compromised-user` was found in a public GitHub repository. The key was committed 15 minutes ago. Automated scanners have likely already harvested it. Immediate action required."*
>
> This is a real scenario that happens every day. Your job now: **contain the incident as fast as possible.**

The clock is ticking. Every second the key remains active, an attacker could be using it.

---

### Step 7: Containment — Deactivate the Key

**This is the most critical step in incident response: stop the bleeding.**

📋 Copy and paste, **replacing `<KEY_ID>`** with the AccessKeyId from Step 4:

```
aws iam update-access-key --user-name compromised-user --access-key-id <KEY_ID> --status Inactive
```

**✅ No output means success.** The key is now deactivated.

> **What does this do?** Sets the access key status to `Inactive`. The key still exists, but any API call using it will be rejected. This is **immediate containment** — the attacker can no longer use the stolen credentials.

---

### Step 8: Verify the Key Is Now Inactive

📋 Copy and paste:

```
aws iam list-access-keys --user-name compromised-user --query "AccessKeyMetadata[].{Id:AccessKeyId,Status:Status}" --output table
```

**✅ You should see:**

```
-----------------------------------------
|           ListAccessKeys              |
+----------------------+----------------+
|          Id          |    Status      |
+----------------------+----------------+
|  AKIA...             |  Inactive      |
+----------------------+----------------+
```

The key is now **Inactive**. The attacker is locked out.

---

### Step 9: Test That the Key No Longer Works

Let's prove that the deactivated key cannot authenticate. Set the compromised credentials as environment variables and try to use them.

**Step 9a: Set the compromised credentials**

**Windows (PowerShell):**

📋 Copy and paste, **replacing the placeholders** with the values from Step 4:

```powershell
$env:AWS_ACCESS_KEY_ID="<KEY_ID>"
$env:AWS_SECRET_ACCESS_KEY="<SECRET_KEY>"
$env:AWS_DEFAULT_REGION="us-east-1"
Remove-Item Env:\AWS_PROFILE
```

**macOS / Linux:**

📋 Copy and paste, **replacing the placeholders** with the values from Step 4:

```bash
export AWS_ACCESS_KEY_ID="<KEY_ID>"
export AWS_SECRET_ACCESS_KEY="<SECRET_KEY>"
export AWS_DEFAULT_REGION="us-east-1"
unset AWS_PROFILE
```

**Step 9b: Try to authenticate with the deactivated key**

📋 Copy and paste:

```
aws sts get-caller-identity 2>&1
```

**✅ You should see an error:**

```
An error occurred (InvalidClientTokenId) when calling the GetCallerIdentity operation: The security token included in the request is invalid.
```

**This proves containment is working!** The compromised key can no longer be used to access your AWS account. Even if an attacker has the key, they are locked out.

---

### Step 10: Switch Back to Admin Credentials

**⚠️ Important:** You must clear the compromised credentials and restore your admin profile before continuing.

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

**✅ You should see** your admin role (with `AdministratorAccess` in the ARN), NOT `compromised-user`.

---

### Step 11: Eradication — Delete the Key Entirely

Now that you have confirmed the compromise is real (not a false alarm), permanently delete the key.

📋 Copy and paste, **replacing `<KEY_ID>`** with the AccessKeyId from Step 4:

```
aws iam delete-access-key --user-name compromised-user --access-key-id <KEY_ID>
```

**✅ No output means success.** The key is permanently gone.

> **What is the difference between deactivation and deletion?**
>
> | Action | Reversible? | When to Use |
> |--------|-------------|-------------|
> | Deactivate (Inactive) | ✅ Yes — can reactivate | Immediate containment. Use when you are not 100% sure it is a real compromise. |
> | Delete | ❌ No — permanent | Eradication. Use after you have confirmed the compromise and completed your investigation. |
>
> **In real IR:** Deactivate immediately (seconds), investigate (minutes to hours), then delete once confirmed.

---

### Step 12: Console Checkpoint

Let's verify the eradication in the AWS Console:

1. Go to [https://console.aws.amazon.com/](https://console.aws.amazon.com/)
2. Search for **IAM** in the top search bar and click it
3. Click **Users** in the left sidebar
4. Click on **compromised-user**
5. Click the **Security credentials** tab
6. Scroll down to **Access keys**
7. You should see **no access keys listed** — the key has been permanently deleted

**✅ Checkpoint:** The compromised user has no access keys. The threat has been eradicated. In a real incident, you would now move to the Recovery phase — issuing new credentials through a secure channel and reviewing what the attacker accessed.

---

## What You Just Did

You executed a complete incident response for a compromised access key:

1. **Detected** — Received an alert about exposed credentials
2. **Contained** — Deactivated the key within seconds (stopping the attacker)
3. **Verified containment** — Proved the key can no longer authenticate
4. **Eradicated** — Permanently deleted the compromised key

This is the exact process that security teams follow in production. The key insight: **speed matters**. The faster you deactivate a compromised key, the less damage an attacker can do. Automated detection + automated containment (which you will build in Lab 5C) can reduce response time from hours to seconds.

---

## Cert Prep Callout

**Target Certification:** AWS Security Specialty (SCS-C02)

The Security Specialty exam has an entire domain on incident response. You need to understand:
- The difference between deactivating and deleting access keys
- The order of operations: contain first, investigate second, eradicate third
- How to revoke IAM sessions (beyond just access keys)
- When to use `update-access-key` vs. `delete-access-key`

**Sample question type:** "An organization discovers that an IAM user's access keys have been exposed on a public repository. What should be the FIRST action taken?"  
**Answer:** Deactivate the access key immediately (containment before investigation)

---

## Troubleshooting

| Issue | What It Means | How to Fix It |
|-------|--------------|---------------|
| `An error occurred (EntityAlreadyExists)` when creating the user | The user already exists from a previous attempt | Delete it first: `aws iam delete-user --user-name compromised-user` then try again |
| `An error occurred (NoSuchEntity)` when deactivating the key | Wrong key ID or wrong username | Double-check the AccessKeyId from Step 4 and make sure you are using `compromised-user` |
| `get-caller-identity` still shows admin after setting env vars | `AWS_PROFILE` is overriding the access keys | Make sure you removed `AWS_PROFILE` (Step 9a includes this) |
| `get-caller-identity` still shows compromised-user after Step 10 | Env vars were not cleared | Run the commands in Step 10 again. Close and reopen your terminal if needed. |
| Cannot delete user in cleanup — "cannot delete entity with attached policies" | The user has policies attached | Delete policies first: `aws iam delete-user-policy --user-name compromised-user --policy-name <POLICY_NAME>` |

---

## Cleanup

**⚠️ Important:** Always clean up resources after completing a lab. Follow these steps in order.

### Step 1: Delete the IAM User

📋 Copy and paste:

```
aws iam delete-user --user-name compromised-user
```

**✅ No output means success.**

> **💡 If you get an error** saying the user has access keys, you may have skipped Step 11. Delete the key first:
> ```
> aws iam list-access-keys --user-name compromised-user
> aws iam delete-access-key --user-name compromised-user --access-key-id <KEY_ID>
> aws iam delete-user --user-name compromised-user
> ```

### Step 2: Delete Local Files

Remove the project folder:

**Windows (PowerShell):**

```powershell
cd ~\Desktop
Remove-Item -Recurse -Force ~\Desktop\workshop-lab-5a
```

**macOS / Linux:**

```bash
cd ~/Desktop
rm -rf ~/Desktop/workshop-lab-5a
```

---

## Help

If you get stuck, post your question in the **Lab Help** channel on Microsoft Teams. Include:
1. The **step number** you are on
2. The **command** you ran (copy and paste it)
3. The **full error message** you received (copy and paste it)
4. Your **operating system** (Windows, Mac, or Linux)
