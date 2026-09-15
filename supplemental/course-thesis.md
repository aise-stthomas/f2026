# Course Thesis: What Is Actually Different About AI Systems

This document is the editorial spine of AI Systems Engineering. Every lecture, lab,
and assessment is checked against it. If a topic cannot be justified here, it does
not belong in the course.

---

## The one-sentence thesis

> Classic software engineering assumes components meet a specification. AI components
> are stochastic, their specification lives in data rather than code, they drift
> without anyone editing them, and they now take actions in the world. AI Systems
> Engineering is the discipline of building reliable systems out of parts like that.

---

## The editorial gate

**Every topic must answer: what is different about this because there is a model in
the loop?**

If the answer is "nothing," it is a prerequisite, not a lecture. It goes in the
Systems Toolkit primers (self-paced, outside class) or gets cut.

This gate exists because the most common failure mode for a course like this is
quietly becoming *DevOps with an ML flavor* — a commodity subject, already known to
half of a working-professional audience, and one that dates fast. Infrastructure is
not banned. It has to earn its slot.

Worked examples of the gate:

| Topic | Passes? | What the model changes |
|---|---|---|
| Message queues | Yes | Inference is slow, expensive, rate-limited, and capacity-scarce. Backpressure is not optional. |
| Container orchestration | Narrowly | 30GB cold starts, GPU node pools that cannot burst, autoscaling that cannot fix a queue. Reading a manifest, not operating a cluster. |
| DAG orchestrators | Yes, briefly | Retraining is a DAG; backfills are hard when features are time-dependent; retries demand idempotency. Concept in lecture, tool recognition in lab. |
| Tracing | Yes, fully | "A request" is undefined when one user turn becomes forty tool calls. Latency is bimodal by task type. This is close to an open problem. |
| CI/CD pipelines | Only the AI part | Configuring a runner: prerequisite. Gating a merge on an eval score against a measured noise floor: core content. |
| REST API design | No | Prerequisite. |
| Git branching | No | Prerequisite. |

---

## The five differences

### 1. The component is stochastic, not buggy

In classic engineering a defect is a discrete thing you locate and fix. An AI
component has a *distribution* of behavior. There is no fix — only a shifted
distribution, and the shift may regress cases that previously worked.

What changes in practice:

- **Testing becomes evaluation.** A test is boolean and deterministic. An eval is a
  statistic over a sampled suite, with a confidence interval. "It worked on my
  example" is not evidence.
- **Every requirement becomes a rate.** Not "the system classifies correctly" but
  "the system is correct at a rate of X on slice Y, and here is what happens on the
  remainder."
- **Flakiness is the medium, not a bug.** The same input can yield different outputs.
  You must know your noise floor before you can claim a regression.
- **The system must be correct while the component is wrong.** The component will be
  wrong at some rate, forever. Guardrails, confidence gating, fallbacks, human review,
  and undo are not polish — they are where correctness actually comes from.
- **Your guardrail is also a model.** The mitigation for a stochastic component is
  frequently another stochastic component, with its own error rate, its own slices, and
  its own drift. Safety mechanisms need evaluation harnesses too. This recursion is not
  a paradox; it is the job.

### 2. The specification lives in data, not code

Behavior is induced from data rather than written down. The "spec" for an LLM system
is distributed across the prompt, the retrieved context, the tool descriptions, and a
set of weights you very likely do not control.

What changes in practice:

- A data defect and a code defect are the same class of problem, and only one of them
  shows up in a diff.
- Training/serving skew is a **distributed-systems consistency problem** wearing a
  data hat: two code paths computing the same transformation, drifting apart.
- Data needs the things code has — versioning, contracts, review, provenance, tests —
  and mostly does not have them.
- Outputs become tomorrow's inputs. Feedback loops are a structural property of the
  system, not an edge case.

### 3. The system is non-stationary, and partly not yours

Classic software is stationary unless you change it. An AI system decays with no edit
at all: the world drifts, users adapt to the system, and your model vendor silently
changes the weights under you.

This is why observability, drift detection, and continuous evaluation are not
add-ons. They are the only thing standing between you and a system that is quietly
worse than it was in March.

### 4. Familiar engineering tools are used differently

This is where most of the course's value sits for practitioners. The tool names are
familiar; the practice is not.

| Classic practice | AI systems practice |
|---|---|
| Unit test | Eval harness: sampled suite, slices, statistic, confidence interval |
| Assertion | Runtime guardrail on probabilistic output |
| CI gate: "tests pass" | CI gate: "eval did not regress beyond the measured noise floor" |
| Version the code | Version the *artifact*: code + prompts + model version + index snapshot + tool definitions + eval suite |
| Types and interfaces | Schemas and tool contracts consumed by a nondeterministic caller that may ignore them |
| Logging | Tracing a tree of nondeterministic steps with unbounded depth |
| Step-through debugging | Slicing, distribution comparison, and reproduction under sampling |
| Code review | Code review + eval-suite review; the suite is the real spec |
| SLOs on latency and availability | Quality becomes an SLO dimension, measured statistically |
| Rollback the deploy | Roll back to *which* combination of prompt, model, and index? |

**Evaluation harnesses are the single most important artifact in the course.** They
are the AI system's equivalent of a test suite, a spec, and a regression gate at
once, and building a good one is a skill almost nobody is taught.

### 5. Agent engineering is genuinely new

Not a rebranding. The commitments below are non-negotiable: this course teaches the
mechanism, not the framework. "Just call LangChain" is precisely the understanding
that leaves an engineer unable to debug, cost-control, or secure the result.

