# AI Systems Engineering
### University of St. Thomas — Fall 2026

> **Draft status.** v3 restructures assessment around four cumulative pair projects on a
> shared scaffold, caps outside-class work at 5 hours/week, and fixes the infrastructure
> to what an AWS Academy Learner Lab budget and the Gemini free tier can carry. Project specs, the
> scaffold, and budgets are in [projects.md](supplemental/projects.md). Exam design is a placeholder (see Week 8
> and Week 14). Semester dates and grading boundaries are placeholders.

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
[projects.md](supplemental/projects.md) for the account setup and the budget rules.

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
for each step are published in [rubric.md](supplemental/rubric.md).

| # | Step | Deepened in |
|---|---|---|
| 1 | **Scope & requirements** — users, decisions, functional/nonfunctional, scale, latency budget, cost budget | Week 2 |
| 2 | **Frame the AI task** — what decision the model makes, the I/O contract, whether it should be a model at all | Week 2 |
| 3 | **Metrics** — business metric → online proxy → offline metric, plus guardrails | Weeks 5–6 |
| 4 | **Data** — sources, labels, freshness, privacy, feedback loop | Week 4 |
| 5 | **High-level architecture** — components, data flow, where the model lives, sync vs async | Week 3 |
| 6 | **Deep dive** — retrieval and ranking, the agent loop, the integration layer | Weeks 7, 9–11 |
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
on a broken base. Full specs, the scaffold contents, and the budgets: [projects.md](supplemental/projects.md).

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

**Project:** Scaffold running end to end. **P1 assigned.**

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

**Reading:** Book Ch. 3–4 · Kaestner, *Machine Learning in Production* (risk chapters);
NIST AI RMF core (skim).

---

### Week 3 — Architecture and the trade space
**Framework: step 5**

*What's different:* Inference is slow, expensive, and capacity-constrained in ways that
make "where does the model live" the dominant architectural decision.

- Where the model lives: precompute, online sync, streaming, async, on-device
- The trade space: latency vs quality vs cost vs freshness
- Decomposition; the model as a service boundary; the pipeline jungle
- **The cascade: candidate generation → ranking → re-ranking.** Why it exists (cost).
  **This is the same architecture as your RAG pipeline** — learn it once.
- **The determinism boundary** — deciding, deliberately, what the model is allowed to
  decide. The best agent is often the one you replaced with a state machine.
- Adapt, prompt, or retrieve? — as an architectural decision with maintenance cost
- Capacity and unit-economics reasoning; cost per request as a requirement
- Build vs buy; vendor and model lock-in

**Design Studio:** *"Design Reels ranking."* The canonical interview prompt.

**Lab:** Back-of-envelope capacity and cost model for the Operator at 1M tickets/month;
find the dominant term. Structured peer review of a partner's model.

**Reading:** Book Ch. 8–10 · Huyen, *Designing ML Systems*, architecture chapters;
Dean & Barroso, *The Tail at Scale*.

---

### Week 4 — Data as specification, and retrieval
**Framework: step 4** · *Retrieval archetype*

*What's different:* The specification lives in data — a data defect and a code defect
are the same class of problem, and only one shows up in a diff. And the model's knowledge
is frozen; retrieval is how the right knowledge reaches it, and retrieval quality, not
model quality, is usually the bottleneck.

**Data as specification (30 min)**

- Data contracts, schemas, and schema evolution — the interface between producers and
  consumers
- **Training/serving skew as a distributed-systems consistency problem** — two code
  paths computing one transformation, drifting apart. Feature stores as an answer to
  *that*, not a product category. The prompt-formatting version of the same problem.
- Privacy, retention, and PII as design constraints that reach back into architecture:
  what enters the context, what the trace keeps, for how long
- Validation, lineage, and provenance — briefly; what "version the data" means

**Retrieval, mechanically (25 min)**

- **Embeddings** as a learned map where similarity ≈ relevance — and where that fails:
  negation, numbers, exact identifiers, synonyms it never saw. The retriever is a model
  too; property 1 applies.
- **One approximate-nearest-neighbor algorithm, worked:** random projection trees. A
  random hyperplane splits the space; nearby points usually land on the same side;
  recurse to small leaves; search one leaf. A *forest* of trees raises recall at the
  cost of speed — **index recall is a rate you measure.** The algorithm changes yearly;
  the idea (partition the space, trade recall for speed, measure the recall) does not.
  *Self-learn the current one when you need it; primer 8.*
- **Hybrid (lexical + dense) and rerank** — the cascade from Week 3, again. Lexical
  catches what dense misses.
- **The unit of retrieval.** Chunking is representation design: too small loses
  meaning, too large loses precision. Where most retrieval systems actually fail.
