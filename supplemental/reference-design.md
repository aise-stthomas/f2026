# Reference Design: The Operator
### A support-ticket triage and resolution agent, through all seven steps, at level 4

> This is the exemplar. It is the system you were handed in Week 1, designed the way the
> rubric asks for — every step at level 4, with the alternatives that were rejected and
> why. Read it in Week 3, when you have the vocabulary for steps 1–5; steps 6 and 7 will
> read fully by Week 11. Before each exam, reread it: this is what "level 4" looks like on
> the page.

---

## Step 1 — Scope and requirements

### Users

| Who | What they need |
|---|---|
| **The customer** | A correct resolution, fast; to never be wrongly refused; a path to a human when the agent is wrong |
| **The support agent** (human) | A queue they can clear; the trace attached to every escalation, not just the verdict |
| **The support ops lead** | Cost per resolution down; resolution quality measurable; the trade-off between auto-resolve rate and error rate made explicit — and theirs to set |
| **Finance** | Refund loss bounded; every refund attributable |
| **Security** | No unauthorized action, ever; an audit trail that survives a stochastic actor |
| **Compliance** | Customer data minimized in logs and context; retention bounded; decisions contestable |

### The decision

Per ticket, a **route**: *auto-resolve* (with a specific action — answer, refund up to
a cap, account change), *escalate* to a human with the trace attached, or *hold* pending
a human approval for an action above the cap. The model proposes; code routes; only
code issues consequential actions, and only within limits code enforces.

### The three numbers

- **Scale:** 1M tickets/month ≈ 23/minute average, peaks 10× (Monday morning, outage
  days). Bursty, not high-throughput.
- **Latency:** p95 ≤ 90 seconds to a resolution or an escalation. A ticket can wait; a
  customer sees "we're on it" immediately. This is an **asynchronous** system.
