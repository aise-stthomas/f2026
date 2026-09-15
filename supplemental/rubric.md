# The Design Review Rubric
### Seven steps × four levels — the standard the studios, the checkpoint, and the final are graded against

> You are graded against a standard you can read. Every step of the framework has four
> level descriptors below. The exams score each step separately; the studio walkthroughs
> use the same language. Read this before the first design you're graded on, and read
> it again before each exam.

---

## How the levels work

| Level | In one line |
|---|---|
| **1** | Absent, or generic enough to apply to any system. |
| **2** | Present and specific to this system, but unquantified and unowned. |
| **3** | Quantified, specific, and mostly complete. Numbers, slices, names. |
| **4** | Level 3, plus the trade-offs are explicit, at least one alternative is named and rejected for a reason, and the AI-specific concerns of the step are handled rather than mentioned. |

Two rules that apply to every step:

**What raises the level is depth on the AI-specific questions, not infrastructure
sophistication.** A design that is plain on infrastructure — "a queue, a function, a
key-value store" — and sharp on *where the model lives, what it's allowed to decide,
what happens when it's wrong, and how you'd know* is a level-4 design. A design with
three message queues and no remainder policy is a level 2. This course's thesis is that
infrastructure earns its place only where the model changes it; the rubric agrees.

**Naming a vendor, a framework, or a product does not raise the level.** "Use LangChain"
is not an architecture. "Use Kafka" is not a data design. The mechanism has to be named
and justified; the product is optional.

## The seven steps

### Step 1 — Scope and requirements

| Level | Descriptor |
|---|---|
| 1 | Users are "the business" or "the customer." Requirement is "accurate" or "works well." No numbers. Jumps to architecture. |
| 2 | Names two or more distinct user groups. States a goal in business terms. Mentions scale, latency, or cost without numbers. |
| 3 | Names the user groups and what each needs. Gives the three numbers — scale, latency budget, cost budget — with values (estimated is fine; vague is not). States at least one requirement in the form *rate × slice × remainder × owner*. |
| 4 | All of level 3, plus: every consequential requirement has an owner; the remainder path is designed for *both* kinds of error; at least one requirement is an invariant (a rate of zero) and is identified as such; regulatory or policy constraints are read as requirements where they bind; and the trade-off among quality attributes is stated — which one gives way under pressure. |

*What lowers it:* an accuracy number presented as a requirement; a human-in-the-loop
mentioned as a remainder policy with no design behind it.

### Step 2 — Frame the AI task

| Level | Descriptor |
|---|---|
| 1 | "Use an LLM" or "train a classifier." The decision is undefined. |
| 2 | States what the model predicts or generates. Names the input and output loosely. |
| 3 | States the *decision* — the route or action the system takes — and separates it from the model's output (the model scores; code routes). Gives the I/O contract: fields in, fields out, freshness. Identifies at least one thing the model must not decide. |
| 4 | All of level 3, plus: asks and answers *should it be a model at all* — names what is handled by rules, lookups, or thresholds and why; draws the determinism boundary deliberately and narrowly; states which decisions are inside the boundary (must be evaluated) and which are outside (must be tested); and notes what would move the boundary as models improve. |

*What lowers it:* the model making a decision that a lookup could make; a free-form
output where a schema was possible.

### Step 3 — Metrics

| Level | Descriptor |
|---|---|
| 1 | "Accuracy," "F1," or "user satisfaction," alone. |
| 2 | Names an offline metric and a business metric without connecting them. |
| 3 | Builds the chain: business metric → online proxy → offline metric, and names a guardrail metric. Metrics are per slice. Distinguishes outcome from process where the system takes multiple steps. |
| 4 | All of level 3, plus: names where each link of the chain is known to break (proxy vs. outcome, offline vs. online); states the noise floor or how it would be measured; identifies what cannot be measured and says so; for judged or generated output, says how the judge is validated; for agents, includes cost and step count as first-class dimensions. |

*What lowers it:* a metric a trivial system could maximize; a judge with no validation.

### Step 4 — Data

| Level | Descriptor |
|---|---|
| 1 | "We have data." Sources unnamed. |
| 2 | Names the sources and roughly what's in them. Mentions labels. |
| 3 | Names sources, freshness, and the label source; identifies where labels are delayed or biased; states privacy and retention constraints; notes what flows into the model's context and where it comes from. |
| 4 | All of level 3, plus: identifies the feedback loop (outputs becoming inputs) and how it's contained — holdouts, exploration, or a labeled sample outside the loop; identifies training/serving or corpus/index skew and the mechanism that prevents it; marks which data is untrusted (user-authored, retrieved, tool results) and how that affects the design; names a data contract or validation at a boundary. |

