# Projects, Scaffold, and Budgets
### AI Systems Engineering — Fall 2026 (Draft v3)

Four cumulative pair projects on one scaffolded system. This document is the
specification for the scaffold, each project, the reference-solution rule, and the
quota and cost budgets that make it all fit in an AWS Academy Learner Lab budget and the
Gemini free tier at 5 hours a week.

---

## 1. The system: the Operator

A support-ticket triage and resolution agent. It ingests a stream of tickets, retrieves
from a small runbook/knowledge base, and calls tools that do consequential things: look
up an account, issue a refund up to a limit, escalate to a human. It runs live for the
back half of the semester under a cost budget.

The domain is chosen deliberately: ticket text is **untrusted user content flowing
straight into the context**; refunds are **real consequences requiring an approval
gate**; resolution quality is **evaluable**; and the whole thing is close enough to real
work that you can carry it back to your job.

## 2. The scaffold

You receive the whole system **working** in Week 1. It is deliberately naïve — every
AI-specific part is the simplest thing that runs — and every part is commented the way
last year's agent lab was. You do not build it in order; you **replace parts** in order.

### 2.1 Components

| Component | What it is | Who owns it by Week 13 |
|---|---|---|
| **Ticket simulator** | Generates a stream of tickets from seed data: intents, account IDs, amounts, a few adversarial ones. Deterministic under a seed. Drift and injection can be switched on. | Instructor |
| **Runbook corpus + tiny retriever** | ~50 runbook/policy documents and a simple hybrid search. | Instructor (you tune it in P1 slices; optional stretch to replace) |
| **Capability server** | Exposes `lookup_account`, `issue_refund(amount ≤ limit)`, `escalate` over the course's capability protocol (HTTP transport). Enforces per-task scopes. There are **no in-process tools**; the agent has always talked to this server. | Instructor |
| **"Vendor" capability server** | A second server, appears Week 11: a knowledge-search capability "from a third party." Its descriptions and results are the Week 12 injection vector. | Instructor |
| **Naïve agent loop** | ~100 lines: render descriptions into context, sample, parse, dispatch, append, repeat; a step limit; nothing else. | **You (P3)** |
| **Orchestrator** | Runs the loop as a sequence of function invocations; holds run state in a key-value store; no checkpoint discipline; retries blindly. | **You (P3)** |
| **LLM provider** | Thin client for the free-tier model family, with a **record/replay mode**: every live call is recorded to a fixture; replay serves fixtures deterministically. Quota counter built in. | Instructor |
| **Eval harness (stub)** | 10 items, one aggregate accuracy, no slices, no noise floor, a judge that has never been validated. | **You (P1)** |
| **Deployment** | Functions + queue + key-value store + object storage, deployed by one script. A container image exists for local runs. No release gate; no canary; no kill switch. | **You (P2)** |
| **Trace logger** | Logs tool calls. Does not log the context at each step. (Deliberately.) | **You (P2, extended in P4)** |
| **Usage alarm template** | One-click CloudWatch alarms on the usage that drives cost — function invocations and duration, table capacity, queue requests — with thresholds that trip at roughly $10 and $25 of budget at on-demand prices. | You set it in Week 1's lab |
| **Dev container** | `.devcontainer/` on the same image as local runs; opened by VS Code locally or by GitHub Codespaces. See §2.3. | Instructor |

### 2.2 Infrastructure (AWS Academy Learner Lab)

Each student gets an **AWS Academy Learner Lab** account for the course: a real AWS
account behind a lab page, with a **$50 budget for the semester**, no card, and no bill
to you. The scaffold uses only services with always-free allowances; the budget is a
safety margin, not a budget to spend. Most students should finish the semester having
used a few dollars of it.

| Need | Service | Why it is on-thesis |
|---|---|---|
| Agent step / API | Functions (+ function URL) | The 15-minute invocation limit *is* process death; checkpointing is not optional |
| Ticket queue, backpressure | Queue service | Week 7's queue-and-backpressure on real infrastructure |
| Run state, checkpoints, approval gate | Key-value store | A refund waiting for approval is a row, not a thread |
| Fixtures, traces, corpus snapshot | Object storage | "What is in the build" has a bucket |
| Logs, metrics, alarms | Monitoring service | Week 13 game day; the cost alarm is the first thing you configure |

**Rules.** No EC2 instances. No NAT gateways, load balancers, or managed Kubernetes.
Delete what you are not using. If a usage alarm fires, stop and ask.

**How Learner Lab differs from a normal account.** These are the things that will
otherwise cost you an evening:

- **The budget is hard.** When the $50 is gone, AWS Academy deactivates the lab and
  your deployed resources go with it. The budget meter on the lab page is the source of
  truth, and it updates on a lag of several hours — which is why the scaffold ships
  alarms on *usage* (invocations, capacity, queue requests) rather than on dollars: the
  billing console and billing metrics are not exposed inside Learner Lab.
- **Sessions are timed.** You start a session from the lab page; it runs for a few hours
  and then stops. Serverless resources — functions, queues, tables, buckets, alarms —
  persist across sessions. Credentials do not (see §2.3), and anything that only lived
  in the session does not.
- **IAM is fixed.** You cannot create roles or users. Every function runs as the
  pre-created lab role, and the deploy script is written for it. If a tutorial tells you
  to create a role, it is not for this environment.
- **Regions are limited.** Deploy to `us-east-1`. The deploy script sets this; do not
  override it.
- **Accept the invitation in Week 1.** The lab is created by the instructor; you receive
  an email invitation to AWS Academy before the first class. The Week 1 lab assumes you
  have already accepted it and can open the lab page.

> **Instructor checklist, before Week 1:** the lab is provisioned and invitations are
> out; the scaffold deploys cleanly under the lab role in `us-east-1`; the usage alarm
> template creates without touching billing; the budget meter shows $50. Confirm the
> always-free limits have not moved.

### 2.3 Development environment

Every environment below runs the same thing: the scaffold's container image locally,
and the deploy script against the course AWS account. Pick the one that matches the
machine you actually have. None of them spends AWS credit — development is local or on
GitHub; AWS is only touched by the deploy script and the running system.

**What every setup needs.** Git; Python 3.12+; Docker (to run the container image);
the AWS CLI v2; an editor — VS Code is assumed in the labs because the dev container
and the Codespaces path are built on it, but anything works. Verify with:

```
git --version && python3 --version && docker --version && aws --version
```

| Environment | Setup | Notes |
|---|---|---|
| **Windows** | Install **WSL2 with Ubuntu** and do everything inside it: Git, Python, and the AWS CLI via `apt`; **Docker Desktop** with the WSL2 backend; VS Code on Windows with the *WSL* extension, opened from the Ubuntu shell (`code .`). | Do not use native Windows Python or PowerShell for the scaffold — path and line-ending differences are the first thing that will break. Clone the repo inside the WSL filesystem (`~/`), not under `/mnt/c/`. Set `git config --global core.autocrlf input`. |
| **macOS** | **Homebrew**, then `brew install git python awscli`; **Docker Desktop** (or Colima / OrbStack if you prefer); VS Code with the *Dev Containers* extension. | Apple Silicon is fine: the scaffold image is built for both `arm64` and `amd64`, and the deploy target is set by the deploy script, not by your laptop. |
| **Linux** | Distro packages for Git and Python; **Docker Engine** (add yourself to the `docker` group and log back in); AWS CLI v2 from the AWS installer; VS Code with the *Dev Containers* extension. | The least setup of the four. If your distro's Python is older than 3.12, use `uv` or `pyenv` rather than replacing the system Python. |
| **Cloud: GitHub Codespaces** | Open the scaffold repository in a Codespace. The `.devcontainer/` definition installs everything above; nothing to install on your machine. Works from any browser, including a locked-down work laptop. | The GitHub free tier gives an individual account about 60 hours a month on the smallest machine — more than the course's 3.5 project hours a week. Codespaces stop after 30 minutes idle by default and are deleted after 30 days of inactivity; commit and push before you walk away. This is the path if you cannot install Docker where you are. |
| **Cloud: AWS CloudShell** | Nothing to install. Open CloudShell from the AWS console inside a lab session; it has the AWS CLI and Python preinstalled and a 1 GB persistent home directory. | **For running the deploy script and poking at deployed resources, not for development** — no Docker, no editor to speak of, and the shell times out after roughly 20 minutes idle. Free. |

**Not an option: AWS Cloud9.** AWS stopped offering Cloud9 to new customers in 2024 and
it is no longer available in Learner Lab. It also ran on an EC2 instance, which the
rules above forbid. If you want a browser IDE, use Codespaces.

**Credentials.** Two secrets, handled the same way in every environment:

- **AWS.** The course runs on **AWS Academy Learner Lab**. Keys are issued *per lab
  session* — start the lab, open *AWS Details*, and paste the block into
  `~/.aws/credentials` (in a Codespace, into the Codespace's own file, or set them as
  Codespace secrets). They **expire when the session ends**, typically a few hours; a
  deploy that suddenly returns `ExpiredToken` or `InvalidClientTokenId` means start a new
  session and paste again, not debug the script. The deploy script runs
  `aws sts get-caller-identity` first and stops with a plain-language message when the
  keys are dead. Deployed resources — functions, queues, tables, buckets — persist
  between sessions; only the keys and any running session state do not.
