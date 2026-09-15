# Configuring AWS

The course runs on an **AWS Academy Learner Lab** account: a real AWS account behind a
lab page, with a $50 budget for the semester, no card, and no bill to you. This page is
how to get into it, how to keep it from spending the budget, and how to use it for
development without touching the budget at all.

Everything the scaffold deploys uses services with always-free allowances. The $50 is a
safety margin, not a budget to spend. Most students finish the semester having used a
few dollars of it.

## 1. Before the first block: accept the invitation

The instructor creates the lab; you receive an email invitation to AWS Academy before
the first class. Accept it, create your AWS Academy login, and confirm you can open the
course and see the lab page. The Week 1 lab assumes this is already done.

## 2. Starting a session

1. Open the lab page and click **Start Lab**. The indicator turns green when the
   session is ready; it takes about a minute.
2. Click **AWS** (the green link) to open the console, or **AWS Details** to see
   credentials for the command line.
3. Sessions are timed, typically four hours, and stop on their own. **Deployed
   resources persist across sessions**: functions, queues, tables, buckets, and alarms
   are all still there next time. Credentials and anything that only lived in the
   session do not.

The **budget meter** on the lab page is the source of truth for what you have spent.
It updates on a lag of several hours, which is why Section 4 alarms on *usage*
rather than on dollars.

## 3. Credentials for the command line

Every session issues fresh keys. From the lab page, open **AWS Details**, then **Show**
next to *AWS CLI*, and paste the whole block into `~/.aws/credentials`:

```ini
[default]
aws_access_key_id=...
aws_secret_access_key=...
aws_session_token=...
```

Then confirm they work:

```bash
aws sts get-caller-identity
```

Three things that will otherwise cost you an evening:

- **Keys expire when the session ends.** A deploy that suddenly fails with
  `ExpiredToken` or `InvalidClientTokenId` means start a new session and paste again,
  not debug the script.
- **Region is `us-east-1`.** The deploy script sets it. Do not override it, and if the
  console shows a different region, switch it.
- **IAM is fixed.** You cannot create roles or users. Every function runs as the
  pre-created lab role, and the deploy script is written for it. If a tutorial tells
  you to create a role, it is not for this environment.

## 4. Set the usage alarms first

Cost per request is a requirement in this course, and a Learner Lab with no alarms has
no signal until the budget is gone, and the lab with it. You configure the alarms in
the Week 1 lab, before anything is deployed, and keep the screenshot; the homework
asks for it.

**Why usage, not dollars.** Billing metrics and the billing console are not exposed
inside Learner Lab. The only thing you can alarm on is the usage that drives cost:
function invocations and duration, table capacity, queue requests.

**What to alarm on.** The scaffold ships a one-click template with thresholds
calibrated to trip at roughly $10 and $25 of budget at on-demand prices. Until you have
it, create these by hand. The thresholds below sit well inside the always-free
allowances, so a trip means something is looping, not that you are working hard.

| Service | Metric | Statistic, period | Warn at | Stop at |
|---|---|---|---|---|
| Lambda | `Invocations` | Sum, 1 day | 30,000 | 100,000 |
| Lambda | `Duration` | Sum, 1 day | 2,000,000 ms | 6,000,000 ms |
| DynamoDB | `ConsumedWriteCapacityUnits` | Sum, 1 day | 200,000 | 600,000 |
| SQS | `NumberOfMessagesSent` | Sum, 1 day | 100,000 | 300,000 |

**How, in the console.**

1. Console → **CloudWatch** → **Alarms** → **Create alarm**.
2. **Select metric** → pick the service (for example *Lambda* → *Across all
   functions* → `Invocations`).
3. Set **Statistic** to *Sum* and **Period** to *1 day*.
4. **Threshold**: *Greater than* the warn value from the table.
5. **Notification**: create a new SNS topic with your email address, then confirm the
   subscription from the email that arrives. Without the confirmation the alarm fires
   into nothing.
6. Name it (`usage-lambda-invocations-warn`), create it, and repeat for the stop
   threshold and the other rows.

