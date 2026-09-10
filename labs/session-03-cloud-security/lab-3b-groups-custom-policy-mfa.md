# Lab 3B: IAM Groups, Custom Policies & MFA

**Session:** 3 — Cloud Security Fundamentals  
**Track:** Cloud Security & IR  
**Difficulty:** Intermediate  
**Estimated Time:** 25–30 minutes  
**Target Cert:** AWS Security Specialty

---

## Overview

In this lab, you will:

1. **Create an IAM group** — assign permissions to many users at once
2. **Write a custom policy with an explicit Deny** — prevent dangerous actions even if another policy allows them
3. **Add a user to the group** — the user inherits the group's permissions automatically
4. **Test before and after** — the user can delete with full access, then loses that ability the moment it joins the group with an explicit Deny
5. **Enable MFA on your root account** — add a second layer of security

By the end of this lab, you will understand how groups simplify permission management, how explicit Deny overrides Allow, and why MFA is a critical security control.

---

## Prerequisites

- ✅ Completed **Lab 1A** (AWS CLI installed and configured)
- ✅ Completed **Lab 3A** (familiar with IAM users and policies)
- ✅ AWS CLI authenticated — run `aws sts get-caller-identity` and confirm it returns your account info
- ✅ **VS Code** installed (from Lab 1B) — any text editor works, but these labs assume VS Code
- ✅ A smartphone with an authenticator app installed (Google Authenticator, Authy, or Microsoft Authenticator) — needed for the MFA section (MFA should have been done since Session 1, but is important enough in Security to revisit for those who ignored that step.)

---

## Cost Notice

| Service | What It Is | Cost |
|---------|-----------|------|
| IAM | Identity and Access Management | Always Free |
| Amazon S3 | Cloud storage for files | $0.023 per GB/month |
| MFA | Multi-Factor Authentication | Always Free |

**Estimated cost for this lab: $0.00** — the test bucket holds a single tiny file and is deleted in cleanup.

---

## Concepts

Before we start, here is what each concept means:

**IAM Group** is a collection of IAM users. Instead of attaching the same policy to 50 users individually, you attach it to a group and add all 50 users to that group. When you update the group's policy, everyone in the group gets the updated permissions automatically. This is how real companies manage access at scale.

**Explicit Deny** is a `"Effect": "Deny"` statement in a policy. It is the strongest permission in AWS — if ANY policy attached to a user contains an explicit Deny for an action, that action is blocked, **even if another policy says Allow**. Think of it as a security override that cannot be bypassed.

**Implicit Deny** is what happens when there is no policy at all. If you do not have a policy that says "Allow" for an action, you are denied by default. The difference from explicit Deny is that an implicit deny CAN be overridden by adding an Allow policy. An explicit Deny cannot.

**MFA (Multi-Factor Authentication)** requires two forms of proof to log in: something you know (your password) and something you have (a code from your phone). Even if an attacker steals your password, they cannot log in without your phone. AWS strongly recommends MFA on all accounts, especially the root account.

**Defense in Depth** is the security principle of using multiple layers of protection. If one layer fails (password stolen), another layer catches it (MFA blocks the login). Groups + explicit Deny + MFA together create multiple layers of defense.

---

## ⚠️ Reminder: How to Read Commands in This Lab

Commands are inside gray code boxes. **📋 Copy and paste** them into your terminal.

**Placeholders** look like `<THIS>` — replace them (including the `<` and `>`) with your actual values.

Here are the placeholders you will use in this lab:

| Placeholder | What to Replace It With | Example |
|-------------|------------------------|---------|
| `<YOUR_PROFILE_NAME>` | Your AWS CLI profile name from Lab 1A | `AdministratorAccess-123456789012` |
| `<YOUR_BUCKET_NAME>` | A globally unique S3 bucket name you choose | `jane-doe-lab3b-groups` |

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
mkdir ~\Desktop\workshop-lab-3b
cd ~\Desktop\workshop-lab-3b
```

**macOS / Linux:**

📋 Copy and paste:

```bash
mkdir ~/Desktop/workshop-lab-3b
cd ~/Desktop/workshop-lab-3b
```

> **What does this do?** Creates a new folder on your Desktop called `workshop-lab-3b` and moves your terminal into that folder.

**Step 2b: Verify you're in the right folder**

📋 Copy and paste:

```
pwd
```

**✅ You should see** a path ending in `workshop-lab-3b` (e.g., `C:\Users\YourName\Desktop\workshop-lab-3b` on Windows or `/Users/YourName/Desktop/workshop-lab-3b` on Mac).

> **💡 From now on, save ALL files you create in this lab to this folder.** When the lab says "save the file," save it here.

**Step 2c: Open the folder in VS Code**

📋 Copy and paste:

```
code .
```

> **What does this do?** This opens VS Code with `workshop-lab-3b` as its **file tree** on the left, so the policy file you create in Step 5 lands in the right place. (You set up the `code` command in Lab 1B — if you see `'code' is not recognized`, close and reopen your terminal, or revisit Lab 1B, Step 6.)

---

### Step 3: Create an S3 Bucket and Upload a Test File

📋 Copy and paste this command, **replacing `<YOUR_BUCKET_NAME>`**:

```
aws s3 mb s3://<YOUR_BUCKET_NAME> --region us-east-1
```

> **🔄 Example:**
> ```
> aws s3 mb s3://jane-doe-lab3b-groups --region us-east-1
> ```

**✅ You should see:**

```
make_bucket: <YOUR_BUCKET_NAME>
```

Now upload a test file:

**Windows (PowerShell):**

📋 Copy and paste, **replacing `<YOUR_BUCKET_NAME>`**:

```powershell
"Confidential HR data - do not delete" | Out-File -Encoding utf8 test-file.txt
aws s3 cp test-file.txt s3://<YOUR_BUCKET_NAME>/test-file.txt
```

**macOS / Linux:**

📋 Copy and paste, **replacing `<YOUR_BUCKET_NAME>`**:

```bash
echo "Confidential HR data - do not delete" > test-file.txt
aws s3 cp test-file.txt s3://<YOUR_BUCKET_NAME>/test-file.txt
```

**✅ You should see:**

```
upload: ./test-file.txt to s3://<YOUR_BUCKET_NAME>/test-file.txt
```

---

### Step 4: Create an IAM Group

Now create a group that will hold the read-only permissions.

📋 Copy and paste:

```
aws iam create-group --group-name workshop-s3-readers
```

**What does this do?** Creates an IAM group called `workshop-s3-readers`. Any user added to this group will inherit its permissions.

**✅ You should see** JSON output with the group details, including `"GroupName": "workshop-s3-readers"`.

> **💡 Why use a group?** Imagine you have 20 data analysts who all need read access to the same S3 bucket. Instead of attaching a policy to each user individually (and updating 20 policies when something changes), you attach the policy to a group and add all 20 users to it. One policy change updates everyone instantly.

---

### Step 5: Write the Custom Policy with Explicit Deny

This policy has TWO statements — one that allows read access, and one that explicitly denies destructive actions.

**Step 5a:** In the VS Code file tree, click the **New File** icon and name the file `custom-s3-policy.json`.

**Step 5b:** 📋 Copy and paste this entire block into it, **replacing `<YOUR_BUCKET_NAME>`** in ALL FOUR places:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowReadAccess",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::<YOUR_BUCKET_NAME>",
                "arn:aws:s3:::<YOUR_BUCKET_NAME>/*"
            ]
        },
        {
            "Sid": "ExplicitDenyDestructiveActions",
            "Effect": "Deny",
            "Action": [
                "s3:DeleteObject",
                "s3:DeleteBucket"
            ],
            "Resource": [
                "arn:aws:s3:::<YOUR_BUCKET_NAME>",
                "arn:aws:s3:::<YOUR_BUCKET_NAME>/*"
            ]
        }
    ]
}
```

> **🔄 Example:** If your bucket name is `jane-doe-lab3b-groups`, replace all four instances of `<YOUR_BUCKET_NAME>` with `jane-doe-lab3b-groups`.

