# AWS Services & Concepts Glossary

A quick reference for every AWS service and concept used in the labs. Bookmark this page and refer back whenever you forget what something is.

---

## AWS Services

| Service | What It Does (Plain English) | First Used In |
|---------|------------------------------|---------------|
| **AWS CLI** | A program on your computer that lets you control AWS by typing commands | Lab 1A |
| **IAM Identity Center** | The modern, secure login system for AWS accounts (SSO) | Lab 1A |
| **AWS Organizations** | Groups multiple AWS accounts together under one management structure | Lab 1A |
| **AWS STS** | Security Token Service — verifies your identity and issues temporary credentials | Lab 1A |
| **Amazon SNS** | Simple Notification Service — sends messages (email, SMS) to subscribers | Lab 1B |
| **AWS Budgets** | Tracks your AWS spending and sends alerts when you approach a limit | Lab 1B |
| **Amazon S3** | Simple Storage Service — cloud storage for files (buckets and objects) | Lab 1C |
| **Amazon CloudFront** | Global content delivery network (CDN) that caches content at edge locations worldwide and adds HTTPS | Lab 1D (side quest) |
| **AWS Certificate Manager (ACM)** | Provides free, auto-renewing SSL/TLS certificates (for CloudFront, must live in `us-east-1`) | Lab 1D (side quest) |
| **Amazon EC2** | Elastic Compute Cloud — virtual servers you rent in the cloud | Lab 2A |
| **AWS Systems Manager** | Manages and connects to EC2 instances (Session Manager = remote terminal) | Lab 2A |
| **AWS Lambda** | Serverless compute — runs your code without managing servers, only when triggered | Lab 2C |
| **Lambda Function URL** | A public HTTP endpoint for a Lambda function (no API Gateway needed) | Lab 2C |
| **IAM** | Identity and Access Management — controls who can do what in your account | Lab 3A |
| **Amazon GuardDuty** | Continuous threat detection — monitors for suspicious activity 24/7 | Lab 4A |
| **AWS CloudTrail** | Audit log of every API call in your account (who did what, when) | Lab 4B |
| **Amazon EventBridge** | Event routing — matches events to rules and triggers actions automatically | Lab 4C |
| **AWS Security Hub** | Aggregates security findings from multiple services into one dashboard | Session 4 (lecture) |
| **AWS Config** | Tracks configuration changes to your resources over time | Session 4 (lecture) |
| **Amazon DynamoDB** | Fully managed NoSQL key-value database; used here to lock OpenTofu state | Lab 7A |
| **Amazon CloudWatch** | Monitoring service — collects metrics, stores logs, triggers alarms | Lab 9A |
| **CloudWatch Alarm** | A rule that watches a metric and takes action when it crosses a threshold | Lab 9A |
| **CloudWatch Logs** | Stores text output from Lambda, EC2, and other services (log groups + streams) | Lab 9B |
| **CloudWatch Logs Insights** | Query engine for searching and analysing logs at scale | Lab 9B |
| **CloudWatch Dashboard** | Customizable page showing multiple metrics in a single operational view | Lab 9C |
| **CloudWatch Metric Filter** | A rule that extracts numbers from structured logs and publishes them as custom metrics | Lab 10B |
| **AWS Systems Manager Parameter Store** | Free service for storing configuration values that Lambda reads at runtime | Lab 10C |
| **Amazon Bedrock** | Managed AI service providing access to foundation models via API (no ML expertise needed) | Lab 11A |
| **Amazon API Gateway** | Creates public HTTP endpoints for Lambda functions (REST APIs accessible from browsers) | Lab 11A |
| **Amazon Bedrock Knowledge Bases / OpenSearch Serverless** | Managed vector database + retrieval used by production RAG systems (this lab builds a lightweight version by hand) | Lab 12A |
| **Amazon Bedrock Guardrails** | Enforced safety layer that inspects inputs and outputs against your policies, independent of the prompt | Lab 12B |

---

## Tools & Platforms (Non-AWS)