- **Evaluating retrieval separately from generation:** recall@k against a labeled set,
  per slice, including the "answer is not in the corpus" slice — before you look at
  the model's output at all.
- Freshness and invalidation (corpus and index drift apart); **access control at
  retrieval time** — the model believes what it is shown and cannot enforce permissions.
- *Dated sidebar:* "vector database" is a product category, not an architecture; hybrid
  is the default; most production vector search is a feature of a store you already run.

**Closing (8 min)**

- Feedback loops, previewed: your outputs become tomorrow's inputs; the loop in the
  Operator. Week 6 owns it.
- Retraining and re-indexing as a DAG — pointer to Week 13 and primer 7.

**Design Studio — audit format:** *"Here is a running recommendation system's data
pipeline. Find the three things that will hurt you."* (Skew, staleness, and a feedback
loop are in there; so are two red herrings.)

**Lab:** Two parts. **(1)** Add contract validation to the scaffold's ticket pipeline,
break it deliberately, and watch what validation does and does not catch. **(2)** Measure
recall@5 of the scaffold's runbook retriever on 20 labeled questions; switch it from
lexical to hybrid and watch recall move; find the question where the right unit exists
in the corpus but was never retrieved, and say why.

**Reading:** Book Ch. 6, 17 · Sambasivan et al., *Data Cascades*; Breck et al., *The ML
Test Score*.

**Project: P1 due — Measure it.** **P2 assigned.**

---

### Week 5 — Evaluation I: offline
**Framework: step 3**

*What's different:* This is the week the course turns on. A test is boolean and
deterministic; an eval is a statistic over a sampled suite with a confidence interval.

- Task-level vs component-level evaluation; the harness as the spec
- Golden sets; slicing, and why the aggregate hides the failure
- **Labels and ground truth** — who decides what is true; inter-annotator agreement as
  the ceiling on everything downstream; label leakage; labels that are themselves model
  outputs
- Behavioral testing: invariance, directional expectation, minimum functionality
- Evaluating generative output: rubrics, LLM-as-judge **and its failure modes** —
  position bias, self-preference, verbosity bias, judge drift
- Validating a judge against human labels; inter-rater agreement
- **The noise floor**: how many items you need, and how large a real regression is
- Fairness metrics as slicing, in the harness

**Design Studio:** *"Design the evaluation for a customer-support summarizer."*

**Lab:** Measure judge–human agreement on your own P1 task, per slice.

**Reading:** Book Ch. 12–15 · Ribeiro et al., *CheckList*; D'Amour et al.,
*Underspecification*.

---

### Week 6 — Evaluation II: online, and shipping a change
**Framework: step 3**

*What's different:* Offline metrics disagree with what you want, systematically. And
"roll back the deploy" does not name a single artifact.

- Shadow, canary, A/B, interleaving, bandits — and when each is appropriate
- Guardrail metrics; proxy metrics and Goodhart's law
- Ranking metrics vs engagement; position bias
- Telemetry design: what to log so you can learn anything later
- Harvesting labels from production; delayed and biased feedback
- Pathological feedback loops, and detecting one you are inside of
- **What is in "the build"**: code + prompts + model version + index + tool definitions
  + eval suite. Versioning and reproducing all of it.
- **Release gates on eval scores** against the noise floor — the CI gate that replaces
  "tests pass"; gating on replay fixtures plus a live smoke suite
- Rollback when the artifact is a combination rather than a commit

**Design Studio:** **Peer mock interviews.** *"Ship a new ranking model to 400M users.
Design the rollout."*

**Lab:** Run an A/A test on the scaffold and see your own noise floor.

**Reading:** Book Ch. 16, 28 · Kohavi, Tang & Xu, *Trustworthy Online Controlled
Experiments* (selected).

---

### Week 7 — Serving, inference economics, and scheduling on scarce capacity
**Framework: steps 6–7**

*What's different:* You cannot autoscale your way out of a shortage, cost per request is
a requirement, and the unit of capacity is accelerator memory rather than CPU.

- Queues and backpressure; why adding replicas does not fix a saturated queue
- Batching, including continuous batching; throughput vs latency
- Caching: exact, prefix, semantic — and when semantic caching is a correctness bug
- Timeouts, retries, idempotency, circuit breakers, rate limits, quotas
- **Streaming and perceived latency**; progressive disclosure as architecture
- **What the orchestrator assumes and what the model breaks:** capacity is
  memory-shaped (KV cache scales with context length — one long request can OOM a
  stable pod); cold start is minutes (readiness must mean "weights loaded and a golden
  probe passed," or a successful deploy causes an outage); routing is stateful (prefix
  caches — adding replicas can *raise* p50); liveness ≠ correctness (alive and wrong)
