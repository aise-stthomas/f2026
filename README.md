# AI Systems Engineering
### University of St. Thomas · Fall 2026

**Instructor:** Jim Howard · **Format:** one 3-hour block per week, hybrid, 14 weeks ·
**Day, time, and room:** to be announced

[Schedule](#schedule) · [Syllabus](syllabus.md) · [Supplemental material](supplemental/) ·
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
**Workload cap:** five hours a week outside class. **Cost to you:** nothing; projects
run on a course-provided AWS Academy Learner Lab account and the Gemini free tier.
The full policies are in the [syllabus](syllabus.md).

## How a week works

| Segment | What happens |
|---|---|
| Failure of the Week | One real AI system failure, analyzed against the design framework. |
| Lecture | About seventy minutes, with a break. |
| Design studio | Design in pairs, then review as a group against the rubric. |
| Lab | Read-and-run, finishable in the hour, independent of the projects. |
| Homework | Ten multiple-choice questions on the studio and the lab, due before the next block. |

## Schedule

Links appear as material is released. Remaining dates will be filled in as the semester proceeds.

| Wk | Date | Topic | Slides | Design studio | Lab | Reading | Project |
|---|---|---|---|---|---|---|---|
| 1 | Tue, Sep 15 | Why AI systems fail differently | [slides](lectures/week-01/slides.pdf) | [Design a system that flags fraudulent transactions](lectures/week-01/studio.pdf) | [Feel the distribution](https://github.com/aise-stthomas/feel-the-distribution) | Sculley et al., *Hidden Technical Debt in ML Systems*; Zinkevich, *Rules of Machine Learning* | Scaffold running end to end. P1 assigned. |
| 2 | Tue, Sep 22 | Requirements, risk, and designing for mistakes | | | | Kaestner, *Machine Learning in Production*, risk chapters; NIST AI RMF core (skim) | |
| 3 | | Architecture and the trade space | | | | Huyen, *Designing ML Systems*, architecture chapters; Dean & Barroso, *The Tail at Scale* | |
| 4 | | Data as specification, and retrieval | | | | Sambasivan et al., *Data Cascades*; Breck et al., *The ML Test Score* | P1 due. P2 assigned. |
| 5 | | Evaluation I: offline | | | | Ribeiro et al., *CheckList*; D'Amour et al., *Underspecification* | |
| 6 | | Evaluation II: online, and shipping a change | | | | Kohavi, Tang & Xu, *Trustworthy Online Controlled Experiments* (selected) | |
| 7 | | Serving, inference economics, and scheduling on scarce capacity | | | | Google SRE Book: handling overload, cascading failures | P2 due. |
| 8 | | Checkpoint exam and project clinic | | | | | P3 assigned. |
| 9 | | Agents I: the mechanism | | | | Anthropic, *Building Effective Agents*; a provider's tool-use API, read as a wire format | |
| 10 | | Agents II: state, orchestration, and delegation | | | | | |
| 11 | | The integration and trust layer | | | | The capability-protocol specification the scaffold uses, read as a primary source | P3 due. P4 assigned. |
| 12 | | Security and safety for agentic systems | | | | Willison on prompt injection and the lethal trifecta; OWASP Top 10 for LLM Applications | |
| 13 | | Operating a live AI system | | | | | P4 due. Repository tagged. |
| 14 | | Demos and final | | | | | |

## Supplemental material

| Document | What it is |
|---|---|
| [Course thesis](supplemental/course-thesis.md) | What is different about AI systems, and the gate every topic must pass. |
| [Design review rubric](supplemental/rubric.md) | Seven steps by four levels. The standard the studios and both exams grade against. |
| [Reference design](supplemental/reference-design.md) | The Operator through all seven steps at level 4. |
| [Common system components](supplemental/common-system-components.md) | A one-page vocabulary for AI system designs. |
| [Projects, scaffold, and budgets](supplemental/projects.md) | The four cumulative pair projects and the budgets. |

## Projects

Four cumulative pair projects on one scaffolded system, about twelve hours each. Each
ships with the reference solution to the previous one. Specifications are in the
[projects document](supplemental/projects.md).

| Project | Assigned | Due | You replace or add |
|---|---|---|---|
| P1 — Measure it | Wk 1 | Wk 4 | Eval harness: golden set, slices, noise floor, validated judge, blind-spot register |
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
