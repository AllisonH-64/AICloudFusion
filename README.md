# AI Cloud Fusion — Lab Guides

Welcome to the AI Cloud Fusion workshop lab guides. These hands-on labs accompany the weekly workshop sessions and are designed to be completed independently using only the instructions in each guide.

They take you from creating your first AWS account all the way to shipping a grounded, guardrailed, cost-governed generative-AI chatbot — covering cloud fundamentals, security, incident response, infrastructure as code, CI/CD, observability, and AI engineering along the way.

## Getting Started

**New here?** Start with [Lab 1A](labs/session-01-cloud-concepts/lab-1a-aws-cli-setup.md) — it walks you through creating your AWS account and setting up everything you need from scratch. No prior experience required.

New to the terminology? Keep the [Glossary](labs/GLOSSARY.md) open in another tab.

## How to Use These Labs

Each session has its own folder containing three labs at different difficulty levels:

| Level | What to Expect |
|-------|---------------|
| **Lab A (Beginner)** | Foundational setup or introductory concepts |
| **Lab B (Intermediate)** | Core hands-on task with multiple AWS services |
| **Lab C (Advanced)** | Real-world engineering task — you build something practical |

Some sessions also include an optional **Side Quest** — a bonus lab that extends the session's work for those who want to go further.

Work through the labs in order (A → B → C). Each lab includes:

- 📋 Copy-and-paste commands with clear instructions
- 🔄 Placeholders clearly marked so you know what to replace
- 💡 Annotations explaining what each service does and why
- ✅ Console checkpoints to verify your work visually
- 🧹 Cleanup/decommission steps so you don't incur any costs

## Session Labs