| Tool | What It Does (Plain English) | First Used In |
|------|------------------------------|---------------|
| **OpenTofu** | Open-source Infrastructure-as-Code tool that reads `.tf` files and creates, updates, or destroys cloud resources to match | Lab 7A |
| **HCL (HashiCorp Configuration Language)** | The language OpenTofu `.tf` configuration files are written in | Lab 7A |
| **Git** | Version-control system that tracks timestamped snapshots (commits) of your code | Lab 7A |
| **GitHub Actions** | GitHub's built-in CI/CD engine; workflows are defined as YAML in `.github/workflows/` and run on GitHub's servers | Lab 8A |

---

## Key Concepts

| Concept | What It Means | First Used In |
|---------|---------------|---------------|
| **Region** | A geographic area with multiple AWS data centers (e.g., us-east-1 = N. Virginia) | Lab 1A |
| **Availability Zone (AZ)** | An individual data center within a region | Session 1 (lecture) |
| **Free Tier** | AWS services you can use at no cost within specified limits | Lab 1A |
| **Always Free** | Services that are free forever (not just 12 months) within usage limits | Lab 1B |
| **Bucket** | An S3 container for storing files (like a folder in the cloud) | Lab 1B |
| **Bucket Policy** | A JSON document that controls who can access files in an S3 bucket | Lab 1C |
| **Static Website Hosting** | S3 feature that serves HTML files as a website | Lab 1C |
| **CDN (Content Delivery Network)** | Infrastructure that caches content at distributed data centers, serving each user from the nearest one | Lab 1D (side quest) |
| **Edge Location** | A CloudFront data center near users that caches and serves content with low latency | Lab 1D (side quest) |
| **Origin** | The source CloudFront fetches content from (an S3 bucket, in this lab) | Lab 1D (side quest) |
| **Origin Access Control (OAC)** | Lets CloudFront read a private S3 bucket via signed requests, blocking direct public access | Lab 1D (side quest) |
| **HTTPS / TLS** | HTTP encrypted with Transport Layer Security; CloudFront provisions free certificates automatically | Lab 1D (side quest) |
| **Distribution** | A deployed CloudFront configuration (origins, cache behaviour, delivery settings) | Lab 1D (side quest) |
| **Cache Invalidation** | Forcing CloudFront to drop cached files before their TTL so updates appear immediately | Lab 1D (side quest) |
| **Block Public Access** | An S3 setting that blocks all public access regardless of bucket policy | Lab 1D (side quest) |
| **Instance** | A single virtual server running on EC2 | Lab 2A |
| **Instance Type** | The size of an EC2 server (CPU + memory). `t2.micro` = smallest/free | Lab 2A |
| **AMI** | Amazon Machine Image — the operating system template for an EC2 instance | Lab 2A |
| **Instance Profile** | A container that attaches an IAM role to an EC2 instance | Lab 2A |
| **Session Manager** | A way to connect to EC2 instances through your browser (no SSH keys needed) | Lab 2A |
| **Presigned URL** | A temporary URL that grants permission to upload/download from S3 (expires) | Lab 2C |
| **CORS** | Cross-Origin Resource Sharing — browser security that blocks cross-domain requests unless allowed | Lab 2C |
| **Event-Driven Architecture** | Code runs in response to events (not on a schedule or continuously) | Lab 2C |
| **IAM User** | An identity with permanent credentials (for systems, not humans) | Lab 3A |
| **IAM Group** | A collection of users that share the same permissions | Lab 3B |
| **IAM Role** | A temporary identity that anyone can "assume" to get specific permissions | Lab 3C |
| **IAM Policy** | A JSON document that defines what actions are allowed or denied | Lab 3A |
| **Inline Policy** | A policy attached directly to one user, group, or role (not reusable) | Lab 3A |
| **Least Privilege** | Give only the minimum permissions needed — nothing more | Lab 3A |
| **Explicit Deny** | A Deny statement in a policy — overrides any Allow, cannot be bypassed | Lab 3B |
| **Implicit Deny** | No policy = denied by default. Can be overridden by adding an Allow | Lab 3B |
| **MFA** | Multi-Factor Authentication — requires password + phone code to log in | Lab 3B |
| **Trust Policy** | Defines WHO is allowed to assume a role | Lab 3C |
| **Assume Role** | Switching to a role's identity to get its temporary credentials | Lab 3C |
| **Session Token** | Extra credential required when using temporary role credentials | Lab 3C |
| **Separation of Duties** | No single person has all the power (developers ≠ auditors ≠ admins) | Lab 3C |
| **Detector** | The GuardDuty resource that represents the service being enabled | Lab 4A |
| **Finding** | A security alert generated by GuardDuty when it detects suspicious activity | Lab 4A |
| **Severity Level** | How urgent a finding is: Low (1-3), Medium (4-6), High (7-8) | Lab 4A |
| **Trail** | A CloudTrail configuration that stores logs permanently in S3 | Lab 4B |
| **Event Pattern** | A JSON filter that tells EventBridge which events to match | Lab 4C |
| **SOAR** | Security Orchestration, Automation, and Response — automating security workflows | Lab 4C |
| **Incident Response** | The process of detecting, containing, and recovering from a security event | Lab 5A |
| **Containment** | Stopping an incident from spreading (revoke credentials, isolate systems) | Lab 5A |
| **Eradication** | Removing the threat completely (delete compromised keys, users, resources) | Lab 5A |
| **Credential Revocation** | Deactivating or deleting access keys to stop unauthorized access | Lab 5A |
| **Forensic Timeline** | A chronological reconstruction of events during an incident | Lab 5B |
| **Incident Report** | A formal document recording what happened, actions taken, and lessons learned | Lab 5B |
| **Automated Remediation** | Using Lambda to automatically fix security issues without human intervention | Lab 5C |
| **Well-Architected Framework** | AWS's set of best-practice pillars for designing sound cloud systems | Lab 6A |
| **Security Pillar** | Well-Architected pillar: protect data, systems, and assets from unauthorized access | Lab 6A |
| **Reliability Pillar** | Well-Architected pillar: workloads perform correctly and recover from failure | Lab 6A |
| **Versioning (S3)** | Keeps multiple copies of a file so you can recover from accidental deletes or overwrites | Lab 6A |
| **Delete Marker** | A placeholder S3 adds when a versioned object is "deleted", allowing recovery | Lab 6A |
| **Operational Excellence Pillar** | Well-Architected pillar: run and monitor systems to keep delivering business value | Lab 6B |
| **Performance Efficiency Pillar** | Well-Architected pillar: use resources efficiently (e.g., right-sizing Lambda memory) | Lab 6B |
| **Cost Optimization Pillar** | Well-Architected pillar: deliver business value at the lowest price point | Lab 6C |
| **Sustainability Pillar** | Well-Architected pillar: minimize the environmental impact of workloads | Lab 6C |
| **AWS Graviton** | AWS's ARM-based (`arm64`) processors — cheaper and more energy-efficient than x86 | Lab 6C |
| **Function Architecture (arm64 vs x86_64)** | The processor type a Lambda runs on: Graviton (ARM) versus Intel/AMD | Lab 6C |
| **Log Retention Policy** | Automatically deletes log entries older than a set number of days (controls cost) | Lab 6C |
| **Cost Projection** | An estimate of future spend based on your current configuration | Lab 6C |
| **Infrastructure as Code (IaC)** | Defining cloud resources in text files instead of clicking in the console | Lab 7A |
| **Declarative vs Imperative** | Declarative describes the desired end state; imperative lists step-by-step commands | Lab 7A |
| **State File** | A JSON record of what IaC has created, used to work out what needs to change next | Lab 7A |
| **Remote State Backend** | Storing the state file in S3 so a whole team shares one infrastructure snapshot | Lab 7A |
| **State Locking** | Using DynamoDB to stop two people changing infrastructure at the same time | Lab 7A |
| **Provider** | A plugin that teaches OpenTofu how to talk to a platform (e.g., the AWS provider) | Lab 7A |
| **Resource** | A single declared piece of infrastructure in code (a bucket, a function) | Lab 7A |
| **Module** | A reusable package of infrastructure code with inputs and outputs | Lab 7A |
| **Default Tags** | Tags automatically applied to every resource the provider creates | Lab 7A |
| **`.tf` / `.tfvars` Files** | HCL configuration files, and the separate files that hold their variable values | Lab 7A |
| **`.gitignore`** | A file listing what Git should never track (state files, secrets, temp folders) | Lab 7A |
| **Commit** | A saved, timestamped snapshot of code changes in Git | Lab 7A |
| **Module Inputs (Variables) / Outputs** | Parameters passed into a module, and the values it returns to the caller | Lab 7C |
| **Cross-Service Permission** | A resource-based policy letting one service (S3) invoke another (Lambda) | Lab 7C |
| **Event Notification** | Config that sends events to a target when something happens (S3 upload → Lambda) | Lab 7C |
| **Force Destroy** | A setting that allows deleting a resource even when it still holds data | Lab 7C |
| **Dependency Ordering** | OpenTofu sequencing create/destroy automatically based on resource relationships | Lab 7C |
| **CI/CD** | Continuous Integration/Delivery — automation that runs build, test, and deploy steps when code is pushed | Lab 8A |
| **OIDC (OpenID Connect)** | A protocol letting one system prove its identity with short-lived tokens instead of stored secrets | Lab 8A |
| **Identity Provider** | An IAM configuration that establishes trust with an external system such as GitHub | Lab 8A |
| **Federated Identity** | Authenticating through an external provider instead of AWS access keys | Lab 8A |
| **Workflow** | A GitHub Actions automation (jobs, steps, triggers) defined in YAML | Lab 8A |
| **Pipeline Role** | An IAM role GitHub Actions assumes, scoped by its trust policy to a specific repository | Lab 8A |
| **Drift** | When real infrastructure no longer matches the code that is supposed to define it | Lab 8C |
| **Drift Detection** | Automatically comparing real infrastructure against code and alerting on mismatches | Lab 8C |
| **Source of Truth** | Treating the configuration code as authoritative — reality must match it | Lab 8C |
| **Out-of-Band Changes** | Changes made outside the pipeline (manual console clicks) that bypass version control | Lab 8C |
| **Scheduled Workflow / `workflow_dispatch`** | Running a GitHub Actions workflow on a cron timer, or manually on demand | Lab 8C |
| **Metric** | A single measurement tracked over time (e.g., error count, duration, invocations) | Lab 9A |
| **Alarm State** | An alarm's current condition: OK (healthy), ALARM (threshold crossed), INSUFFICIENT_DATA (not enough data) | Lab 9A |
| **Log Group** | A CloudWatch Logs container for logs from one source (e.g., `/aws/lambda/<function-name>`) | Lab 9B |
| **Log Stream** | An individual sequence of log events within a log group (one per Lambda execution environment) | Lab 9B |
| **Structured Logging** | Writing logs as JSON for machine-searchability (query by field, level, request ID) | Lab 9B |
| **MTTD (Mean Time to Detect)** | How long between "something broke" and "we know it's broken" — alarms reduce this | Lab 9B |
| **MTTR (Mean Time to Resolve)** | How long between "we know it's broken" and "it's fixed" — good logs reduce this | Lab 9B |
| **Smoke Test** | A quick automated check after deployment that verifies the application runs without crashing | Lab 9C |
| **Shift Left** | Catching problems earlier in the process (in CI, not in production) | Lab 9C |
| **Defence in Depth** | Using multiple complementary safeguards (CI tests + monitoring + alarms) rather than relying on one | Lab 9C |
| **External API Call** | When your code calls another service over the internet to get data (introduces dependency risk) | Lab 10A |
| **Custom Metric** | A CloudWatch metric you define yourself (vs built-in AWS metrics), lives in a custom namespace | Lab 10B |
| **Namespace** | A container for related CloudWatch metrics (e.g., `AWS/Lambda` or your own `WorkshopChatbot`) | Lab 10B |
| **Dependency Monitoring** | Watching the health of services your application depends on, not just your own code | Lab 10B |
| **Graceful Degradation** | System continues to function with reduced features when a component fails, rather than crashing | Lab 10C |
| **Fallback Response** | A pre-prepared answer returned when the primary data source is unavailable | Lab 10C |
| **Circuit Breaker Pattern** | Architecture pattern that stops calling a failing dependency and switches to fallback until it recovers | Lab 10C |
| **Foundation Model** | A large AI model pre-trained on vast data that can answer questions, generate text, and have conversations | Lab 11A |
| **Token** | The unit AI models use to measure text (~1 token ≈ 1 word); charged per input + output token | Lab 11A |
| **Converse API** | Bedrock's unified API for calling any model with a standard message format | Lab 11A |
| **System Prompt** | Invisible instructions controlling AI behaviour (personality, constraints, topic restrictions) | Lab 11B |
| **Prompt Engineering** | The skill of crafting prompts to control AI outputs — quality, length, style, and cost | Lab 11B |
| **Temperature** | AI parameter controlling randomness (0 = deterministic, 1 = creative) | Lab 11B |
| **Token Budget** | A limit enforced in code to reject requests that would consume too many tokens | Lab 11C |
| **Retrieval-Augmented Generation (RAG)** | Fetch relevant documents and inject them into the prompt before the model generates an answer | Lab 12A |
| **Grounding** | Constraining answers to provided facts so the bot cites sources and admits when it doesn't know | Lab 12A |
| **Hallucination** | Confident but false statements from an AI; RAG greatly reduces them | Lab 12A |
| **Chunking** | Splitting documents into smaller passages so only the relevant pieces are retrieved | Lab 12A |
| **Retrieval** | Finding the document chunks most relevant to a user's query | Lab 12A |
| **Vector Embeddings** | Numerical representations of meaning that enable similarity-based search (beats keyword matching) | Lab 12A |
| **Citation / Attribution** | Showing the source document behind each fact, for trust and auditability | Lab 12A |
| **Cold Start** | The slower first Lambda invocation after deployment; later "warm" calls reuse cached resources | Lab 12A |
| **RAG vs Fine-Tuning** | RAG is cheaper and updates instantly; fine-tuning bakes knowledge in at higher cost and slower to change | Lab 12A |
| **Responsible AI** | Designing for safety, fairness, privacy, and transparency through enforceable, auditable controls | Lab 12B |
| **Content Filters** | Bedrock detection categories (Hate, Insults, Sexual, Violence, Misconduct, Prompt Attacks) with adjustable strength | Lab 12B |
| **Denied Topics** | Administrator-defined off-limits subjects, blocked by meaning rather than keywords | Lab 12B |
| **PII Protection** | Detecting and blocking personal data (credit cards, SSNs) before the model processes it | Lab 12B |
| **Prompt Injection / Jailbreaking** | Attempts to override system instructions via user input; guardrails block them independently of the prompt | Lab 12B |
| **Guardrail Versioning** | Immutable, numbered guardrail releases pinned in production so changes don't silently affect live systems | Lab 12B |
| **Defence in Depth (Cost)** | Layered cost controls: input validation → per-request token alarm → account-level budget | Lab 12C |
| **Account-Level Cost Governance** | Organization-wide spending limits, independent of application-layer guardrails, as a final safeguard | Lab 12C |
| **Production-Readiness Checklist** | A systematic review of functionality, controllability, grounding, safety, cost control, observability, and least privilege | Lab 12C |