- **Gemini.** One key per student, in the `GEMINI_API_KEY` environment variable or a
  `.env` file that is already in `.gitignore`. Never in code, never in a fixture, never
  in a commit. The provider's record/replay mode (§2.4) means most of your runs do not
  need the key at all.

If you are on a personal free-plan account instead of Learner Lab, the only difference
is that your keys are long-lived; everything else in this section applies unchanged.

### 2.4 Model access (Gemini free tier)

- **One API key per student**, not per pair. This doubles the pair's budget and gives the
  cheap-model / expensive-model routing lab real teeth.
- The free tier is rate-limited per minute and capped per day, with no SLA. The numbers
  change; verify at semester start. Design assumption: ~15 requests/minute on the
  cheapest model and a daily cap in the low thousands.
- **Record/replay is how you stay inside the budget.** Reruns replay fixtures. CI gates
  run on fixtures plus a small live smoke suite. Trajectory harnesses are small and run
  overnight.

### 2.5 The reference-solution rule

Each project is released with the instructor's reference solution to the previous
project. If your P(n−1) is shaky, build P(n) on the reference. This is not a penalty
and does not affect your P(n−1) grade. Nobody cascades.

---

## 3. The projects

Each project has: what you replace, what you deliver, a one-page design doc, an hour
target, a quota budget, and the rubric emphasis. Pairs. Repositories are tagged at each
deadline.

### P1 — Measure it
**Assigned Week 1 · Due Week 4 · ~12 hours · Weight 10%**

*The scaffold "works." Prove it, per slice, with a noise floor — and say what you cannot see.*

**You replace:** the eval harness stub.

**Deliver:**
1. A **golden set** of 50–100 tickets with expected resolutions, sourced from the
   simulator plus hand-authored adversarial and edge items. Coverage mapped to the
   Week 2 mitigation table.
2. **Slices**: at minimum by intent, by amount band, by whether the ticket contains an
   instruction to the agent, and one slice of your choosing that you expect to fail.
3. **Scorers** appropriate to each output type: exact match for the action taken;
   a rubric-based judge for resolution text.
4. **A validated judge**: agreement with your own human labels on ≥30 items, reported
   per slice; the judge's failure modes you found.
5. **The noise floor**: the same system run 5× on the suite; report the spread and the
   effect size you can detect.
6. **The blind-spot register**: what this harness cannot see. Written, honest, short.
7. **Design doc (1 page):** the seven-step brief for the Operator — you have the
   vocabulary from Weeks 1–3.

**Quota budget:** ~700 live calls (100 items × 5 runs + judging), recorded as fixtures.
Fits one day of a single key; two keys make it comfortable.

**Rubric emphasis:** slices that expose what the aggregate hides; a judge you validated
rather than trusted; an honest blind-spot register. Coverage over size.

---

### P2 — Ship it
**Assigned Week 4 · Due Week 7 · ~12 hours · Weight 10%**

*A change to the prompt is a deploy. Prove that no deploy regresses beyond the noise floor,
and that you can turn it off.*

**You replace:** the deployment's release path and the trace logger.

**Deliver:**
1. **A release gate in CI**: the P1 harness runs on **replay fixtures** on every change,
   plus a **live smoke suite** of ~10 items. The gate fails when any slice regresses
   beyond the noise floor at your stated confidence. Show one deliberately bad prompt
   change being blocked.
2. **A canary**: a weighted split between two versions of the build — prompt, model
   identifier, corpus snapshot — with a **kill switch** that reverts in one action.
   Show it working.
3. **A build manifest**: the six components of "the build" pinned in one file; the
   rollback procedure names a combination, not a commit.
4. **Telemetry**: the trace logger now captures the **context at each step** (or a
   reference to it), the parse, the dispatch, the result, cost, latency, and model
   version, with a join key per run. Redaction policy stated.
5. **A cost model**: cost per resolution at 1M tickets/month under three model-routing
   choices; the dominant term identified.
6. **Design doc (1 page):** the rollout design — what you watch during a canary, what
   triggers the kill switch, what "rollback" rolls back to.

**Quota budget:** the gate runs on fixtures; the live smoke suite is ~10 calls per push.
Budget ~200 live calls for the canary demonstration.

**Rubric emphasis:** the gate actually blocks a regression; the kill switch is one
action; the manifest names everything in the build.

---

### P3 — Make it act
**Assigned Week 8 · Due Week 11 · ~12 hours · Weight 10%**