>[!TIP]
>When you highlight one of the placeholder you should notice all of them get highlighted. Clicking Ctrl+Left Shift+L will allow you to edit them simultaneously.

**Step 5c:** **Save** the file (**Ctrl+S** / **Cmd+S**). You should see `custom-s3-policy.json` appear in the file tree.

> **⚠️ Common mistake:** Make sure the name is exactly `custom-s3-policy.json`, not `custom-s3-policy.json.txt`, and that you replaced `<YOUR_BUCKET_NAME>` in ALL FOUR places (two in each statement). Naming it in the file tree with a `.json` ending sets the file type automatically.

> **What does this file do?** It defines two rules:
> - **Statement 1 (Allow):** The user can list the bucket and read files from it
> - **Statement 2 (Deny):** The user CANNOT delete objects or the bucket — even if another policy tries to allow it
>
> The explicit Deny in Statement 2 is the key concept. It acts as a safety net that cannot be overridden.

**Understanding Explicit Deny vs. Implicit Deny:**

| Scenario | Result | Why |
|----------|--------|-----|
| No policy exists for an action | **Denied** (implicit) | No Allow = denied by default. But you CAN fix this by adding an Allow policy. |
| A Deny statement exists for an action | **Denied** (explicit) | Deny ALWAYS wins. Even if you add 10 Allow policies, the Deny still blocks it. |
| An Allow statement exists, no Deny | **Allowed** | The Allow grants permission and nothing blocks it. |
| Both Allow AND Deny exist for the same action | **Denied** | Deny ALWAYS wins over Allow. Always. |

> **💡 Real-world use case:** A company might give developers broad S3 access but add an explicit Deny on the bucket containing financial records. Even if a developer accidentally gets an overly permissive policy, the Deny ensures they can never delete financial data.

---

### Step 6: Attach the Policy to the Group

📋 Copy and paste:

```
aws iam put-group-policy --group-name workshop-s3-readers --policy-name CustomS3ReadPolicy --policy-document file://custom-s3-policy.json
```

**What does this do?**
- `put-group-policy` — attaches an inline policy to the group (not a user)
- Anyone in the `workshop-s3-readers` group will get these permissions

**✅ No output means success.**

---

### Step 7: Create a User and Give It Broad S3 Access

This time you will deliberately give the user **more** access than it should have — full S3 access, attached directly to the user. This sets up the real test: can the group's explicit Deny override a broad Allow? (Think of a user who was *accidentally* granted too much access.)

**Step 7a: Create the user**

📋 Copy and paste:

```
aws iam create-user --user-name workshop-group-member
```

**✅ You should see** JSON output with `"UserName": "workshop-group-member"`.

**Step 7b: Attach full S3 access directly to the user**

📋 Copy and paste:

```
aws iam attach-user-policy --user-name workshop-group-member --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess
```

**What does this do?** Attaches the AWS-managed `AmazonS3FullAccess` policy **directly to the user**. This allows every S3 action — including delete — on every bucket. The user is **not** in the group yet.

**✅ No output means success.**

---

### Step 8: Prove the User CAN Delete (Before Joining the Group)

**Step 8a: Create access keys**

📋 Copy and paste:

```
aws iam create-access-key --user-name workshop-group-member
```

**✅ You should see** JSON output with `AccessKeyId` and `SecretAccessKey`.

> **📝 Write down BOTH values immediately:**
>
> - **AccessKeyId:** ______________________________
> - **SecretAccessKey:** ______________________________

**Step 8b: Set the user's credentials**

**Windows (PowerShell):**

📋 Copy and paste, **replacing the placeholders** with the values from Step 8a:

```powershell
$env:AWS_ACCESS_KEY_ID="<ACCESS_KEY_ID_FROM_STEP_8A>"
$env:AWS_SECRET_ACCESS_KEY="<SECRET_ACCESS_KEY_FROM_STEP_8A>"
$env:AWS_DEFAULT_REGION="us-east-1"
Remove-Item Env:\AWS_PROFILE
```

**macOS / Linux:**

📋 Copy and paste, **replacing the placeholders** with the values from Step 8a:

```bash
export AWS_ACCESS_KEY_ID="<ACCESS_KEY_ID_FROM_STEP_8A>"
export AWS_SECRET_ACCESS_KEY="<SECRET_ACCESS_KEY_FROM_STEP_8A>"
export AWS_DEFAULT_REGION="us-east-1"
unset AWS_PROFILE
```

**Step 8c: Verify you are the user**

📋 Copy and paste:

```
aws sts get-caller-identity
```

**✅ You should see** `workshop-group-member` in the output.

**Step 8d: Test DELETE — should WORK ✅**

📋 Copy and paste, **replacing `<YOUR_BUCKET_NAME>`**:

```
aws s3 rm s3://<YOUR_BUCKET_NAME>/test-file.txt 2>&1
```

**✅ You should see:** `delete: s3://<YOUR_BUCKET_NAME>/test-file.txt`. Right now the user **can** delete the file, because `AmazonS3FullAccess` allows it and nothing is blocking it.

**Step 8e: Put the file back** (you will need it for the next test). 📋 Copy and paste, **replacing `<YOUR_BUCKET_NAME>`**:

```
aws s3 cp test-file.txt s3://<YOUR_BUCKET_NAME>/test-file.txt
```

**✅ You should see** the upload succeed. (The user can also upload, because it still has full S3 access.)

---

### Step 9: Switch Back to Admin and Add the User to the Group

The group carries the **explicit Deny** on delete (from Step 5). Now you will add the user to it — while keeping the full-access policy attached — to see which one wins.

**Step 9a: Switch back to your admin credentials**

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

**Step 9b: Add the user to the group**

📋 Copy and paste:

```
aws iam add-user-to-group --user-name workshop-group-member --group-name workshop-s3-readers
```

**What does this do?** The user now has BOTH: `AmazonS3FullAccess` (attached directly, which **allows** delete) and the group's policy (which **explicitly denies** delete on your bucket).

**✅ No output means success.**

> **⏳ Wait about 10 seconds** for the change to take effect before testing.

---

### Step 10: Prove the User Can NO LONGER Delete (Explicit Deny Wins)

**Step 10a: Switch back to the user's credentials**

**Windows (PowerShell):**

📋 Copy and paste (the same keys from Step 8a):

```powershell
$env:AWS_ACCESS_KEY_ID="<ACCESS_KEY_ID_FROM_STEP_8A>"
$env:AWS_SECRET_ACCESS_KEY="<SECRET_ACCESS_KEY_FROM_STEP_8A>"
$env:AWS_DEFAULT_REGION="us-east-1"
Remove-Item Env:\AWS_PROFILE
```

**macOS / Linux:**

📋 Copy and paste (the same keys from Step 8a):

```bash
export AWS_ACCESS_KEY_ID="<ACCESS_KEY_ID_FROM_STEP_8A>"
export AWS_SECRET_ACCESS_KEY="<SECRET_ACCESS_KEY_FROM_STEP_8A>"
export AWS_DEFAULT_REGION="us-east-1"
unset AWS_PROFILE
```

**Step 10b: Test DELETE — should now be DENIED ❌**

📋 Copy and paste, **replacing `<YOUR_BUCKET_NAME>`**:

```
aws s3 rm s3://<YOUR_BUCKET_NAME>/test-file.txt 2>&1
```

**✅ You should see an error:** `delete failed` with `An error occurred (AccessDenied)`.

**This is the whole point of the lab.** The same user, running the same command, could delete the file moments ago. The only thing that changed is that it joined a group with an **explicit Deny**. The user *still* has `AmazonS3FullAccess` (which allows delete) — but the explicit Deny overrides it. **Deny always wins.**

> **💡 Why this matters:** you cannot always control every Allow a user accumulates (from groups, directly attached policies, federated roles, and so on). An explicit Deny is a guardrail that holds no matter how many Allows pile up.

---

### Step 11: Switch Back to Admin Credentials

**⚠️ Important:** Clear the user's credentials and restore your admin profile before continuing.

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

**✅ You should see** your admin role (with `AdministratorAccess` in the ARN).

---

### Step 12: Console Checkpoint — Groups