---

## Common CLI Commands Reference

### AWS CLI (`aws`)

| Command | What It Does |
|---------|-------------|
| `aws sts get-caller-identity` | Shows who you are currently authenticated as |
| `aws sso login --profile <NAME>` | Logs you in via SSO (opens browser) |
| `aws s3 mb s3://<BUCKET>` | Creates an S3 bucket |
| `aws s3 cp <SOURCE> <DEST>` | Copies files to/from S3 |
| `aws s3 rm s3://<BUCKET> --recursive` | Deletes all files in a bucket |
| `aws s3 rb s3://<BUCKET>` | Deletes an empty bucket |
| `aws s3api put-bucket-policy` | Attaches a bucket policy to an S3 bucket |
| `aws s3api delete-bucket-policy` | Removes a bucket policy |
| `aws s3api put-bucket-versioning` | Enables (or suspends) versioning on a bucket |
| `aws s3api get-bucket-versioning` | Shows a bucket's versioning status |
| `aws s3api list-object-versions` | Lists all object versions in a versioned bucket |
| `aws s3api put-public-access-block` | Turns on Block Public Access for a bucket |
| `aws iam create-user --user-name <NAME>` | Creates an IAM user |
| `aws iam create-role --role-name <NAME>` | Creates an IAM role |
| `aws iam attach-role-policy` | Attaches a managed policy to a role |
| `aws iam detach-role-policy` | Detaches a managed policy from a role |
| `aws iam put-role-policy` | Attaches an inline policy to a role |
| `aws iam delete-role-policy` | Removes an inline policy from a role |
| `aws iam delete-role` | Deletes an IAM role |
| `aws iam put-user-policy` | Attaches an inline policy to a user |
| `aws iam create-access-key` | Creates access keys for a user |
| `aws iam update-access-key --status Inactive` | Deactivates an access key |
| `aws iam delete-access-key` | Permanently deletes an access key |
| `aws iam create-open-id-connect-provider` | Registers an external OIDC issuer (e.g., GitHub) as trusted |
| `aws iam delete-open-id-connect-provider` | Removes an OIDC identity provider |
| `aws ec2 run-instances` | Launches an EC2 instance |
| `aws ec2 terminate-instances` | Terminates (deletes) an EC2 instance |
| `aws lambda create-function` | Creates a Lambda function |
| `aws lambda invoke` | Manually runs a Lambda function |
| `aws lambda update-function-code` | Updates a Lambda function's code (redeploy) |
| `aws lambda update-function-configuration` | Updates a function's settings (env vars, memory, architecture) |
| `aws lambda get-function-configuration` | Retrieves a function's configuration |
| `aws lambda create-function-url-config` | Creates a public HTTP endpoint for a Lambda function |
| `aws lambda delete-function` | Deletes a Lambda function |
| `aws guardduty create-detector --enable` | Enables GuardDuty |
| `aws guardduty delete-detector` | Disables GuardDuty |
| `aws cloudtrail lookup-events` | Searches CloudTrail event history |
| `aws events put-rule` | Creates an EventBridge rule |
| `aws events put-targets` | Adds a target to an EventBridge rule |
| `aws cloudwatch get-metric-statistics` | Retrieves metric data points for a specified period |
| `aws cloudwatch put-metric-data` | Publishes a custom metric to CloudWatch |
| `aws cloudwatch put-metric-alarm` | Creates or updates a CloudWatch alarm |
| `aws cloudwatch describe-alarms` | Shows alarm state (OK, ALARM, INSUFFICIENT_DATA) |
| `aws cloudwatch delete-alarms` | Deletes one or more alarms |
| `aws cloudwatch put-dashboard` | Creates or updates a CloudWatch dashboard |
| `aws cloudwatch delete-dashboards` | Deletes a CloudWatch dashboard |
| `aws logs describe-log-groups` | Lists CloudWatch Log groups |
| `aws logs describe-log-streams` | Lists log streams within a log group |
| `aws logs get-log-events` | Reads log entries from a specific stream |
| `aws logs filter-log-events` | Searches log events across a log group |
| `aws logs start-query` | Starts a CloudWatch Logs Insights query |
| `aws logs get-query-results` | Gets results from a Logs Insights query |
| `aws logs put-metric-filter` | Creates a metric filter that extracts custom metrics from log data |
| `aws logs delete-metric-filter` | Deletes a metric filter |
| `aws logs put-retention-policy` | Sets automatic log deletion after N days |
| `aws logs delete-log-group` | Deletes a CloudWatch log group |
| `aws ssm put-parameter` | Creates or updates a parameter in SSM Parameter Store |
| `aws ssm get-parameter` | Reads a parameter value from SSM Parameter Store |
| `aws ssm delete-parameter` | Deletes a parameter from SSM Parameter Store |
| `aws bedrock-runtime converse` | Calls a Bedrock AI model with messages and returns a generated response |
| `aws bedrock create-guardrail` | Creates a Bedrock guardrail (content filters, denied topics, PII, etc.) |
| `aws bedrock create-guardrail-version` | Publishes an immutable, numbered version of a guardrail |
| `aws bedrock list-guardrails` | Lists guardrails in the account |
| `aws bedrock delete-guardrail` | Deletes a guardrail |
| `aws apigateway create-rest-api` | Creates a new API Gateway REST API |
| `aws apigateway create-resource` | Adds a path resource (e.g., /chat) to an API |
| `aws apigateway put-method` | Adds an HTTP method (GET, POST) to an API resource |
| `aws apigateway put-integration` | Connects an API method to a backend (Lambda) |
| `aws apigateway create-deployment` | Deploys an API to a stage (makes it live) |
| `aws apigateway get-rest-apis` | Lists REST APIs (to find one's ID) |
| `aws apigateway delete-rest-api` | Deletes an API Gateway REST API |
| `aws sns create-topic` | Creates an SNS notification topic |
| `aws sns subscribe` | Subscribes an email/endpoint to a topic |
| `aws dynamodb create-table` | Creates a DynamoDB table (used for state locking) |
| `aws dynamodb describe-table` | Checks a table's status |
| `aws dynamodb list-tables` | Lists all DynamoDB tables |
| `aws dynamodb delete-table` | Deletes a DynamoDB table |
| `aws cloudfront create-origin-access-control` | Creates an Origin Access Control for private S3 access |
| `aws cloudfront create-distribution` | Creates a CloudFront distribution |
| `aws cloudfront list-distributions` | Lists CloudFront distributions |
| `aws cloudfront get-distribution` / `get-distribution-config` | Shows a distribution's status / editable config |
| `aws cloudfront update-distribution` | Updates a distribution (e.g., to disable before deleting) |
| `aws cloudfront create-invalidation` | Clears cached files so updates serve immediately |
| `aws cloudfront delete-distribution` | Deletes a (disabled) CloudFront distribution |
| `aws budgets create-budget` | Creates an account budget with spending limits and alerts |
| `aws budgets describe-budget` | Retrieves a budget's configuration |
| `aws budgets delete-budget` | Deletes a budget |

### OpenTofu (`tofu`)

| Command | What It Does |
|---------|-------------|
| `tofu --version` | Shows the installed OpenTofu version |
| `tofu init` | Initializes the working directory, downloads providers, loads modules and backend |
| `tofu plan` | Previews the infrastructure changes without applying them |
| `tofu plan -detailed-exitcode` | Plan mode used for drift detection (exit 0 = no changes, 2 = drift, 1 = error) |
| `tofu apply` | Applies the planned changes and creates/updates resources |
| `tofu destroy` | Removes all infrastructure OpenTofu manages |
| `tofu force-unlock <LOCK_ID>` | Manually releases a stale state lock |

### Git (`git`)

| Command | What It Does |
|---------|-------------|
| `git init` | Initializes a new Git repository |
| `git config` | Sets your identity (name/email) for commits |
| `git status` | Shows untracked and staged files |
| `git add .` | Stages all changed files for the next commit |
| `git commit -m "<message>"` | Saves a snapshot with a description |
| `git log --oneline` | Displays the commit history |

---

*This glossary is updated as new sessions are added.*