*Replace the naïve loop with an orchestrator that survives process death, issues a refund
exactly once, and never sees a credential. No frameworks.*

**You replace:** the agent loop and the orchestrator.

**Deliver:**
1. **Your loop**, from scratch: discover the capability server's tools at startup,
   render descriptions into context, sample, **validate at the boundary**, dispatch
   over the wire, append results marked as untrusted, repeat. Roughly 200 lines.
2. **Your orchestrator** across function invocations: run state and **checkpoints** in
   the key-value store, written *before* dispatch; **idempotency keys** on every
   consequential action; resume after a killed invocation without re-issuing a refund.
   Show a run killed mid-refund that resumes correctly.
3. **Budgets** as safety mechanisms: step limit, token/cost limit, wall-clock limit,
   and a progress check. Show each one halting a run.
4. **The approval gate**: a refund above the limit suspends the run as a row; a human
   approves through a minimal endpoint; the run resumes. Consent at the moment of
   consequence.
5. **Scoped authority**: the client attaches a per-task token; the server refuses an
   out-of-scope action. **No credential ever appears in a context or a trace** — show
   the trace to prove it.
6. **Context management**: a compaction policy with a stated eviction order that
   preserves the refund limit and the ticket's constraints. Evaluated (see 7).
7. **A trajectory harness**: 15 tickets in a seeded sandbox; trajectory metrics (steps,
   tool errors, cost, escalations, policy violations); a trajectory judge validated on
   10 human-reviewed runs; **policy-violation rate at zero** as a gate; cost reported
   as a distribution, not a mean.
8. **Design doc (1 page):** the integration and authorization design — the six
   problems, and what the three parties are in your system.

**Prohibited:** agent frameworks. Permitted and encouraged: AI assistance, the
scaffold's provider client, the protocol client library.

**Quota budget:** trajectories are expensive — 15 tickets × ~25 steps ≈ 375 calls per
harness run. Plan for ~4 full runs over the three weeks, overnight, recorded. Iterate on
single tickets in replay.

**Rubric emphasis:** exactly-once refund across a crash; budgets that actually halt;
credentials provably absent from context; a compaction policy with an eval.

---

### P4 — Break it, run it
**Assigned Week 11 · Due Week 13 (repo tagged) · ~12 hours · Weight 10%**

*Someone attacked it. Something drifted. Nothing was deployed. Find it, contain it, write it up.*

**You replace/add:** hardening, SLOs, and the operations layer.

**Deliver:**
1. **Red-team findings and fixes**: the Week 12 attacks on your system (via ticket and
   via the vendor server), what succeeded, what you changed, and what you *could not*
   fix — with the containment that bounds it instead. Attack items added to the
   harness.
2. **Containment**: read-only default; consequence tiers per tool; the refund cap;
   untrusted-content marking; egress limited to the capability servers. State the
   attacker's maximum payoff in dollars.
3. **SLOs**: availability, latency by task type, **quality** (via continuous evaluation
   on production samples against the noise floor), and **cost per resolution**. A
   **degradation ladder** ending in escalate-everything.
4. **Vendor-drift canary**: a pinned suite on a schedule; a detected change treated as an
   unplanned deploy.
5. **The game-day postmortem**: the Week 13 incident — detection, triage by layer,
   mitigation, root cause with no diff, action items, and the blind-spot register
   updated.
6. **Design doc (1 page):** the threat model — assets, entry points, the lethal
   combination and which leg you removed.
7. **Demo backup** (optional): a 5-minute recording off the tag.

**Quota budget:** ~300 live calls (red-team reruns + canary suite). Everything else on
fixtures.

**Rubric emphasis:** honest accounting of what could not be fixed; a quality SLO that
would actually have fired; a postmortem whose root cause is a distribution shift.

---

## 4. Hour budget across the semester

| Weeks | Reading | Project | Total/wk |
|---|---|---|---|
| 1–4 (P1) | ~1.5 | ~3 | ~4.5 |
| 5–7 (P2) | ~1.5 | ~4 | ~5.5 |
| 8–11 (P3) | ~1.5 | ~3 | ~4.5 |
| 12–13 (P4) | ~1 | ~6 | ~7 |
| 14 | 0 | 0 (demo off the tag) | 0 |

Weeks 12–13 run hot by design — P4 is mostly reacting to the red team and the game day,
which happen in class. Everything else sits at or under the 5-hour cap. If you are
consistently over, report it; that is a bug in the course.

## 5. What the demo is

Six minutes, live, off the frozen P4 tag: one ticket end to end, one trace walked
through, three minutes of questions on a design decision and the postmortem. No new
work. The demo is where the course verifies that you can explain what you built.
