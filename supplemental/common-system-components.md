# Common System Components
### A one-page vocabulary for the parts that show up in AI system designs

> This is not a distributed-systems course, and it doesn't need to be. But a design
> needs nouns. Here are the components that appear in nearly every AI system design,
> what each is for, and — the part that matters here — *what changes about it when
> there's a model in the loop.* The Systems Toolkit primers go deeper on any of them.

---

## Moving data

| Component | What it's for | Where it appears in an AI design · what the model changes |
|---|---|---|
| **Queue** | Decouples a producer from a consumer; holds work until it can be done. | Between "a ticket arrived" and "the agent runs." Inference is slow, expensive, and rate-limited, so the queue is the shock absorber — and *adding replicas does not fix a saturated queue* when the scarce resource is the model. |
| **Stream / pub-sub** | Continuous events, many consumers. | Transactions, clicks, tickets as they happen; the feed that online prediction reads. Freshness vs. cost is decided here. |
| **Batch job** | Run over everything, on a schedule. | Nightly scoring, re-indexing a corpus, retraining, running the eval harness. The cheapest place for a model to live; the stalest. |
| **DAG / scheduler** | Orders jobs with dependencies; retries. | Retraining and re-indexing pipelines. Backfills are hard when features are time-dependent; every task must be idempotent because retries are certain. |

## Storing things

| Component | What it's for | Where it appears · what the model changes |
|---|---|---|
| **Key-value store** | Fast lookup by key; simple, durable. | Run state and **checkpoints** for an agent; the approval gate is a row in it. Session and user memory. The thing that survives when a function dies mid-run. |
| **Relational database** | Structured records, transactions, joins. | Accounts, orders, tickets — the *ground truth* the agent looks up through a tool. Never in the model's context wholesale; reached through a scoped capability. |
| **Object store / bucket** | Large files, cheap, durable. | The corpus, the index snapshot, **recorded fixtures**, traces, the golden set. "What is in the build" lives here. |
| **Cache** | Remember a computed answer. | Exact cache (same prompt → same output), prefix cache (shared system prompt), **semantic cache** (similar question → same answer — a correctness bug when similar questions have different answers). |
| **Index** | Find things by content: lexical, dense/vector, hybrid, structured. | Retrieval. The unit of retrieval is a design decision; access control happens *at retrieval time*, not in the prompt. Freshness = corpus/index consistency. |
| **Feature store** | One computation of a feature, served to training and serving alike. | The answer to *training/serving skew* — two code paths computing the same feature and drifting apart. A mechanism, not a product category. |

## Running things

| Component | What it's for | Where it appears · what the model changes |
|---|---|---|
| **Function (serverless)** | Run a small unit of code on demand; no server to manage; hard time limit. | One agent step. The time limit *is* process death — it's why checkpointing before every dispatch is not optional. |
| **Container** | Package code and its dependencies; run anywhere. | The model server; the capability server. Cold start of a model container is minutes, not seconds — readiness must mean "weights loaded and a golden probe passed." |
| **Model server** | Hosts weights, batches requests, exposes inference. | Capacity is *memory-shaped* (context length × batch); routing is *stateful* (prefix cache); replicas are not fungible. Or it's a vendor API — in which case the rate limit is your capacity and the vendor's update is your unplanned deploy. |
| **Accelerator pool** | GPUs/TPUs. | Scarce, non-burstable, expensive. You cannot autoscale out of a shortage; the queue and the cascade are how you spend it. |

## Edges and control

| Component | What it's for | Where it appears · what the model changes |
|---|---|---|
| **API gateway / load balancer** | One front door; spread load. | Fine for the app. Wrong for the model server if it's least-connections and the servers hold prefix caches. |
| **Rate limiter / quota** | Bound calls per tenant or key. | Your Gemini key *is* one. Also: bound the *consequence* — refunds per hour, actions per run. |
| **Circuit breaker / timeout / retry** | Stop hammering a failing dependency; bound waiting; try again. | Reinterpreted for a slow, nondeterministic callee: a retry is a *new sample*; retrying a consequential action needs an idempotency key. |
| **Policy engine / guardrail** | Check a request or output against rules. | Sits *outside* the model, in code, at the boundary. Structural checks (caps, scopes) work when the model is wrong; content checks are themselves models with error rates. |

## The AI-specific ones

| Component | What it is | Why it's a component and not an afterthought |
|---|---|---|
| **Orchestrator / agent loop** | The program around the model: render, sample, parse, validate, dispatch, append; budgets; checkpoint; resume; pause for a human. | It is the control flow. The model is not. Everything about durability, cost, and safety is decided here. |
| **Capability server (tool server)** | Exposes actions to the agent over a protocol: discovery, description, invocation, scope. | A tool's description is the model's only knowledge of the tool. The server is the authorization boundary; the harness holds credentials, the model never sees one. |
| **LLM client with record/replay** | Wraps the model call; records every live response as a fixture; replays deterministically. | How you reproduce a run, test in CI without quota, and pin "the build." |
| **Evaluation harness** | Golden set, slices, scorers, a validated judge, a noise floor, a blind-spot register. | The spec. The test suite. The regression gate. The single most important artifact in the system. |
| **Trace store** | Every run as a tree: context in, tokens out, parse, dispatch, result, cost, latency, model version, join key. | The only way to debug a sample. The audit record for a stochastic actor. The most sensitive data you hold. |
| **Human review queue** | Where the remainder goes. | A component with an error rate that *rises* as the model improves. Needs sampling, seeding, rotation, and its own metric. |
| **Vendor drift canary** | A pinned suite run on a schedule against the hosted model. | The only way to learn that the model changed under you. |

---

## The Operator, named

The scaffold you were handed uses exactly these, and nothing else:

**ticket simulator** → **queue** → **orchestrator + agent loop** on **functions** ↔
**capability server** (`lookup_account`, `issue_refund`, `escalate`) · **LLM client**
(record/replay) → **model** (vendor API) · **key-value store** (run state, checkpoints,
approvals) · **bucket** (fixtures, traces, corpus) · **evaluation harness** ·
**trace logger** · **human review queue** (the escalate route) · in Week 11, a second
**capability server** from a "vendor."

If you can say what each box is for and what the model changes about it, you have the
vocabulary this course needs. The rest is judgment, and that's what the studios are for.