| Session | Topic | Labs |
|---------|-------|------|
| 1 | Cloud Concepts & AWS Global Infrastructure | [Lab 1A: AWS Account & CLI Setup](labs/session-01-cloud-concepts/lab-1a-aws-cli-setup.md) · [Lab 1B: Cost Budget & SNS Alert](labs/session-01-cloud-concepts/lab-1b-cost-budget-sns-alert.md) · [Lab 1C: S3 Static Website](labs/session-01-cloud-concepts/lab-1c-s3-static-website.md) · [🧭 Side Quest 1D: HTTPS with CloudFront](labs/session-01-cloud-concepts/sidequest-lab-1d-cloudfront-https.md) |
| 2 | Core AWS Services | [Lab 2A: Launch EC2 Instance](labs/session-02-core-services/lab-2a-launch-ec2-instance.md) · [Lab 2B: EC2 + S3 with IAM Role](labs/session-02-core-services/lab-2b-ec2-s3-iam-role.md) · [Lab 2C: Serverless File Pipeline](labs/session-02-core-services/lab-2c-serverless-file-pipeline.md) |
| 3 | Cloud Security Fundamentals | [Lab 3A: IAM Least Privilege](labs/session-03-cloud-security/lab-3a-iam-least-privilege.md) · [Lab 3B: Groups, Custom Policy & MFA](labs/session-03-cloud-security/lab-3b-groups-custom-policy-mfa.md) · [Lab 3C: Roles & CloudTrail Audit](labs/session-03-cloud-security/lab-3c-roles-cloudtrail-audit.md) |
| 4 | Threat Detection & AWS Security Services | [Lab 4A: GuardDuty Findings](labs/session-04-threat-detection/lab-4a-guardduty-findings.md) · [Lab 4B: CloudTrail Investigation](labs/session-04-threat-detection/lab-4b-cloudtrail-investigation.md) · [Lab 4C: Automated Security Alerts](labs/session-04-threat-detection/lab-4c-automated-security-alerts.md) · [🧭 Side Quest 4D: Human-Readable Alerts](labs/session-04-threat-detection/sidequest-lab-4d-human-readable-alerts.md) |
| 5 | Incident Response on AWS | [Lab 5A: Credential Revocation](labs/session-05-incident-response/lab-5a-credential-revocation.md) · [Lab 5B: Investigate & Report](labs/session-05-incident-response/lab-5b-investigate-and-report.md) · [Lab 5C: Automated Remediation](labs/session-05-incident-response/lab-5c-automated-remediation.md) |
| 6 | AWS Well-Architected Framework | [Lab 6A: S3 Security & Reliability](labs/session-06-well-architected/lab-6a-s3-security-reliability.md) · [Lab 6B: Lambda Performance & Operations](labs/session-06-well-architected/lab-6b-lambda-performance-operations.md) · [Lab 6C: Cost Optimization & Sustainability](labs/session-06-well-architected/lab-6c-cost-sustainability.md) |
| 7 | IaC Fundamentals with OpenTofu | [Lab 7A: OpenTofu Setup](labs/session-07-iac-fundamentals/lab-7a-opentofu-setup.md) · [Lab 7B: Lambda with IaC](labs/session-07-iac-fundamentals/lab-7b-lambda-with-iac.md) · [Lab 7C: Event-Driven Modules](labs/session-07-iac-fundamentals/lab-7c-event-driven-modules.md) |
| 8 | CI/CD for Infrastructure | [Lab 8A: GitHub OIDC](labs/session-08-cicd/lab-8a-github-oidc.md) · [Lab 8B: Pipeline Plan & Apply](labs/session-08-cicd/lab-8b-pipeline-plan-apply.md) · [Lab 8C: Drift Detection](labs/session-08-cicd/lab-8c-drift-detection.md) |
| 9 | Monitoring & Observability | [Lab 9A: CloudWatch Alarms](labs/session-09-monitoring/lab-9a-cloudwatch-alarms.md) · [Lab 9B: Diagnose with Logs](labs/session-09-monitoring/lab-9b-diagnose-with-logs.md) · [Lab 9C: CI Smoke Test](labs/session-09-monitoring/lab-9c-ci-smoke-test.md) |
| 10 | Chatbot Observability | [Lab 10A: Deploy Chatbot](labs/session-10-chatbot-observability/lab-10a-deploy-chatbot.md) · [Lab 10B: Monitor Dependencies](labs/session-10-chatbot-observability/lab-10b-monitor-dependencies.md) · [Lab 10C: Resilient Fallback](labs/session-10-chatbot-observability/lab-10c-resilient-fallback.md) |
| 11 | AI Engineering | [Lab 11A: Bedrock Chatbot](labs/session-11-ai-engineering/lab-11a-bedrock-chatbot.md) · [Lab 11B: Prompt Engineering](labs/session-11-ai-engineering/lab-11b-prompt-engineering.md) · [Lab 11C: AI Monitoring](labs/session-11-ai-engineering/lab-11c-ai-monitoring.md) |
| 12 | RAG, Guardrails & AI Capstone | [Session Overview](labs/session-12-rag-guardrails-capstone/README.md) · [Lab 12A: RAG Knowledge Base](labs/session-12-rag-guardrails-capstone/lab-12a-rag-knowledge-base.md) · [Lab 12B: Bedrock Guardrails](labs/session-12-rag-guardrails-capstone/lab-12b-bedrock-guardrails.md) · [Lab 12C: Capstone — Cost Governance](labs/session-12-rag-guardrails-capstone/lab-12c-capstone-cost-governance.md) |
| 13 | Capstone — Build Something You Own | [Capstone Guide](labs/session-13-capstone/README.md) *(no labs — a self-directed project)* |

## Prerequisites

- An email address, phone number, and credit/debit card (for AWS account creation)
- A computer running Windows, macOS, or Linux
- No prior AWS or cloud experience needed — Lab 1A covers everything from scratch

## Cost

Nearly every lab uses **Always Free** AWS resources and includes cleanup steps at the end, so most sessions cost **$0.00**. The exceptions are the AI Engineering sessions (11 and 12), which use Amazon Bedrock and Guardrails — a few cents of usage, kept to **well under $0.20** for the whole capstone.

Every lab has a cost notice listing the services it uses and its estimated cost. **Always complete the Cleanup section at the end of each lab** to ensure no resources are left running.

## Need Help?

If you get stuck on any lab, post your question in the **Lab Help** channel on Microsoft Teams. Include:
1. The **command** you ran (copy and paste it)
2. The **full error message** you received
3. Which **step number** you are on
4. Your **operating system** (Windows, macOS, or Linux)
