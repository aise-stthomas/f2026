# AI Systems Engineering
### University of St. Thomas · Fall 2026

**Instructor:** Jim Howard · **Format:** one 3-hour block per week, hybrid, 14 weeks ·
**Day, time, and room:** to be announced

[Schedule](#schedule) · [Reading](#reading) · [Syllabus](syllabus.md) · [Supplemental material](supplemental/) ·
[Course site](https://aise-stthomas.github.io/) · [GitHub](https://github.com/aise-stthomas)

---

Most AI projects fail somewhere between the demo and the second month in production.
Not because the model was bad, but because the system around it was built on
assumptions that classic software engineering takes for granted and AI systems violate.

This course is about the system, not the model. You will measure, ship, make agentic,
break, secure, and operate one live system over the semester, a support-ticket triage
and resolution agent, replacing its parts with your own as the course teaches them. You
will also learn to design an AI system out loud, under time pressure, against an
explicit rubric. Every topic has to answer one question: *what is different about this
because there is a model in the loop?*

**Who it is for.** Working professionals in a master's program. Prerequisites:
comfortable Python, the command line, you have trained or called a model, you can read
an HTTP API. No cloud, container, or distributed-systems background required.

## How a week works

| Segment | What happens |
|---|---|
| Failure of the Week | One real AI system failure, analyzed against the design framework. |
| Lecture | About seventy minutes, with a break. |
| Design studio | Design in pairs, then review as a group against the rubric. |
| Lab | Read-and-run, finishable in the hour, independent of the projects. |
| Homework | Ten multiple-choice questions on the studio and the lab, due before the next block. |

## Schedule

| Wk | Date | Topic | Slides | Design studio | Lab | Reading | Project |
|---|---|---|---|---|---|---|---|
| 1 | Tue, Sep 15 | Why AI systems fail differently | [slides](lectures/week-01/slides.pdf) | [Design a system that flags fraudulent transactions](lectures/week-01/studio.pdf) · [answers](lectures/week-01/studio-answers.pdf) | [Feel the distribution](https://github.com/aise-stthomas/feel-the-distribution) | [reading](#reading-week-1) | Before Week 2: [AWS setup checklist](supplemental/configuring-aws.md). |
| 2 | Tue, Sep 22 | Requirements, and measuring AI systems | [slides](lectures/week-02/slides.pdf) | [A résumé screener rejected a qualified candidate](lectures/week-02/studio.pdf) | [Start Project 1: your first harness](https://github.com/aise-stthomas/measure-it) | [reading](#reading-week-2) | P1 assigned: Measure it. |
| 3 | | Architecture and the trade space | | | | [reading](#reading-week-3) | |
| 4 | | Data as specification, and retrieval | | | | [reading](#reading-week-4) | P1 due. P2 assigned. |
| 5 | | Evaluation I: offline | | | | [reading](#reading-week-5) | |
| 6 | | Evaluation II: online, and shipping a change | | | | [reading](#reading-week-6) | |
| 7 | | Serving, inference economics, and scheduling on scarce capacity | | | | | P2 due. |
| 8 | | Checkpoint exam and project clinic | | | | [reading](#reading-week-8) | P3 assigned. |
| 9 | | Agents I: the mechanism | | | | | |
| 10 | | Agents II: state, orchestration, and delegation | | | | [reading](#reading-week-10) | |
| 11 | | The integration and trust layer | | | | [reading](#reading-week-11) | P3 due. P4 assigned. |
| 12 | | Security and safety for agentic systems | | | | | |
| 13 | | Operating a live AI system | | | | | P4 due. Repository tagged. |
| 14 | | Demos and final | | | | | |

## Reading

Assigned at the end of each block, for the following week.

### Week 1 — Why AI systems fail differently
{: #reading-week-1 }

- Sculley et al., *Hidden Technical Debt in Machine Learning Systems*
- Kaestner, *Machine Learning in Production*, Ch. 6 *Gathering Requirements*, Ch. 7 *Planning for Mistakes*, Ch. 27 *Safety*
- NIST AI RMF core (skim)

### Week 2 — Requirements, and measuring AI systems
{: #reading-week-2 }

- Miller, *Adding Error Bars to Evals* ([arXiv 2411.00640](https://arxiv.org/abs/2411.00640))
- Huyen, *Designing ML Systems*, architecture chapters
- Dean & Barroso, *The Tail at Scale*

### Week 3 — Architecture and the trade space
{: #reading-week-3 }

- Sambasivan et al., *Data Cascades*
- Breck et al., *The ML Test Score*

### Week 4 — Data as specification, and retrieval
{: #reading-week-4 }

- Ribeiro et al., *CheckList*
- D'Amour et al., *Underspecification*

### Week 5 — Evaluation I: offline
{: #reading-week-5 }

- Kohavi, Tang & Xu, *Trustworthy Online Controlled Experiments* (selected)

### Week 6 — Evaluation II: online, and shipping a change
{: #reading-week-6 }

- Google SRE Book: handling overload, cascading failures

### Week 8 — Checkpoint exam and project clinic
{: #reading-week-8 }

- Anthropic, *Building Effective Agents*
- a provider's tool-use API, read as a wire format

### Week 10 — Agents II: state, orchestration, and delegation
{: #reading-week-10 }

- The capability-protocol specification the scaffold uses, read as a primary source

### Week 11 — The integration and trust layer
{: #reading-week-11 }

- Willison on prompt injection and the lethal trifecta
- OWASP Top 10 for LLM Applications

## Supplemental material

| Document | What it is |
|---|---|
| [Course thesis](supplemental/course-thesis.md) | What is different about AI systems, and the gate every topic must pass. |
| [Common system components](supplemental/common-system-components.md) | A one-page vocabulary for AI system designs. |
| [Configuring AWS](supplemental/configuring-aws.md) | The Learner Lab account: getting in, the usage alarms, the rules, and using it for development. |

## Projects

Four cumulative pair projects on one scaffolded system, about twelve hours each. Each
ships with the reference solution to the previous one. Specifications are in the
[projects document](https://aise-stthomas.github.io/projects) on the course site.

| Project | Assigned | Due | You replace or add |
|---|---|---|---|
| P1 — Measure it | Wk 2 | Wk 4 | Eval harness: golden set, slices, noise floor, validated judge, blind-spot register |
| P2 — Ship it | Wk 4 | Wk 7 | Eval gate in CI, canary with kill switch, cost model, telemetry |
| P3 — Make it act | Wk 8 | Wk 11 | Your own agent loop and orchestrator over the capability server |
| P4 — Break it, run it | Wk 11 | Wk 13 | Hardening after the red team, SLOs, tracing, game-day postmortem |

## Assessment

| Component | Weight |
|---|---|
| Weekly homework | 10% |
| Projects (4) | 40% |
| Checkpoint exam (Week 8) | 20% |
| Final exam (Week 14) | 15% |
| Demo and defense (Week 14) | 15% |

Policies on late work, AI tools, collaboration, and cost are in the [syllabus](syllabus.md).