Let's verify the group setup in the AWS Console:

1. Go to [https://console.aws.amazon.com/](https://console.aws.amazon.com/)
2. Search for **IAM** in the top search bar and click it
3. Click **User groups** in the left sidebar
4. Click on **workshop-s3-readers**
5. You should see:
   - **Users** tab: `workshop-group-member` is listed as a member
   - **Permissions** tab: `CustomS3ReadPolicy` is listed as an inline policy
6. Click the policy name to expand it — you should see both the Allow and Deny statements

**✅ Checkpoint:** The group, its member, and its policy are all visible in the console. This is how administrators manage permissions at scale in real organizations.

---

### Step 13: Enable MFA on Your Root Account

MFA is the single most important security control you can enable on your AWS account. If your root account password is compromised, MFA prevents the attacker from logging in.

> **⚠️ This is a console-only step.** MFA setup requires scanning a QR code with your phone, so it cannot be done from the CLI.

> **📱 You will need:** Your smartphone with an authenticator app installed (Google Authenticator, Authy, or Microsoft Authenticator).

**Step 13a: Navigate to Security Credentials**

1. Sign in to the [AWS Console](https://console.aws.amazon.com/) as the **root user** (use your root email and password, NOT your Identity Center user)
2. Click your **account name** in the top-right corner of the console
3. Click **Security credentials**

**Step 13b: Set Up MFA**

1. Scroll down to the **Multi-factor authentication (MFA)** section
2. Click **Assign MFA device**
3. For **Device name**, enter: `my-phone` (or any name you will remember)
4. Select **Authenticator app**
5. Click **Next**

**Step 13c: Link Your Authenticator App**

1. Click **Show QR code**
2. Open your authenticator app on your phone (Google Authenticator, Authy, etc.)
3. In the app, tap the **+** button to add a new account
4. Select **Scan QR code** and point your phone camera at the QR code on screen
5. Your app will start generating 6-digit codes that change every 30 seconds

**Step 13d: Verify MFA**

1. Enter the **current 6-digit code** from your authenticator app into the **MFA code 1** field
2. **Wait** for the code to change (about 30 seconds)
3. Enter the **new 6-digit code** into the **MFA code 2** field
4. Click **Add MFA**

**✅ Checkpoint:** You should see a success message. The MFA section now shows your device as active. Next time you log in as the root user, you will need both your password AND a code from your phone.

> **💡 Why MFA matters:** In 2023, AWS reported that accounts without MFA were 20x more likely to be compromised. MFA is free, takes 2 minutes to set up, and is the single best thing you can do to protect your account.

> **⚠️ Do NOT lose access to your authenticator app.** If you lose your phone or delete the app, you will need to contact AWS Support to regain access to your root account. Consider backing up your MFA codes (Authy does this automatically).

---

## What You Just Did

You built a multi-layered security setup:

1. **Created a group** — the scalable way to manage permissions for multiple users
2. **Wrote a policy with explicit Deny** — a safety net that cannot be overridden
3. **Proved that Deny always wins** — the user could delete the file with full S3 access, but the group's explicit Deny blocked it the moment the user joined
4. **Enabled MFA** — added a second authentication factor to your root account

This is **defense in depth** — multiple layers of security working together. If one layer fails, the others still protect you.

---

## Cert Prep Callout

**Target Certification:** AWS Security Specialty (SCS-C02)

The Security Specialty exam tests these concepts heavily:
- **Policy evaluation logic:** How AWS decides Allow vs. Deny (explicit Deny always wins)
- **Groups vs. direct user policies:** When to use each approach
- **MFA enforcement:** How to require MFA for specific actions using policy conditions
- **Least privilege at scale:** Using groups to manage permissions for large teams

**Sample question type:** "A user has an Allow policy for S3 full access attached directly, and is also in a group with an explicit Deny on S3 delete. Can the user delete S3 objects?"  
**Answer:** No — explicit Deny always overrides Allow, regardless of where the policies are attached.

---

## Troubleshooting

| Issue | What It Means | How to Fix It |
|-------|--------------|---------------|
| `An error occurred (EntityAlreadyExists)` when creating the group or user | The resource already exists from a previous attempt | Delete it first (see cleanup steps) and try again |
| `An error occurred (MalformedPolicyDocument)` when attaching the policy | Your JSON file has a syntax error | Open `custom-s3-policy.json` and check for missing commas, brackets, or quotes. Make sure you replaced `<YOUR_BUCKET_NAME>` in all four places. |
| Delete in **Step 10b** shows "Access Denied" | This is correct behavior! | The explicit Deny is working as intended — Access Denied IS the expected result at Step 10b. (In Step 8d, *before* joining the group, the same delete should have **succeeded**.) |
| Delete in **Step 8d** fails with "Access Denied" (but should work) | The full-access policy hasn't propagated yet, or the user was added to the group too early | Wait ~10 seconds after Step 7b and retry. Make sure you have not yet run Step 9b (adding the user to the group) at this point. |
| Cannot find "Security credentials" in the console | You may be logged in as your Identity Center user | Sign out and sign back in as the **root user** (the email you used to create the account) |
| MFA code is rejected | The code expired or there is a time sync issue | Wait for a fresh code. Make sure your phone's clock is set to automatic. |
| `get-caller-identity` still shows the user after cleanup | Env vars were not cleared | Run the commands in Step 11 again. Close and reopen your terminal if needed. |

---

## Cleanup

**⚠️ Important:** Always clean up resources after completing a lab. Follow these steps in order.

> **Note:** Do NOT remove MFA from your root account — that should stay enabled permanently.

### Step 1: Delete the Access Key

📋 Copy and paste, **replacing `<ACCESS_KEY_ID_FROM_STEP_8A>`**:

```
aws iam delete-access-key --user-name workshop-group-member --access-key-id <ACCESS_KEY_ID_FROM_STEP_8A>
```

**✅ No output means success.**

### Step 2: Remove the User from the Group

📋 Copy and paste:

```
aws iam remove-user-from-group --user-name workshop-group-member --group-name workshop-s3-readers
```

**✅ No output means success.**

### Step 2b: Detach the Full S3 Access Policy

The user still has `AmazonS3FullAccess` attached directly (from Step 7b). You must detach it before the user can be deleted.

📋 Copy and paste:

```
aws iam detach-user-policy --user-name workshop-group-member --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess
```

**✅ No output means success.**

### Step 3: Delete the User

📋 Copy and paste:

```
aws iam delete-user --user-name workshop-group-member
```

**✅ No output means success.**

### Step 4: Delete the Group Policy

📋 Copy and paste:

```
aws iam delete-group-policy --group-name workshop-s3-readers --policy-name CustomS3ReadPolicy
```

**✅ No output means success.**

### Step 5: Delete the Group

📋 Copy and paste:

```
aws iam delete-group --group-name workshop-s3-readers
```

**✅ No output means success.**

### Step 6: Empty and Delete the S3 Bucket

📋 Copy and paste, **replacing `<YOUR_BUCKET_NAME>`**:

```
aws s3 rm s3://<YOUR_BUCKET_NAME> --recursive
```

```
aws s3 rb s3://<YOUR_BUCKET_NAME>
```

**✅ You should see** `remove_bucket: <YOUR_BUCKET_NAME>`.

### Step 7: Delete Local Files

> **⚠️ Close VS Code first.** If VS Code still has the `workshop-lab-3b` folder open, the delete will fail — especially on Windows. Choose **File → Close Folder** or quit VS Code before running the commands below.

Remove the project folder:

**Windows (PowerShell):**

```powershell
cd ~\Desktop
Remove-Item -Recurse -Force ~\Desktop\workshop-lab-3b
```

**macOS / Linux:**

```bash
cd ~/Desktop
rm -rf ~/Desktop/workshop-lab-3b
```

---

## Help

If you get stuck, post your question in the **Lab Help** channel on Microsoft Teams. Include:
1. The **step number** you are on
2. The **command** you ran (copy and paste it)
3. The **full error message** you received (copy and paste it)
4. Your **operating system** (Windows, Mac, or Linux)