- **Per-customer weights:** canarying a base model needs capacity for both versions;
  per-tenant adapters make tenant a slice, the manifest N rows, and hot-swap a deploy
  with no diff
- Cost engineering as system design: routing, cascades, distillation, quantization
- *(Read a deployment definition; do not operate a cluster.)*

**Design Studio:** *"Design the serving stack for an LLM feature at 10k QPS under a
fixed daily budget."*

**Lab:** Hit the Gemini rate limit on purpose. Add the queue, the cache, and the
timeout; watch the latency curve recover.

**Reading:** Book Ch. 11 · Google SRE Book — handling overload, cascading failures.

**Project: P2 due — Ship it.**

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

### Week 9 — Agents I: the mechanism
**Framework: step 6**

*What's different:* This is a new engineering discipline, and almost all public material
about it is framework tutorials that hide the mechanism.

- **What an LLM actually does in an agent**: given tokens, produce a distribution over
  the next token. It does not call anything, decide anything persistently, remember
  anything between invocations, or run a loop. Everything else is your program.
- **What a tool call actually is**, end to end: descriptions rendered into context →
  model emits tokens matching a format → *your harness* parses and dispatches → result
  appended → model invoked again
- Consequences derived, not asserted: the loop is your control flow; malformed calls are
  samples, not exceptions; validate at the boundary; cost and latency are superlinear in
  steps; the description is the model's only knowledge of the tool; **error messages
  are an interface for a nondeterministic reader**
- The control spectrum: prompt chain → router → workflow → autonomous loop
- When *not* to build an agent
- Stopping conditions, step limits, budget limits as safety mechanisms

**Design Studio:** *"Design an agent that resolves customer refund requests."*

**Lab:** Read the scaffold's loop (~100 lines). Trace one run token by token: the
render, the parse, the dispatch, the context growth. Then break it: give it a tool that
returns a stack trace and watch what it does.

**Reading:** Book Ch. 20–21 · Anthropic, *Building Effective Agents*; a provider's
tool-use API documentation, read as a wire format.

---

### Week 10 — Agents II: state, orchestration, and delegation
**Framework: step 6**

*What's different:* The context window is the real state machine, and evaluating a
forty-step trajectory is not the same problem as evaluating one answer.

- Context as state: budgeting, compaction, summarization; what gets evicted and what
  breaks when it does
- Memory tiers — and which are just retrieval in a hat
- **The orchestrator**: what it owns — state, routing, dispatch, budgets, retries,
  concurrency, termination, checkpointing, resumption, human handoff
- Durable execution across process death; the approval gate as a suspended run
- Delegation and subagents; context isolation; multi-agent as the continuum's far end;
  coordination failures — deadlock, infinite delegation, cost explosion
- **Evaluating agents**: trajectory vs outcome, partial credit, per-step attribution,
  cost and step count as eval dimensions; a right answer by a stupid path is a latent
  failure

**Design Studio:** *"Design a research agent that runs for 30 minutes and survives a
process restart."*

**Lab:** Kill the scaffold's agent mid-run and watch what it loses. Add compaction and
observe what breaks.

**Reading:** Book Ch. 18–19, 22, 24, 26.

---

### Week 11 — The integration and trust layer
**Framework: step 6**

*What's different:* The consumer of the interface is a nondeterministic process that must
discover, interpret, and invoke at runtime — not a programmer reading docs at build time.

**The general problem first.** An agent needs capabilities it was not hard-coded
against. Six problems, and every protocol answers some subset:

1. **Discovery** — how does the agent learn what exists?
2. **Description** — how is a capability described to a *model* reader? Not syntax —
   when to use it, what it means, what it costs.
3. **Invocation** — the wire mechanics.
4. **Contract and versioning** — capability sets that change mid-session.
5. **Authorization** — who acts, on whose behalf, with what scope, who consented.
6. **Session and direction** — long-lived state, progress, streaming, server-initiated
   calls.

**Then the design decisions:**

- Why RPC rather than REST for a verb-oriented caller; why the session must be
  bidirectional. Derive it; don't memorize it.
- The scaffold's capability server as one implementation, mapped onto the six problems.
  You have been calling it since Week 1; now you know why it is shaped that way.
- Agent→agent as a different problem shape: opaque stochastic remote party, task
  lifecycle instead of return value, goal delegation instead of invocation
- The N×M problem; the LSP analogy; what protocols do *not* solve
- **A second, "vendor" capability server** appears this week: same client code, new
  capability, zero integration work. Keep it. You will meet it again in Week 12.