- **Cost:** ≤ $0.12 per resolution, monthly average, including model calls, retrieval,
  and infrastructure; human escalation is costed separately (it's the expensive path).

### Requirements, in the language of rates

| Rate | Slice | Remainder policy | Owner |
|---|---|---|---|
| Auto-resolves **≥ 70%** | refunds ≤ $50, account in good standing, single-intent tickets | escalate with trace; human queue SLO 4h | Support ops lead |
| Auto-resolves **≥ 40%** | all tickets | as above | Support ops lead |
| Wrong refund amount **≤ 1%** | all auto-resolved refunds | reversible within 24h; customer notified; case becomes a golden-set item | Support ops lead |
| Wrong resolution (customer reopens within 7d) **≤ 8%** | all auto-resolved | reopened tickets route to a human first; weekly labeled sample measures the rate | Support ops lead |
| **Unauthorized refund = 0** | all tickets, including adversarial | invariant: any occurrence is an incident; agent paused; enforced by the cap in code and the scoped token, not by the model | Security |
| Escalates **≥ 95%** | legal threats, safety language, requests over the cap, account-closure requests | remainder measured weekly on a labeled sample; misses are golden-set items | Support ops lead |
| p95 latency **≤ 90 s** | all tickets | slow tickets still complete; customer sees progress | Engineering |
| Cost **≤ $0.12** / resolution | monthly average | budget exhaustion pauses auto-resolution; everything escalates | Engineering |
| Appeals resolved **≤ 2 business days** at ≥ 95% | all customer appeals | overdue appeals escalate to a named person | Support ops lead |

### Constraints read as requirements

Customer PII may enter the context only as needed for the ticket, never wholesale; traces
are redacted and retained 90 days; every refund is attributable to a ticket, an
instruction, an approval (if any), and an action. Where the deployment falls under
consumer-protection rules, "the customer can contest" is a requirement, not a feature.

### The trade-off

Auto-resolve rate versus wrong-resolution rate. Under pressure, **auto-resolve rate gives
way.** Escalating more is always safe; resolving wrong is not. The cap, the invariant,
and the latency SLO do not move.

**Rejected:** a synchronous chat assistant (latency budget of seconds, no room for
review or approval, cost dominated by idle conversation). **Rejected:** "resolves 90% of
tickets" as the headline requirement — unowned, unsliced, and it would have driven the
threshold toward resolving wrong.

*Why this is level 4:* every consequential requirement has a name; both kinds of error
have a path; one requirement is an invariant and is identified as one; the quality
attribute that yields under pressure is stated.

---

## Step 2 — Frame the AI task

### What the model decides — and what it doesn't

The model reads a ticket plus retrieved context and **proposes** a route and, for
auto-resolve, a draft action with arguments: `refund(amount, reason)`, `answer(text)`,
`escalate(reason)`. That's the whole decision surface.

**Outside the boundary — code, not model:**

- The refund **cap** ($50 without approval, $200 with) is a constant.
- Whether an action **requires approval** is a policy lookup on (action, amount,
  account status), not a judgment.
- **Argument validation**: schema, account exists, amount ≤ cap, refund not already
  issued for this order (idempotency key).
- **Routing on known keys**: tickets containing legal-threat or safety language are
  escalated by a deterministic classifier before the agent runs (and the agent's own
  escalation is a second chance, not the first).
- **Formatting, arithmetic, lookups, dates.**

### I/O contract

*In:* ticket text (untrusted), account summary (fields whitelisted: status, tenure, last
3 orders, open refunds), retrieved runbook units (≤ 5), tool results (untrusted). *Out:*
a JSON object matching a schema — `{route, action, args, rationale}` — validated by code
before anything happens. Free-form text appears only inside `answer.text`, and that is
the only field a customer ever sees.

### Should it be a model at all?

Partly no. Roughly a third of tickets are single-intent, template-shaped ("where is my
order," "reset my password") and are resolved by a rule + lookup path with no model call.
The model handles the ambiguous middle: multi-intent tickets, tickets that need policy
interpretation, drafts that need to be in the customer's words.

**Rejected:** letting the model choose the refund amount freely (it becomes a target
for injection; a cap in a prompt is a request, not a limit). **Rejected:** fine-tuning
for policy (policy changes monthly; the runbook corpus is the right rung).

*Why level 4:* the decision is separated from the output; the boundary is drawn narrowly
and listed; the "should it be a model" question is answered with a specific slice that
isn't.

---

## Step 3 — Metrics

### The chain

| Business metric | Online proxy | Offline metric | Guardrail |
|---|---|---|---|
| Support cost per ticket | Cost per resolution; escalation rate | Trajectory cost distribution on the harness (report p50/p95, never the mean) | Escalation rate must not fall below what the labeled sample says is correct |
| Customer satisfaction; refund loss | Resolution without reopen in 7 days; appeal rate | Resolution correctness on the golden set, per slice; judge validated against human labels per slice | Wrong-refund rate ≤ 1%; **unauthorized refund = 0** |
| Time to resolution | p95 latency by route | Steps per trajectory; tool-error rate | Step and cost budgets never exceeded |

### Where the chain breaks — known and named

- **Reopen-within-7-days** undercounts wrong resolutions: some customers give up. The
  weekly labeled sample is the correction.
- **The judge** for resolution text is a model; it is validated monthly against 30
  human-labeled items *per slice* and its agreement is reported next to its scores. It
  is blind to polite failures ("we're sorry, we can't help") — the blind-spot register
  says so.
- **Cost per resolution** improves if the agent escalates everything; that's why
  escalation rate is a guardrail, not a target.

### Process, not just outcome

An agent that reaches the right resolution by issuing and reversing refunds until one
sticks is a latent failure. Trajectory metrics — steps, tool calls, tool errors, retries,
policy violations along the way — are first-class; a right outcome via a policy
violation scores as a failure.

### The noise floor

The full harness is run 5× on the same build before any comparison; the spread is the
floor. A change is a regression only if it exceeds the floor at 95% confidence, on any
slice.

**Rejected:** "task success rate" as the single metric (hides cost, hides the path, hides
the slice).

*Why level 4:* every link's failure is named; the judge is validated and its blind spot
written down; process metrics exist; the floor is measured, not assumed.

---

## Step 4 — Data

### Sources and freshness

| Data | Source | Freshness | Trust |
|---|---|---|---|
| Ticket text | customer | real-time | **untrusted** — instructions and data share this channel |
| Account summary | the account system, via `lookup_account` | seconds | trusted, whitelisted fields only |
| Runbooks / policies | the knowledge corpus | re-indexed nightly; policy changes invalidate affected units same-day | trusted content, authored by support ops; **treated as untrusted in context** anyway |
| Tool results | the capability server | real-time | untrusted in context (the world wrote them) |
| Labels | human review outcomes, reopen signal, appeals, weekly labeled sample | days to weeks; reopens biased toward the persistent | — |

### The feedback loop, and its containment

The agent's resolutions enter ticket history; ticket history is retrieved for the next
ticket from the same customer; agent-authored resolution text is a candidate for the
runbook corpus. Uncontained, the system trains on its own outputs. Containment:
agent-authored text is *tagged* and never enters the corpus without human authorship;
the weekly labeled sample is drawn *outside* the auto-resolved population; a 2%
exploration slice is routed to a human regardless of score, so the label set is not
selected by the model.

### Skew

The corpus and the index drift apart between re-indexes. A content hash per runbook unit
is stored with the index; a mismatch invalidates the unit. The training/serving version
of this problem doesn't arise (no training), but the *prompt* version does: the same
account-summary formatter is used offline in the harness and online in production, from
one code path.

### Privacy and retention

Only whitelisted account fields enter the context. Traces store a reference to the
context plus a redacted copy; raw context is retained 30 days, redacted 90. Customer
text in fixtures is synthetic or consented. Memory across sessions is limited to the
customer's own ticket history; nothing the agent "learned" about how to treat a customer
persists.

**Rejected:** dumping the full account record into context (PII, tokens, and an
injection target). **Rejected:** using reopen-within-7-days as the only label (biased;
the sample corrects it).

*Why level 4:* untrusted data is marked as a design input; the feedback loop is named and
contained by three mechanisms; skew has a mechanism; privacy reaches the architecture.

---

## Step 5 — Architecture

### Where the model lives

**Asynchronous, online.** A ticket enters a queue; an orchestrator run picks it up; the
customer sees an acknowledgment immediately and a resolution within the latency budget.
Nothing model-shaped is in a synchronous path. Indexing, evaluation, and the drift canary
run offline on a schedule.

### The components

```
ticket simulator / intake ──► queue ──► orchestrator (functions, one step per invocation)
                                            │  ▲
                                            ▼  │
                                  capability server ── lookup_account · issue_refund · escalate
                                  (scopes enforced; credentials held here)
                                            │
        LLM client (record/replay) ◄────────┤────────► key-value store (run state,
                 │                          │            checkpoints, approval rows)
                 ▼                          │
         model (vendor API,                 └────────► bucket (fixtures, traces,
         cheap tier for triage,                         corpus + index snapshot, golden set)
         stronger tier for drafts)

offline:  evaluation harness (CI, on fixtures + live smoke) · re-indexer (nightly) ·
          vendor drift canary (daily) · trace store → dashboards
human:    review queue (escalate route) · approval endpoint (hold route) · appeals
```

### Sync vs. async, decided

| Path | Sync/async | Why |
|---|---|---|
| Customer acknowledgment | sync | must be instant; contains no model output |
| Agent run | async, multi-step | latency budget is 90 s; steps are seconds each; process death is expected |
| Approval | async, suspended | a human may take hours; the run waits as a row |
| Retrieval | sync within a step | milliseconds; small corpus |
| Eval, re-index, canary | batch | offline by nature |

### The cascade, where cost demands it

A cheap model triages (route + intent); the stronger model drafts only on the
auto-resolve route. The deterministic pre-filter (rules + lookup) runs first and handles
the template-shaped third. Retrieval is hybrid (lexical + dense) with a rerank to five
units.

### The failure path, drawn

Model unavailable or over budget → **degradation ladder**: stronger model → cheaper model
→ deterministic path for template tickets → escalate everything. Each rung has its own
eval; the bottom rung is exercised weekly in a game day.

### The build

One manifest: code · prompts and templates · model identifiers (both tiers) · corpus
and index snapshot hash · tool definitions · configuration (caps, budgets, thresholds) ·
the eval suite and its noise floor. Any change to any line is a release and goes through
the gate.

**Rejected:** a synchronous single-call design (can't pause for approval, can't survive
a timeout, can't afford the strong model on every ticket). **Rejected:** a long-running
container holding the run in memory (process death loses the run; checkpoints in a store
survive it). **Rejected:** in-process tools (no scope boundary; credentials in the same
process as the model's context).

*Why level 4:* placement justified by the three numbers; sync/async decided per path;
the boundary visible; the cascade used for cost; the failure path drawn; the build defined;
alternatives rejected with reasons.

---

## Step 6 — Deep dive: the agent loop, the orchestrator, and the trust layer

### The loop, mechanically

Each step: **render** the context (instructions, account summary, retrieved units, ticket
marked as untrusted, prior tool results marked as untrusted) → **sample** → **parse** the
structured action → **validate** at the boundary (schema; tool exists; args in range; cap;
idempotency key unused) → **dispatch** over the capability protocol → **append** the
result, marked untrusted → repeat. The model does one thing: given tokens, produce a
distribution. Everything else is the orchestrator.

### The orchestrator's responsibilities, and the decisions made

| Responsibility | Decision |
|---|---|
| State | Context is rebuilt from the durable run record each step; the record, not the context, is the source of truth |
| Checkpointing | **Before every dispatch.** The record includes the pending action and its idempotency key; a crash after dispatch and before commit is resolved by querying the capability server with the key |
| Idempotency | Every consequential call carries `run_id:step:action_hash`; the server refuses a duplicate |
| Budgets | 25 steps · 40k tokens · $0.30 · 5 minutes wall clock · a "no new information in 3 steps" check. Any exhaustion → escalate with the trace |
| Retries | Transient tool errors retried 2× with backoff; recoverable errors (bad args) fed back to the model as an error message written for a model reader; repeated failure → escalate |
| Approval | `hold` writes an approval row and ends the invocation; the approval endpoint writes the decision; a resume invocation continues from the checkpoint. Consent at the moment of consequence |
| Termination | A final `route` action, or any budget, or a policy violation detected at the boundary |
| Observability | Every transition is a span with context reference, tokens, parse, dispatch, result, cost, latency, model version, join key |

### Context as the state machine

Budget per step: instructions 1.5k tokens · account summary ≤ 400 · retrieved units ≤ 5
× 300 · ticket ≤ 1k (truncated with a marker) · tool results ≤ 3 most recent, ≤ 500
each · history compacted beyond 8 steps into a structured summary that **must preserve**
the ticket's constraints and any amount mentioned. Eviction order: oldest tool results
first; retrieved units second; never the instructions or the ticket's constraint summary.
The compaction policy has its own eval (P3).

### Tools as interface design

Each tool description states purpose, when to use it, when *not* to, argument semantics,
side effects, and cost. `issue_refund` is idempotent (key), reversible (24h window),
gated (cap; approval above), and rate-limited (3 per customer per day). Error messages
returned to the model say what went wrong and what would work; they never include stack
traces or credentials.

### The integration layer — the six problems, answered for this system

| Problem | Answer |
|---|---|
| Discovery | Tools are listed from the capability server at run start; the list is versioned and pinned in the manifest |
| Description | Written for a model reader; reviewed like an API; part of the build |
| Invocation | Structured RPC over a persistent session; results typed and validated |
| Contract & versioning | Server advertises a version; a mismatch with the manifest fails the run to escalation, never silently |
| Authorization | Below |
| Session & direction | Server can push progress and elicit a human approval; the run is a durable session, not a request |

### Three-party authorization

The customer is the principal; the agent acts for them; the account and refund systems
are the resources. The harness obtains a **per-task, attenuated token**: this customer,
this ticket, `lookup_account` (read) and `issue_refund` up to the cap, expiring in 10
minutes and refreshed across a checkpoint. The token never enters the context; the model
names a tool, the harness attaches the credential. Approval above the cap is the human's
consent at the moment of consequence. Every action is logged with the token's claims,
the instruction that led to it, and the approval, if any — an audit trail that
identifies *who* acted even though the actor was a sample.

**What remains unsolved, honestly:** scope granularity still doesn't match intent —
"refund up to $50 for this ticket" is expressible; "refund only if the order was actually
damaged" is not. Injection through the ticket cannot be prevented, only contained.

**Rejected:** a service-account token for the whole run (ambient authority: whoever
controls the ticket controls the account system). **Rejected:** letting the model see the
credential "for convenience" (context is data the model can be induced to emit).

*Why level 4:* the loop is mechanical; checkpoint-before-dispatch and idempotency are
explicit; the context budget has an eviction order and an eval; tools are designed as
interfaces; the six problems are answered; authorization is three-party with attenuation;
the unsolved part is named.

---

## Step 7 — Failure, scale, and operations

### The mistake table — top rows

| Mistake | Slice | Severity | Mitigation | Its failure mode | Owner |
|---|---|---|---|---|---|
| Refunds the ticket's stated amount instead of policy's | tickets that state an amount | med · reversible · one customer | cap in code; policy amount from lookup, not context; trajectory judge flags "amount came from ticket" | judge misses paraphrased amounts | Ops lead |
| Follows an instruction embedded in the ticket | adversarial tickets | **high** · partly reversible · bounded by cap | untrusted marking; read-only default; cap; scoped token; quarantined pre-classifier | classifier is a model — it will miss some; the cap is the real bound | Security |
| Right resolution by a bad path (refund–reverse–refund) | multi-step refunds | latent | trajectory metrics; policy-violation gate at zero | novel bad paths the rubric didn't anticipate | Ops lead |
| Escalates nothing / escalates everything after a prompt change | all | med / cost | escalation-rate guardrail with a noise floor; canary | guardrail lags a slow drift | Engineering |
| Compaction drops the customer's constraint | long tickets | med · reversible | compaction preserves constraint summary; compaction eval | eval covers the constraints we imagined | Engineering |
| Human approver rubber-stamps | hold route | high | seeded approvals; sampled review; trace-not-verdict; approver agreement metric | attention decays anyway; the metric is the tripwire | Ops lead |

### SLOs

Availability 99.5% (the queue absorbs the rest) · p95 latency ≤ 90 s by route ·
**quality**: resolution correctness on the weekly sample ≥ 92% per slice, measured
against the noise floor · **cost** ≤ $0.12/resolution monthly · unauthorized refunds = 0
(an alert, not an SLO). Error budget on quality is spent by prompt and model changes;
when it's gone, changes stop until the sample recovers.

### Release

Every change to the manifest goes through: harness on replay fixtures (all slices, vs.
the noise floor) → live smoke suite (10 items) → shadow on 5% of traffic (no actions
taken) → canary at 10% with the kill switch armed → full. Rollback names the manifest,
not a commit. A **vendor model update** detected by the daily canary suite is treated as
an unplanned deploy: the harness runs, and if any slice regressed, the model tier is
pinned back where the vendor allows it, or the ladder drops a rung.

### Drift

Ticket-mix drift (input distribution monitor) · customers learning the agent (reopen and
appeal rates by phrasing cluster) · runbook drift vs. index (content hash) · vendor drift
(canary). Alerts fire on distributions against the floor, not on single events.

### Degradation ladder

Strong model → cheap model → deterministic path for template tickets → escalate
everything. Each rung has a harness score; the ladder is exercised in a monthly game day.

### Containment — the attacker's maximum payoff

Assume the model is persuaded. What can it reach? Read-only by default; `issue_refund`
capped at $50 without a human and $200 with; rate-limited 3/customer/day; scoped to this
customer's account; no egress except the two capability servers; no credentials in
context. **Maximum payoff of a fully successful injection: one capped refund to the
attacker's own account, reversible within 24 hours, flagged by the refund-to-self rule.**
The vendor server's descriptions are reviewed before enablement; a description change
is a release.

### Incidents with no diff

Runbook: *quality dropped, nothing deployed.* Check the vendor canary → check ticket-mix
drift → check the index hash → check the approver metric → rerun the harness on today's
sample. Mitigation ladder: pin the model tier → drop a rung → pause auto-resolution.
Postmortem updates the blind-spot register.

### Cost model — the dominant term

Cost ≈ tickets × [P(model path) × (steps × tokens per step × price per token)] + retrieval
+ infra. At 1M tickets, 65% on the model path, 6 steps × 4k tokens on the cheap tier and
one 3k-token draft on the strong tier, the **step count on the cheap tier is the dominant
term** — which is why the step budget is a cost control before it's a safety control,
and why the deterministic pre-filter is worth more than any prompt optimization.

**Rejected:** an output-filtering guardrail as the injection defense (it's a model; the
cap is the defense). **Rejected:** human review of every auto-resolution (throughput,
and Bainbridge — review would decay within a month).

*Why level 4:* every mitigation has a failure mode; automation bias is addressed where a
human is a control; the ladder ends in a deterministic rung; containment states the
attacker's maximum payoff in dollars; the cost model names its dominant term; the
no-diff incident has a runbook.

---

## What to take from this

Not the numbers — yours will differ. Take the shape: users before boxes; the decision
separated from the model's output; a boundary drawn narrowly; a metric chain with its
breaks named; untrusted data marked; placement justified by three numbers; the loop
stated mechanically; authority attenuated; every mitigation with its failure mode; and
an honest line about what isn't solved. That shape, on any system, is a level-4 design.
