# Week 2 — Homework
### 10 multiple-choice questions

> Questions 1–5 are on the lecture and the studio, 6–8 on the reading (Miller, *Adding
> Error Bars to Evals*), and 9–10 on the lab. Assigned after the block, due before the
> next one.

---

**1.** A support system's only stated requirement is "wrong refund amount ≤ 1%." Read as a
requirement in the form taught this week, what is missing?

- (a) Nothing; a rate is a requirement.
- (b) The slice, the remainder policy, and an owner: on which tickets, what happens to the
  1% that are wrong, and who signed for that number.
- (c) The model's accuracy on a benchmark.
- (d) The training data.

**2.** The sepsis model was validated by its vendor at an AUC of 0.76–0.83 and measured by
Michigan Medicine at 0.63 on their own patients, missing two-thirds of sepsis cases at the
recommended alert setting. Which lesson does the case support?

- (a) The vendor's number was fraudulent.
- (b) AUC is the wrong metric for clinical models.
- (c) A number measured elsewhere, at a different setting, on different patients, is not a
  measurement of the system you run; measure it where you run it.
- (d) The model needed more training data.

**3.** A requirements table includes the line "unauthorized refund = 0." Which statement
is correct?

- (a) It can be verified by running the harness enough times to observe zero failures.
- (b) It should be relaxed to a small rate, since no system is perfect.
- (c) No sample can show a rate of zero; a zero is an invariant that must be enforced in
  the code around the model, not measured in the model.
- (d) It belongs in the prompt so the model knows the rule.

**4.** A harness runs a suite and reports "refund-over-cap: 4 of 6 passed (67%)." A
requirement says ≥ 80%. What can you conclude?

- (a) The requirement is not met.
- (b) The requirement is met, within rounding.
- (c) Nothing yet: with six items the 95% interval runs from about 30% to 90%, which
  includes 80%. The slice needs more tickets.
- (d) The scorer is wrong.

**5.** In the studio, a design proposed measuring a résumé screener's false-reject rate
using résumés of people the company had previously hired. What is the problem?

- (a) Hired employees' résumés are confidential.
- (b) The labels exist only where the old process said yes; a model evaluated on them can
  look excellent while repeating every past rejection. The sample must include applications
  the old process rejected, labelled blind.
- (c) There are too few of them.
- (d) Nothing; hired employees are by definition qualified.

**6.** *(Reading)* Miller argues that eval questions should be treated as a random sample
from an unseen "super-population," and that results should be reported with a standard
error. What is the practical consequence for a harness with a 60-ticket slice?

- (a) The reported pass rate is exact for those 60 tickets and needs no interval.
- (b) The pass rate is an estimate of performance on tickets like these; it should carry an
  interval, and the interval narrows with the square root of the number of tickets.
- (c) Sixty is always enough.
- (d) The tickets should be replaced with a larger public benchmark.

**7.** *(Reading)* Miller shows that when eval questions are grouped, such as several
questions drawn from the same passage, the naive standard error can understate the true
uncertainty by a factor of three or more, and recommends clustered standard errors. Which
situation in your own harness is the same problem?

- (a) Scoring the action and the amount separately.
- (b) Running the same ticket five times and counting the five results as five independent
  trials.
- (c) Tagging one ticket with two slices.
- (d) Using a model judge for the rationale.

**8.** *(Reading)* To compare two versions of a system, Miller recommends computing the
difference on each question and taking the standard error of those paired differences,
rather than comparing two independent averages. Why does this help?

- (a) It needs fewer questions to reach the same confidence because it removes the shared
  question-difficulty variance from the comparison.
- (b) It makes the two versions' scores identical.
- (c) It removes the need for a noise floor.
- (d) It only works when the two versions are the same model.

**9.** *(Lab)* You recorded three runs of eight tickets and, on one slice, saw pass rates
of 75%, 63%, and 88%. Nothing changed between runs. What is this spread, and what does it
tell you?

- (a) A bug in the recorder, since nothing changed.
- (b) The slice's noise floor: how much the number moves by itself. A later change smaller
  than this spread is not evidence of anything.
- (c) The model's accuracy, averaged.
- (d) Evidence that the second run was worse.

**10.** *(Lab)* The template's `record.py` saves every model output to a file, and
`score.py` reads only those files. Why is the harness split that way?

- (a) So that scoring is free and repeatable: scorers can be rewritten and rerun without
  spending calls, against outputs that hold still.
- (b) Because the model cannot be called twice.
- (c) So that the golden set can be regenerated by the model.
- (d) To keep the API key out of the scorer.

