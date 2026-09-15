# P1 — Measure it

**Assigned Week 1 · Due before the Week 4 block · ~12 hours · Pairs · 10% of the grade**

> The triage step "works." Prove it, per slice, with a noise floor, and say what you
> cannot see.

The evaluation harness is the single most important artifact in this course. It is the
AI system's test suite, its specification, and its release gate at once. P1 is where you
build your first one, and everything you ship later is gated on it.

## The system you are measuring

The full Operator scaffold arrives with P2. For P1, the system under test is the triage
step you met in the Week 1 lab: the `triage()` function in
[feel-the-distribution](https://github.com/aise-stthomas/feel-the-distribution). It
renders a ticket and an account summary into a prompt, takes one sample from the
model, and parses the result into `{action, refund_amount, rationale}`. That is the
component. Everything you measure is about that component.

The lab's `slices.py` is the harness stub you are replacing: ten items, one aggregate
number, no noise floor, a judge that has never been validated. Start by copying the lab
repository into your pair's repository. Your harness will port to the Operator without
changes, because the Operator's output contract has the same shape.

## Deliver

1. **A golden set** of 50–100 tickets with expected outcomes. The ticket simulator
   arrives with the scaffold, so for P1 you write them: realistic tickets across the
   intents the policy covers, plus hand-authored adversarial and edge items. Map the
   coverage to the mitigation table from the Week 2 lab.
2. **Slices.** At minimum: by intent, by refund amount band relative to the caps, by
   whether the ticket contains an instruction addressed to the agent, and one slice of
   your own choosing that you expect to fail. Every number you report is per slice.
3. **Scorers** appropriate to each output type: exact match for the action and the
   amount; a rubric-based judge for the rationale text.
4. **A validated judge.** Agreement between your judge and your own human labels on at
   least 30 items, reported per slice, plus the judge's failure modes you found.
5. **The noise floor.** The same system run five times over the suite. Report the
   spread per slice and the smallest effect you could actually detect.
6. **The blind-spot register.** What this harness cannot see. Written, honest, short.
7. **A one-page design doc:** the seven-step brief for the Operator, using the
   [design review rubric](../supplemental/rubric.md). The
   [reference design](../supplemental/reference-design.md) is the exemplar. Do not
   copy it; the point is your reasoning about the trade-offs.

## Budget

- About 700 live calls: 100 items × 5 runs, plus judging. On the free tier that is
  roughly half an hour of wall time per full run, so plan runs rather than launching
  them casually.
- **Record every live call** to a fixture file and score from the fixtures. Re-scoring
  costs nothing; re-sampling costs quota.
- One key per student, in `.env`, never in a commit.

## Submit

- One GitHub repository per pair, private, with the instructor added as a
  collaborator. Tag it `p1` before the Week 4 block starts; the tag is what is graded.
- `README.md` says how to run the harness end to end from a clean clone.
- The fixtures, the per-slice report, the judge validation, the noise-floor numbers,
  the blind-spot register, and `design.md` are all in the repository.
- Both members must be able to explain any line of the submission in a five-minute
  live walkthrough.

## How it is graded

Coverage over size. The emphasis is on slices that expose what the aggregate hides, a
judge you validated rather than trusted, and an honest blind-spot register. A
hundred-item suite with one aggregate number scores below a fifty-item suite with
slices, a noise floor, and a register that admits what it misses.

## Pacing

About three hours a week for three weeks. Start with the golden set; the judge comes
last.

| Week | Do |
|---|---|
| 1–2 | Golden set and slices. Use the Week 2 lab's mitigation table to check coverage. |
| 2–3 | Scorers, fixtures, and the five-run noise floor. |
| 3–4 | Judge validation, the blind-spot register, the design doc, the tag. |
