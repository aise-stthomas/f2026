# AI Systems Engineering
### University of St. Thomas — Fall 2026

---

## Course description

Most AI projects fail somewhere between the demo and the second month in production.
Not because the model was bad — because the system around it was built on assumptions
that classic software engineering takes for granted and AI systems violate.

This course is about the system, not the model. Its subject is what changes when a
component is stochastic rather than merely buggy, when the specification lives in data
rather than code, when the system decays without anyone editing it, and when the
component takes actions in the world on your behalf.

You will measure, ship, make agentic, break, secure, and operate one live system over
the semester — a support-ticket triage and resolution agent — replacing its parts with
your own as the course teaches them. You will also learn to design an AI system out
loud, under time pressure, against an explicit rubric: the skill a design review
demands, and the one a design interview measures.

The governing constraint on this syllabus is in [course-thesis.md](supplemental/course-thesis.md): **every topic must
answer what is different about it because there is a model in the loop.** Topics that
cannot are prerequisites, not lectures.

## Who this course is for

Working professionals in a master's program, hybrid: roughly half in the room, half
online, in the same 3-hour block. The course assumes you have a job where some of this
will be useful on Monday, and that your time is genuinely scarce.

**Prerequisites:** comfortable Python; command line; have trained or called a model
before; can read an HTTP API. **Not required:** software engineering background,
distributed systems background, or any cloud, container, or orchestration experience —
the Systems Toolkit primers close those gaps, and the project scaffold supplies the
infrastructure.

**Workload cap: 5 hours per week outside class.** About 1.5 hours of reading and 3.5
hours of project work on average. Projects are sized to this and the hour targets are
published with each spec. If you are consistently over, that is a bug in the course;
report it.