**Authorization — the hard part, and it is not solved.** Three parties: user, agent
acting for them, resource.

- Ambient authority is how injection becomes privilege; least privilege as containment
- Scope vs intent; capability-based security and attenuated tokens
- Consent at the moment of consequence; the approval gate as an authorization primitive
- **Credentials never enter the context** — derived: context is data the model can be
  induced to emit
- Audit and non-repudiation for a stochastic actor; token lifetime for a long-running
  agent

**Design Studio:** **Peer mock interviews.** *"Design the integration layer for an agent
that must act across five internal systems on behalf of a user."*

**Lab:** Attach a per-task scoped token to your client; watch the server refuse an
out-of-scope refund. Then connect the vendor server and use it with no code changes.

**Reading:** Book Ch. 23, 25, 27 · The capability-protocol specification your scaffold
uses (primitives, transports, authorization), read as a primary source.

**Project: P3 due — Make it act.** **P4 assigned.**

---

### Week 12 — Security and safety for agentic systems
**Framework: step 7**

*What's different:* Your system takes consequential actions, and its instructions and its
data arrive through the same channel.

- Threat-modeling an agentic system; what an attacker actually wants
- **Prompt injection as a confused-deputy problem** — and why it is not patched
- The lethal combination: private data + untrusted content + an exfiltration channel
- Indirect injection through tickets, retrieved documents, tool results — **and tool
  descriptions**: the vendor server from Week 11 is not what it seemed
- Least privilege for tools; capability scoping; sandboxing; egress control
- Approval gates for consequential actions; spending and blast-radius limits
- Supply chain: models, prompts, indexes, third-party capability servers
- Classic ML attacks where they still bind: poisoning, extraction, membership inference
- Filtering and its limits: the detector is a model; bound the blast radius instead

**Design Studio — audit format:** *"An agent with email access reads an untrusted
document. Threat model it."*

**Lab:** **Cross-pair red team.** Attack another pair's agent via indirect injection —
through a ticket, and through the vendor server. Share findings, not exploits, until
the debrief. Then patch yours.

**Reading:** Book Ch. 32–33 · Willison on prompt injection and the lethal trifecta;
OWASP Top 10 for LLM Applications.

---

### Week 13 — Operating a live AI system
**Framework: step 7**

*What's different:* The system decays with no edit at all.

- Tracing nondeterministic workflows: what *is* a request when one turn becomes forty
  tool calls? Unbounded span nesting; bimodal latency by task type
- Metrics that matter: cost per resolution, loop depth, tool error rate, escalation rate,
  quality — alongside the usual four
- Drift: data drift, concept drift, and **your vendor silently changing the model**
- Continuous evaluation in production; canarying a prompt change
- **The improvement loop as a DAG:** retraining and re-indexing cadence, backfills
  that are hard when features are time-dependent, idempotent tasks because retries are
  certain *(one instance: a DAG scheduler — primer 7)*
- SLOs and error budgets for a probabilistic system; quality as an SLO dimension
- Degradation ladders; incident response; the postmortem when the root cause has no diff
- Technical debt and the maintenance burden; when to delete a model

**Design Studio:** **The callback.** You are handed back your Week 1 fraud-detection
scope. Redesign it. The gap between the two answers is the course.

**Lab:** **Game day.** The instructor injects a fault into your running system — a
swapped replay fixture that simulates the vendor changing the model, a poisoned runbook,
a drift in the ticket stream. Detect it from traces, triage the layer, mitigate, start
the postmortem. Instructor in the room.

**Reading:** Book Ch. 29–31.

**Project: P4 due — Break it, run it.** Repository tagged at the deadline; the Week 14
demo runs off the tag.

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
| Projects (4) | 40% | 10 each. Pairs. See [projects.md](supplemental/projects.md). |
| Checkpoint exam | 20% | Week 8, in the block. |
| Final exam | 15% | Week 14, take-home timed window. |
| Demo and defense | 15% | Week 14, live. The one assessment nobody can game. |

Labs and studios are not graded directly — there is no TA, and grading them would be
theater. The weekly homework verifies both.

### Projects, briefly

Full specs in [projects.md](supplemental/projects.md). Each project: what you replace in the scaffold, a one-page
design doc for what you built, an hour target, and a quota budget.

| Project | Assigned | Due | You replace / add | Hours |
|---|---|---|---|---|
| **P1 — Measure it** | Wk 1 | Wk 4 | Eval harness: golden set, slices, noise floor, validated judge, blind-spot register | ~12 |
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
budget in days, and an exhausted budget deactivates the lab. See [projects.md](supplemental/projects.md).

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