*What lowers it:* labels that come from the system's own outputs with no correction;
PII in the context with no minimization.

### Step 5 — Architecture

| Level | Descriptor |
|---|---|
| 1 | Boxes with no stated purpose, or a single box called "AI." |
| 2 | Components named; data flow shown; no reasoning about where the model lives or why. |
| 3 | States where the model lives (batch, online sync, streaming, async, edge) and why; separates sync from async; names the components needed by the three numbers from step 1; shows where the evaluation harness and the trace sit. |
| 4 | All of level 3, plus: the determinism boundary from step 2 is visible in the architecture — what is code, what is model; the cascade (candidate → rank → select) is used where cost demands it; the failure path is drawn — what runs when the model is unavailable or over budget; the build is defined (code + prompts + model + index + tools + eval suite) and versioned as one artifact; one alternative placement is named and rejected with a reason. |

*What lowers it:* the model in a synchronous path it can't meet the latency budget in;
infrastructure chosen for sophistication rather than the three numbers.

### Step 6 — Deep dive

The deep dive changes by system shape. Grade the one the prompt calls for.

| Level | Descriptor |
|---|---|
| 1 | Restates the architecture. |
| 2 | Describes the mechanism in framework terms ("the agent calls tools," "we retrieve relevant chunks"). |
| 3 | **Retrieval:** the unit of retrieval, the index type, the rerank stage, and how retrieval is evaluated separately from generation. **Agent:** the loop stated mechanically — render, sample, parse, validate, dispatch, append — with budgets and a stopping rule. **Integration:** the six problems (discovery, description, invocation, contract, authorization, session) named for this system. |
| 4 | All of level 3, plus, for the shape in question — **Retrieval:** the access-control-at-retrieval decision; freshness and invalidation; the "not in corpus" case. **Agent:** checkpoint-before-dispatch and idempotency for consequential actions; context budget with an eviction policy; the approval gate as a suspension point; tool descriptions treated as interface design. **Integration:** three-party authorization — scope attenuated per task, consent at the moment of consequence, credentials never in context, audit that survives a stochastic actor. In all cases: what remains unsolved is named honestly. |

*What lowers it:* "the model decides" where the harness should; a framework name in
place of a mechanism.

### Step 7 — Failure, scale, and operations

| Level | Descriptor |
|---|---|
| 1 | "We'll monitor it." |
| 2 | Lists failure modes without severity or mitigation. Mentions monitoring and retraining. |
| 3 | A mistake table: the top failure modes with severity (cost × reversibility × blast radius), a mitigation for each, and an owner. SLOs that include quality and cost, not just latency and availability. A rollout design — shadow or canary, with a kill switch — and what "rollback" rolls back to. Drift named, including vendor drift. |
| 4 | All of level 3, plus: every mitigation has its own failure mode written next to it; automation bias is addressed wherever a human is a control; the degradation ladder is explicit and ends in a deterministic or human rung; containment bounds what a fully wrong model can do — caps, scopes, read-only defaults — and the attacker's maximum payoff is stated; the cost model names its dominant term; an incident with no diff (vendor change, drift, feedback loop) has a runbook. |

*What lowers it:* a guardrail with no evaluation; human review with no design for
attention decay; a rollout with no kill switch.

---

## Scoring

**Studios and homework.** The studio walkthrough uses the level language; nothing is
scored. The weekly homework tests whether you can recognize the levels.

**Exams.** Each step is scored 1–4 and weighted by the prompt. A typical design prompt
weights steps 1, 2, 5, and 7 most heavily; a deep-dive prompt weights step 6. Converting
to a grade:

| Mean level | Grade band |
|---|---|
| ≥ 3.6 | A |
| 3.0 – 3.5 | B |
| 2.2 – 2.9 | C |
| < 2.2 | Below |

A level 4 on every step is rare and not expected. A level 3 on every step, with a 4 on
the step the prompt is about, is an excellent answer.

**Space limits are part of the rubric.** Half a page per step on the exams. A level-4
answer fits; an answer that runs long is usually a level 2 that hasn't decided what
matters.

## The three most common ways to lose a level

1. **Skipping step 1.** Drawing before scoping. The users, the decision, the three
   numbers, the rates, the owner — in that order, before any box.
2. **Letting the model decide what code should decide.** Every capability outside the
   determinism boundary is one you never have to evaluate, monitor, or defend. Push
   things out.
3. **Mitigations without failure modes.** "Add a guardrail" / "add a human" / "add a
   fallback" — each is a component with its own error rate. Name it.