**How, from the command line**, once your credentials are pasted. This creates the
topic, subscribes your email, and makes one alarm; the others follow the same shape.

```bash
TOPIC=$(aws sns create-topic --name usage-alarms --query TopicArn --output text)
aws sns subscribe --topic-arn "$TOPIC" --protocol email --notification-endpoint you@example.com
aws cloudwatch put-metric-alarm \
  --alarm-name usage-lambda-invocations-warn \
  --namespace AWS/Lambda --metric-name Invocations \
  --statistic Sum --period 86400 --evaluation-periods 1 \
  --threshold 30000 --comparison-operator GreaterThanThreshold \
  --alarm-actions "$TOPIC"
```

**When an alarm fires:** stop, look at what is running, and ask. The rules below are
the reason it should never get past the warn level.

## 5. The rules

- **No EC2 instances. No NAT gateways, load balancers, or managed Kubernetes.** None
  are needed, and each can consume the $50 in days.
- **Delete what you are not using.** The scaffold's teardown script removes everything
  it deployed.
- **If a usage alarm fires, stop and ask.**
- **When the budget is gone, the lab is deactivated** and your deployed resources go
  with it. There is no appeal to AWS. Do not find out.

## 6. Using it for development

The short version: **development does not touch AWS.** You develop and run the system
locally, or in a browser IDE, and only the deploy script and the running system use the
account. This is what keeps the budget untouched for most of the semester.

**Where you develop.** Pick the one that matches the machine you actually have; every
option runs the same container image and the same deploy script.

| Environment | What you install | Notes |
|---|---|---|
| **Windows** | WSL2 with Ubuntu, and do everything inside it: Git, Python, AWS CLI via `apt`; Docker Desktop with the WSL2 backend; VS Code with the *WSL* extension. | Clone inside the WSL filesystem (`~/`), not under `/mnt/c/`. Set `git config --global core.autocrlf input`. |
| **macOS** | Homebrew, then `brew install git python awscli`; Docker Desktop (or Colima / OrbStack); VS Code with *Dev Containers*. | Apple Silicon is fine; the image is built for both architectures. |
| **Linux** | Distro packages for Git and Python; Docker Engine; AWS CLI v2 from the AWS installer; VS Code with *Dev Containers*. | Least setup of the four. |
| **GitHub Codespaces** | Nothing. Open the scaffold repository in a Codespace; the dev container installs everything. | The path if you cannot install Docker where you are. Commit and push before you walk away; Codespaces stop after 30 minutes idle. |

Verify with:

```bash
git --version && python3 --version && docker --version && aws --version
```

**AWS CloudShell** is for poking at deployed resources and running the deploy script
from inside a lab session, not for development: no Docker, no real editor, and it
times out after about 20 minutes idle. **AWS Cloud9 is not an option**; it is no longer
offered in Learner Lab and ran on an EC2 instance anyway.

**The development loop.**

1. Start a lab session and paste the credentials (Section 3). This is only needed when
   you are going to deploy or inspect something deployed.
2. Run and test locally in the container. The LLM provider's record/replay mode means
   most runs do not even need your Gemini key.
3. Deploy with the scaffold's deploy script. It runs `aws sts get-caller-identity`
   first and stops with a plain message if the keys are dead.
4. Check the alarms and the budget meter occasionally. If either moves faster than you
   expect, stop and ask.
5. When you are done with a deployed version, tear it down, or leave it: idle
   serverless resources cost nothing, and they persist across sessions.

**Two secrets, handled the same way everywhere.** AWS keys go in `~/.aws/credentials`
(or Codespace secrets) and expire with the session. Your Gemini key goes in
`GEMINI_API_KEY` or a `.env` file that is already in `.gitignore`. Never in code, never
in a fixture, never in a commit.

## Instructor checklist, before Week 1

The lab is provisioned and invitations are out; the scaffold deploys cleanly under the
lab role in `us-east-1`; the usage alarm template creates without touching billing; the
budget meter shows $50. Confirm the always-free limits have not moved.