**What an LLM actually does in an agent.** One thing: given a token sequence, produce
a probability distribution over the next token, from which one is sampled. It does
not call anything. It does not decide anything in a persistent sense. It has no
memory between invocations. It does not run a loop. **Everything else is your
program.**

**What a tool call actually is.** Tool descriptions are placed into the context as
tokens, via a template. The model emits tokens that, per a structured format, name a
tool and its arguments. *Your harness* parses those tokens, dispatches a real function
call, and appends the result back into the context as more tokens. Then you invoke
the model again on the now-longer context.

Consequences students must be able to derive themselves:

- The agent loop is your program's control flow, not the model's.
- The model can emit a call to a tool that does not exist, or with malformed
  arguments. This is not an exception condition; it is a sample from a distribution.
  Validate at the boundary — there is no type checker.
- Every step is a full network round trip, and the context grows monotonically. Cost
  and latency scale superlinearly in the number of steps.
- A tool's description is the model's *only* knowledge of that tool. Tool description
  writing is interface design for a nondeterministic reader.
- Error messages returned to the model are its only feedback channel. Error text is a
  user interface, and the user is a language model.
- "The model chose to use the tool" is an anthropomorphism for "the sampled tokens
  matched the tool-call grammar."

**The integration problem, stated generally.** Before any protocol exists: an agent
needs to use capabilities it was not hard-coded against. That decomposes into six
problems, and every protocol in this space answers some subset.

1. **Discovery** — how does the agent learn what exists at all?
2. **Description** — how is a capability described to a *model* reader? Not just the
   syntax of the call, but when to use it, what it means, and what it costs. OpenAPI
   describes syntax to a programmer who already knows the semantics. An agent has only
   the description.
3. **Invocation** — the wire mechanics of actually calling it.
4. **Contract and versioning** — what happens when the capability set changes, possibly
   mid-session.
5. **Authorization** — who is acting, on whose behalf, with what scope, and who
   consented.
6. **Session and direction** — long-lived state, progress, streaming, and the server
   initiating calls back to the client.

**Why RPC rather than REST.** REST is resource-oriented: you model state and transition
it. Agents are verb-oriented — "issue a refund" is a procedure, not a resource state
transition, and forcing it into a resource model inserts a translation layer the model
has to reason through. RPC gives a named, parameterized, schema-typed procedure, which
is the exact shape that renders into a tool description. And REST is unidirectional,
while an agent session needs the server to initiate: progress notifications, streamed
partial results, elicitation of further input, and sampling requests back to the model.
A JSON-RPC session over a persistent transport provides that; request/response REST does
not. MCP is JSON-RPC 2.0 for these reasons, and students should be able to derive that
choice rather than be told it.

**MCP and A2A as implementations.** MCP answers the six problems for *agent →
capability*. A2A answers them for *agent → agent*, which is a genuinely different
problem shape: the remote party is opaque and stochastic rather than a deterministic
tool, tasks are long-running and need a lifecycle rather than a return value, and you
are delegating a goal rather than invoking a function. Teach the six problems as the
durable content. Teach these two as the current answers. Expect the answers to change
within the life of this course and the problems not to.

**Authorization is the hard part, and it is not solved.** Classic API auth has two
parties. An agent has three: the end user, the agent acting on their behalf, and the
resource server. Nearly everything difficult follows from that one fact.

- **Ambient authority is how injection becomes privilege.** If the agent holds a broad
  token, an attacker who controls one retrieved document controls everything that token
  can reach. Least privilege is not hygiene here — it is the primary containment
  mechanism, and it is the thing that limits blast radius when injection succeeds.
- **Scope granularity does not match intent.** OAuth expresses "read email." What you
  actually want to authorize is "read *this thread* for *this task*." Capability-based
  security and attenuated tokens are the conceptual answer; most real deployments do not
  have it, and students should understand the gap they are living with.
- **Consent belongs at the moment of consequence,** not at connection time. A
  human-in-the-loop approval gate is an authorization primitive, not a UX nicety.
- **Credentials must never enter the context.** This follows directly: context is data
  the model can be induced to emit, so a secret in context is a secret already leaked.
  The harness holds credentials; the model names a tool and never sees a key. Students
  should derive this rule, not memorize it.
- **Audit and non-repudiation when the actor is stochastic** — who did what, on whose
  behalf, and can you demonstrate it afterward.
- **Token lifetime and revocation** for an agent that runs for thirty minutes, or three
  days, or until a human answers.

---

## Anti-goals

This course is **not**:

- A DevOps or platform engineering course
- A Kubernetes, Airflow, or cloud-vendor course
- A modeling, deep learning, or prompt engineering course
- A framework tutorial (LangChain, LlamaIndex, or successors)
- An AI ethics survey course — responsible-AI concerns are taught as design
  constraints where they bind, not as a separate module

Models appear only where model behavior changes a system decision. Tools appear only
as one concrete instance of a general mechanism, and the lab says so out loud.

---

## Depth commitments

Topics the course will teach at mechanism level, never by analogy or hand-wave:

1. Where stochasticity actually comes from (sampling), and why temperature is a
   systems parameter, not a personality setting
2. What a tool call is, byte by byte, from context template to parsed dispatch
3. What MCP adds over an API, and what it does not solve
4. What an eval harness contains, and how to establish a noise floor
5. What a trace of an agent run looks like and why spans nest unboundedly
6. Why training/serving skew happens, structurally
7. The context window as the real state machine of an agentic system
8. Why retrieval quality, not model quality, is usually the bottleneck in RAG
9. Why an agent's authorization problem has three parties, and why credentials never
   enter the context
10. Why candidate generation → ranking → re-ranking is the same architecture whether you
   call it a recommender or a RAG pipeline