**Cost to you: $0.** The projects run on an AWS Academy Learner Lab account, provided
by the course with a $50 semester budget and no card, and the Gemini free tier. See
[projects.md](https://aise-stthomas.github.io/projects) for the account setup and the budget rules.

## Learning outcomes

By the end of this course you will be able to:

1. **Design** an AI system end to end against explicit requirements, and defend the
   design in a review — including passing an AI system design interview.
2. **Reason about stochastic components** as a distribution rather than a defect, and
   build systems that remain correct while a component is wrong.
3. **Build an evaluation harness** with slices, a measured noise floor, and a validated
   judge — for single-shot models *and* for multi-step agent trajectories — and gate a
   release on it.
4. **Treat data and context as engineering artifacts** with contracts, provenance, and
   consistency guarantees.
5. **Explain and implement the agent mechanism from scratch** — the loop, tool dispatch
   over a real capability protocol, context management, durable execution — without a
   framework.
6. **Reason about the integration and trust layer**: discovery, description, invocation,
   versioning, and three-party authorization — with current protocols as instances, not
   the subject.
7. **Threat-model and harden an agentic system**, including prompt injection, the
   confused-deputy problem, and containment.
8. **Operate a live AI system**: trace it, detect drift, define SLOs on a probabilistic
   system, and run an incident.

---

## The design framework

Introduced in Week 1, used every week, and the rubric for both exams. Level descriptors
for each step are published in [rubric.md](https://aise-stthomas.github.io/rubric).

| # | Step | Deepened in |
|---|---|---|
| 1 | **Scope & requirements** — users, decisions, functional/nonfunctional, scale, latency budget, cost budget | Week 2 |
| 2 | **Frame the AI task** — what decision the model makes, the I/O contract, whether it should be a model at all | Week 2 |
| 3 | **Metrics** — business metric → online proxy → offline metric, plus guardrails | Weeks 2–3, 6 |
| 4 | **Data** — sources, labels, freshness, privacy, feedback loop | Week 4 |
| 5 | **High-level architecture** — components, data flow, where the model lives, sync vs async | Weeks 3–7, one archetype a week |
| 6 | **Deep dive** — retrieval and ranking, the agent loop, the integration layer | Weeks 5–7, 9–11 |
| 7 | **Failure, scale & operations** — what breaks, monitoring, drift, rollout, cost at scale | Weeks 12–13 |

## Weekly structure

A 3-hour block, hybrid, built for people who worked all day:

| Time | Segment |
|---|---|
| 0:00–0:10 | **Failure of the Week** — one real, documented AI system failure, analyzed against the framework. Which step did they skip? |
| 0:10–1:20 | **Lecture** (~70 min, with a break) |
| 1:20–2:00 | **Design Studio** — build a design in pairs, then review it as a group against the rubric |
| 2:00–3:00 | **Lab** — read-and-run, finishable in the hour, independent of the projects |
| after | **Homework** — 10 multiple-choice questions, ~20 minutes, on the studio scenario and the lab. Assigned after the block; due before the next one. |

**Labs and projects are different things.** Labs are in-class, one per week, tuned to
that week's lecture, and independent of each other — you read well-commented code, run
it, and poke at it. Projects are outside class, cumulative, and build one system.
Lecture order serves the labs; project order serves the build. Neither constrains the
other.

The Studio runs in three formats: **greenfield design** most weeks; **peer mock
interviews** twice (Weeks 6 and 11); and **system audit** twice (Weeks 4 and 12) — here
is an existing system, find the three things that will hurt you. Most of you will
inherit an AI system before you design one, and auditing is the skill nobody teaches.

## The project, in one paragraph

You receive, in Week 1, a **working** support-ticket triage and resolution agent: a
ticket simulator, an agent loop, tools exposed through a capability server, a
deployment, a trace logger. It is deliberately naïve. Over four projects you replace its
AI-specific parts with your own — the evaluation harness (P1), the release gate and
telemetry (P2), the agent loop and orchestrator (P3), the hardening and operations
(P4) — and by Week 13 you are running a system you built the parts of that matter.
Each project ships with the reference solution for the previous one, so nobody builds
on a broken base. Full specs, the scaffold contents, and the budgets: [projects.md](https://aise-stthomas.github.io/projects).

---

## Schedule

Reading lists the target book chapter and, until those chapters are
drafted, the external reading that substitutes. Reading is capped at ~1.5 hours/week.

### Week 1 — Why AI systems fail differently
**Framework: orientation + steps 1–2**

*What's different:* Everything downstream follows from one fact — the component returns
a sample from a distribution, not an answer.

- Model-centric vs system-centric thinking; the demo-to-production gap
- Where stochasticity actually comes from: sampling, and temperature as a systems
  parameter rather than a personality setting
- The four properties: stochastic, data-specified, non-stationary, acting
- System archetypes: batch scoring, online prediction, retrieval assistant, agentic
  workflow, edge
- Hidden technical debt; the AI component as a permanently fallible subsystem
- The design framework; course structure; the scaffold walkthrough

**Design Studio:** *"Design a system that flags fraudulent transactions."* Scope only —
no solution. **Keep your answer. You will be handed it back in Week 13.**

**Lab:** *Feel the distribution.* Send one fixed input through the model 100 times and
plot the spread. Then find the slice of the scaffold where it fails 30% of the time.
Also: open your Learner Lab, create your Gemini key, and **set the usage alarms** —
cost is a requirement, and you configure it on day one.

**Reading:** Book Ch. 1–2 · Sculley et al., *Hidden Technical Debt in ML Systems*;
Zinkevich, *Rules of Machine Learning*.

**Project:** none this week. **P1 is assigned in Week 2.**

---

### Week 2 — Requirements, risk, and designing for mistakes
**Framework: steps 1–2**

*What's different:* You cannot specify "correct." You can only specify a rate, and then
design for what happens on the remainder.

- System goals vs model goals; why the accuracy number is not a requirement
- Every requirement is a rate × slice × remainder policy × owner
- Severity × frequency; the mistake catalog; FMEA and fault trees on AI components
- The mitigation toolkit: guardrails, confidence gating, human-in-the-loop, undo,
  graceful degradation, fallback chains, scoping the blast radius
- **Your guardrail is also a model** — same error rate problem, same drift
- **Automation bias — the failure mode of your favorite mitigation.** Human review
  degrades precisely as the model improves. If you propose it, you own this problem.
- Recourse, contestability, appeal paths as system features
- Regulation as a design input: NIST AI RMF, EU AI Act risk tiers
- The requirements conversation across roles: who decides the acceptable error rate

**Design Studio:** *"A résumé screener rejects a qualified candidate. Redesign the
system so that outcome is survivable."*

**Lab:** Hazard analysis on the scaffold; produce the mitigation table. (You will reuse
it in P1's blind-spot register.)

**Reading:** Book Ch. 3–4 · Kaestner, *Machine Learning in Production*, Ch. 6 *Gathering Requirements*, Ch. 7 *Planning for Mistakes*, Ch. 27 *Safety*;
NIST AI RMF core (skim).

**Project:** Scaffold running end to end. **P1 assigned — Measure it.**

---

### Week 3 — Comparing versions, and architecture, part 1
**Framework: step 3, continued; step 5**

*What's different:* every change to an AI system is a comparison between two
stochastic measurements, and the scorer is often a model itself. And inference is slow,
expensive, and capacity-constrained in ways that make "where does the model live" the
dominant architectural decision.

**Measuring AI systems, part 2 (step 3, continued)**

- Comparing two versions: paired differences; why the interval belongs to the unit you
  sampled; "cannot tell" as a result
- The smallest difference a suite can detect; sizing a slice before the experiment;
  multiple comparisons; the regression gate that replaces "tests pass"
- Judging the judges: a rater as an instrument, reliability before validity; agreement
  measures (percent, Cohen's κ, Fleiss' κ, Krippendorff's α, ICC); validity by kind of
  output, including tone and style with no ground truth; correcting a lenient judge's
  rate; where to spend human labels

**Architecture, part 1 (step 5)**

- Where the model lives: precompute, online sync, streaming, async, on-device; the cost,
  latency and capacity arithmetic; tail latency with a model in the loop; the failure
  path; the trade space
- **The determinism boundary**: what the model is allowed to decide, where correctness
  lives, where knowledge lives; the build as one versioned manifest; build or rent
- **The cascade**: candidate generation → ranking → selection, why it exists (cost),
  one architecture with many names, the recall ceiling

**Design Studio:** *Design the support desk.* The ticket system with two models: a
black-box classifier and the language-model triage step; the architecture, the tables,
sync and async per hop, the failure path.

**Lab:** *Put the model behind a URL.* The triage step deployed as a function with a URL
in the Learner Lab; cold start, warm latency, and what a time limit does.

**Reading:** Book Ch. 8–10, 14–15 · Miller, *Adding Error Bars to Evals*;
Huyen, *Designing Machine Learning Systems*, Ch. 7 *Model Deployment and Prediction
Service*; Dean & Barroso, *The Tail at Scale*.

---

> **From Week 4 the schedule is organized by kind of system.** Each week takes one
> system and asks the same three questions: **how is it built** (the architecture),
> **how is its data handled**, and **how is it measured**. The model is treated as a
> black box with a contract and a datasheet; the course builds everything around it.
> Tools are introduced the week a system first needs them. The seven-step framework
> remains the rubric for studios and exams. The studio each week is a fresh design of
> that week's kind of system; the lab builds one piece of it.

### Week 4 — How you build a scoring system
*The churn score: one model, a number for every account, before anyone asks and the moment someone does.*

- **How it is built:** the batch scorer (a DAG with a model in one node; the
  scheduler, the run id, the check, publishing by pointer; cadence as the dial) and the
  online scorer (feature assembly from the request plus a precomputed table; the model
  in process; the threshold as a requirement in a table; the serving log); where the
  two meet. *Introduced: the scheduler, the feature table, the threshold.*
- **How its data is handled:** where data lives around a black-box model; the dataset
  you trained on versus the one you will have; the ways to get data; building the
  dataset as a component (source inventory, instrumentation and the wait, the join and
  its match rate, who may see production data); the tables of an AI system (inference,
  features, labels, the dataset as a frozen query); the point-in-time join; data as
  specification. *Introduced: the inference table, point-in-time correctness.*
- **How it is measured:** precision at *k* on a holdout; recall and precision at the
  threshold, per slice; p99 of the online score; served = recomputed, exactly; the
  pipeline's freshness, completeness and share logged. *Introduced: the holdout, the
  consistency test.*

**Design Studio:** *Design the delivery-time estimate.* **Lab:** *Score every account,
nightly and at the request.* **Reading:** Book Ch. 6, 8 · Sambasivan et al., *Data
Cascades*; Breck et al., *The ML Test Score*. **Project: P1 due. P2 assigned.**

---

### Week 5 — How you build a retrieval-grounded assistant
*An internal question-answering system over a document corpus: the model's knowledge is frozen, and retrieval is how the right knowledge reaches it.*

- **How it is built:** the indexing path (corpus → chunks → index, offline) and the
  request path (query → retrieve → assemble context → generate → cite); embeddings as a
  learned map and where they fail; one approximate-nearest-neighbour algorithm worked;
  hybrid retrieval and rerank as the cascade again; the unit of retrieval; access control
  at retrieval time. *Introduced: the index, chunking, hybrid retrieval.*
- **How its data is handled:** the corpus as a dataset (provenance, freshness,
  invalidation, permissions); the context as data the model is given; labelling: where
  labels for text come from, the written rule, agreement, cost per label.
  *Introduced: labelling as a pipeline.*
- **How it is measured:** recall@k against a labelled set, per slice, including the
  "answer is not in the corpus" slice, before looking at generated text; groundedness;
  **LLM-as-a-judge, taught in full here**: the judge as an instrument, reliability
  before validity, agreement measures, validity by kind of output, correcting a judge's
  rate. *Introduced: the judge.*

**Design Studio:** *Design the runbook assistant.* **Lab:** Measure recall@5 of a
runbook retriever on 20 labelled questions; switch it from lexical to hybrid; validate a
judge against your own labels. **Reading:** Book Ch. 14, 17 · Lewis et al.,
*Retrieval-Augmented Generation*, §1–3; Manning, Raghavan & Schütze, *Introduction to
Information Retrieval*, Ch. 1 and §6.2; Zheng et al., *Judging LLM-as-a-Judge*, §1–3.

---

### Week 6 — How you build a ranking system
*A feed or a search results page: billions of candidates, a few hundred ranked, position decides what gets seen.*

- **How it is built:** candidate generation → ranking → re-ranking as one architecture
  with many names; the recall ceiling; the two-tower retriever and the learned ranker as
  black boxes; what is precomputed and what is scored at the request. *Introduced: the
  candidate index, the ranker.*
- **How its data is handled:** the serving log as the training set (what was shown, in
  what position, what happened); clicks as labels and what they are not; position bias;
  the feedback loop: outputs become inputs. *Introduced: implicit labels, the loop.*
- **How it is measured:** ranking metrics offline; online: A/B, interleaving, guardrail
  metrics; the noise floor online; the release gate on eval scores against the noise
  floor; what is in "the build" and rollback to a combination. *Introduced: online
  experiments, the release gate.*

**Design Studio:** peer mock interviews: *"Ship a new ranking model to 400M users.
Design the rollout."* **Lab:** Run an A/A test and see your own noise floor; gate a
change. **Reading:** Book Ch. 10, 16, 28 · Covington, Adams & Sargin, *Deep Neural
Networks for YouTube Recommendations*; Kohavi, Tang & Xu, *Trustworthy Online Controlled
Experiments* (selected).

---

### Week 7 — How you build a generation service at volume
*A language-model feature at 10,000 requests a second under a fixed daily budget: you cannot autoscale out of a shortage.*

- **How it is built:** the queue and backpressure; batching; caching (exact, prefix,
  semantic, and when semantic caching is a correctness bug); timeouts, retries,
  idempotency, rate limits; streaming; the model server and what the orchestrator assumes
  that the model breaks (memory-shaped capacity, minute-long cold starts, stateful
  routing); cost engineering: routing, cascades, distillation. *Introduced: the queue,
  the cache, the rate limiter, the model server.*
- **How its data is handled:** synthetic data: what it buys (coverage of rare cases,
  plumbing, red-team cases) and what it cannot (the base rate); testing a system before
  it has production data: borrowed benchmarks, expert cases, replay, shadow deployment,
  a pilot population. *Introduced: synthetic data, shadow deployment.*
- **How it is measured:** cost per request as a requirement; latency SLOs with a model
  in the loop; the canary and the kill switch; quality as an SLO dimension.
  *Introduced: the canary.*

**Design Studio:** *"Design the serving stack for an LLM feature at 10k QPS under a
fixed daily budget."* **Lab:** Hit the rate limit on purpose; add the queue, the cache
and the timeout; watch the latency curve recover. **Reading:** Book Ch. 11 · Google SRE
Book, handling overload and cascading failures. **Project: P2 due.**

---

### Week 8 — Checkpoint and project clinic
**Full block.**

- **0:00–1:15 — Checkpoint exam.** One design prompt, graded against the seven-step
  rubric, half a page per step. Covers Weeks 1–7. Same format and rules as the final
  (see Week 14 and Policies), so you meet the format once before it counts for real.
- **1:15–1:30 — Break.**
- **1:30–3:00 — Project clinic.** Structured, not free work time:
  - P2 reference walkthrough and the three most common failures from grading (20 min).
    This is when you receive the reference P2; build P3 on it if yours is shaky.
  - P3 kickoff: read the spec together; run the scaffold's naïve agent against the
    capability server and read its trace; sketch the loop replacement and what the
    checkpoint must contain (30 min).
  - Quota planning: how many trajectory runs your Gemini budget affords before Week 11,
    and therefore how big your trajectory harness can be (15 min).
  - Pair reviews with the instructor, priority to pairs flagged in P2 grading
    (remaining time; others get theirs in Week 9's lab hour).

**Project: P3 assigned.**

---

### Week 9 — How you build an agentic chatbot, part 1: the loop
*The support-ticket agent: a model that names actions, and the program around it that takes them.*

- **How it is built:** what the model actually does (a next-token distribution) and
  what everything else is (your program); the loop: render → sample → parse → validate →
  dispatch → append → repeat; a tool call byte by byte; the capability server and its
  description as the model's only knowledge of the tool; error text as a user interface.
  *Introduced: the loop, the capability server.*
- **How its data is handled:** the trace as the system's primary dataset: every run as
  a tree; what to record at each step; the context window as the real state.
  *Introduced: traces as data.*
- **How it is measured:** task success on a trajectory suite; step-level metrics
  (parse rate, tool-call validity, steps per task, cost per task); replay fixtures for
  an agent. *Introduced: trajectory evaluation.*

**Design Studio:** *Design the refund agent.* **Lab:** *Trace the loop.* **Reading:**
Book Ch. 20, 21, 23 · Anthropic, *Building Effective Agents*; a provider's tool-use API,
read as a wire format.

---

### Week 10 — How you build an agentic chatbot, part 2: state and orchestration
*The same agent across process death, with a human in the loop and a budget.*

- **How it is built:** the orchestrator: what it owns (state, routing, dispatch,
  budgets, retries, checkpoints, resume, pause for a human); durable execution across
  function invocations; idempotency keys on consequential actions; the approval gate as
  a row in a table; delegation and multi-agent as the same loop with a boundary.
  *Introduced: the orchestrator, the state table, the approval gate.*
- **How its data is handled:** memory as data with a write path controlled by a model;
  context engineering: what enters the context, what is summarized, what is dropped;
  retention of traces. *Introduced: memory and context as datasets.*
- **How it is measured:** evaluating multi-step runs: exactly-once actions under
  injected failures; cost and steps per task against a budget; where a run went wrong.
  *Introduced: fault injection on a run.*

**Design Studio:** *Design the agent that survives a kill.* **Lab:** *Kill and compact.*
**Reading:** Book Ch. 18, 19, 22, 24, 26.

---

### Week 11 — The agentic chatbot with a second vendor: the integration and trust layer
*A capability server you did not write appears. Discovery, description, invocation, contract, authorization, session.*

- **How it is built:** the six integration problems and the current protocols as
  instances (MCP, A2A); why RPC rather than REST; the contract and its versioning;
  three-party authorization and why credentials never enter the context; scoped tokens.
  *Introduced: the protocol layer, scoped tokens.*
- **How its data is handled:** tool descriptions and results as untrusted data; what
  from a vendor's response may enter the context; provenance on every appended
  message. *Introduced: data trust boundaries.*
- **How it is measured:** contract tests against a capability server; agreement
  between the agent's actions and a reviewer on sampled runs; labelling agent outputs.
  *Introduced: labelling trajectories.*

**Design Studio:** peer mock interviews on the full agent design. **Lab:** *Scoped
token.* **Reading:** Book Ch. 25, 27 · the capability-protocol specification the scaffold
uses, read as a primary source. **Project: P3 due. P4 assigned.**

---

### Week 12 — The agentic chatbot under attack: security and safety
*The second vendor's response contains instructions. What does the system do, and what can it be made to do?*

- **How it is built:** the threat model for a system that reads untrusted text and
  takes actions; containment: least privilege, blast radius, the approval gate at the
  moment of consequence, the kill switch; where guardrails sit and why a guardrail is
  also a model. *Introduced: containment.*
- **How its data is handled:** safety eval sets and red-team cases as datasets;
  injection corpora; what the trace must keep for an incident and what it must not.
  *Introduced: adversarial datasets.*
- **How it is measured:** attack success rate per attack class, with its interval;
  containment measured as what an attack could reach, not whether it was caught.
  *Introduced: red-team measurement.*

**Design Studio:** system audit of the Week 11 designs. **Lab:** *Red team.* **Reading:**
Book Ch. 32, 33 · Willison, prompt injection and the lethal trifecta; OWASP Top 10 for
LLM Applications.

---

### Week 13 — Operating a live AI system
*Any of the above, in production for a year: the world moved, the vendor changed the model, and the pager went off.*

- **How it is built:** alarms on cost, errors and quality; the vendor drift canary;
  SLOs with quality as a dimension; the improvement loop as a DAG (retraining,
  re-indexing, backfills); game day. *Introduced: alarms, SLOs, the canary.*
- **How its data is handled:** data over time: drift in inputs, labels and outputs;
  versioning datasets as they grow; retention and deletion. *Introduced: drift
  detection.*
- **How it is measured:** monitoring as continuous evaluation; the noise floor in
  production; incident review for a stochastic actor. *Introduced: production
  monitoring.*

**Design Studio:** the Week 1 envelope returns: redesign the fraud system with
everything since. **Lab:** *Game day.* **Reading:** Book Ch. 29, 30, 31 · Google SRE
Book, *Monitoring Distributed Systems*. **Project: P4 due. Repository tagged.**

---

### Week 14 — Demos and final
**Full block, hybrid.**

- **0:00–1:30 — Demos and defense.** Six minutes per pair, live for both halves: one
  ticket end to end off the frozen tag, one trace walked through, then three minutes of
  instructor questions on a design decision and the postmortem. No new work; the demo is
  the P4 submission. (Optional recorded backup submitted with P4, for connectivity.)
- **1:30–1:45 — Break.**
- **1:45 — Final opens.** Take-home, timed: two-hour window from open. One design prompt
  with numbers (QPS, latency budget, daily cost budget, an error rate someone must own),
  seven steps, half a page each; plus one audit question on a bespoke artifact. A short
  low-weight set of reasoning questions may precede it. Target: 90 real minutes.
  *Exam design to be finalized separately.*

> If the class exceeds ~14 pairs, demos shorten to five minutes or the defense moves
> into the P4 writeup. No lab is sacrificed for demos.

---

## Assessment

| Component | Weight | Notes |
|---|---|---|
| Weekly homework | 10% | 10 multiple-choice questions, ~20 minutes, assigned after each block and due before the next: applied questions on the studio scenario plus 2–3 that can only be answered by having done the lab. Deliberately quick; you get the full week because you have lives. Lowest score dropped. |
| Projects (4) | 40% | 10 each. Pairs. See [projects.md](https://aise-stthomas.github.io/projects). |
| Checkpoint exam | 20% | Week 8, in the block. |
| Final exam | 15% | Week 14, take-home timed window. |
| Demo and defense | 15% | Week 14, live. The one assessment nobody can game. |

Labs and studios are not graded directly — there is no TA, and grading them would be
theater. The weekly homework verifies both.

### Projects, briefly

Full specs in [projects.md](https://aise-stthomas.github.io/projects). Each project: what you replace in the scaffold, a one-page
design doc for what you built, an hour target, and a quota budget.

| Project | Assigned | Due | You replace / add | Hours |
|---|---|---|---|---|
| **P1 — Measure it** | Wk 2 | Wk 4 | Eval harness: golden set, slices, noise floor, validated judge, blind-spot register | ~12 |
| **P2 — Ship it** | Wk 4 | Wk 7 | Eval gate in CI (replay fixtures + live smoke), canary with kill switch, cost model, telemetry | ~12 |
| **P3 — Make it act** | Wk 8 | Wk 11 | Your agent loop and orchestrator: discovery and dispatch over the capability server, budgets, checkpoint/resume across invocations, approval gate, scoped token | ~12 |
| **P4 — Break it, run it** | Wk 11 | Wk 13 | Hardening after the red team, SLOs, tracing, game-day postmortem | ~12 |

**The reference-solution rule.** Each project ships with the instructor's solution to
the previous one. If yours is shaky, build on the reference. Nobody cascades.

## Systems Toolkit

Self-paced primers, 20–30 minutes each, released Week 1. Three are required because a
project depends on them; the rest are take-what-you-need.

| Primer | Required for |
|---|---|
| 1. Structured logging, metrics, and tracing | P2 |
| 2. Reading a provider's inference API as a wire format | P3 |
| 3. Delegated authorization: tokens and scopes | P3 |
| 4. Serverless deployment: functions, queues, and a key-value store in 25 minutes | — |
| 5. Containers and images | — |
| 6. Message queues and pub/sub | — |
| 7. DAG orchestrators | — |
| 8. Approximate nearest-neighbor indexes (HNSW and successors) and similarity search | — |
| 9. Reading a container-orchestrator deployment definition | — |

## Policies

**Late work.** Deadlines are real and enforced — permissively. You are working
professionals with jobs and families, and things happen. If you need time, say so —
before the deadline where possible — and you will get it; no explanation required. The
only thing that is not fine is silence: unrequested late work loses 10% per started
week. The weekly homework already has a full week; the dropped lowest score absorbs a
missed one.

**AI tools.** Encouraged — genuinely — on every lab and project. Not permitted on the
two exams. The course does not run a detection apparatus; obvious cases will be
treated as academic dishonesty, and beyond that the exams are designed so that a
generic answer scores as a generic answer. For any project you must be able to explain
any line of your submission in a five-minute live walkthrough; the Week 14 defense is
that walkthrough. P3 additionally prohibits agent *frameworks* — not AI assistance —
because the point is the mechanism.

**Collaboration.** Labs: collaborate freely. Exams: individual. Pairs do not share code
with other pairs, including during the red team, where you share *findings* and not
exploits until after the debrief.

**Attendance.** Hybrid; both halves attend the same block live. Lecture recordings
posted; the Design Studio is not recordable and is where a third of the learning
happens.

**Cost.** Everything runs on an AWS Academy Learner Lab account and the Gemini free
tier. The usage alarms you set in Week 1 are required. No EC2 instances, NAT gateways,
load balancers, or managed Kubernetes — none are needed, each can consume your $50
budget in days, and an exhausted budget deactivates the lab. See [projects.md](https://aise-stthomas.github.io/projects).

---

## Primary texts

No required purchase. The target text is the book under development alongside this course;
until chapters are drafted, selected chapters from:

- Kaestner, *Machine Learning in Production* (MIT Press) — open lecture notes
- Huyen, *Designing Machine Learning Systems* and *AI Engineering* (O'Reilly)
- Aminian & Xu, *Machine Learning System Design Interview* — for the design drills
- Google *SRE Book* — selected chapters
- Capability-protocol specifications — read as primary sources

Weeks 9–11 require instructor-authored notes; that material does not yet exist in
publishable form elsewhere, which is inconvenient for the semester and ideal for the
book.

> Reading links to be verified and pinned before the semester opens.
