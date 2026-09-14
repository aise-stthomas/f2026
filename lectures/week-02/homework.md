# Week 2 — Homework
### 10 multiple-choice questions · ~20 minutes

> Questions 1–7 are on the lecture and the studio; 8–10 require the lab. Assigned after
> the block, due before the next one.

---

**1.** A screening system's only stated requirement is "false-reject rate ≤ 3%." Which
component of a requirement is missing, and what does its absence hide?

- (a) The owner; it hides who to call.
- (b) The slice; it hides a disparity between populations that a single aggregate
  averages away.
- (c) The latency budget; it hides slowness.
- (d) The training data; it hides bias in labels.

**2.** A team adds a human reviewer who sees every application the model advances.
After two months the reviewer approves 99.6% of the model's rankings. Which is the
best characterization?

- (a) The model has become highly accurate.
- (b) The reviewer is a working control; 99.6% agreement shows alignment.
- (c) Automation bias: the reviewer has stopped being a control, and the design needs a
  mechanism — seeding, sampling, trace-not-verdict — to keep them reading.
- (d) The reviewer should be replaced with a second model.

**3.** Which mitigation works *even when the model is completely wrong*?

- (a) A fairness classifier on the model's output
- (b) A confidence gate
- (c) The model cannot emit "reject"; only a human can
- (d) A larger, more diverse training set

**4.** Two systems have the same error rate. In one, a wrongly flagged person is told and
can appeal within days; in the other they are never told. Why is the second mistake
more severe?

- (a) It is more frequent.
- (b) Severity includes reversibility and recourse; a mistake nobody can contest is
  effectively irreversible.
- (c) It costs more to compute.
- (d) It isn't — severity depends only on the error rate.

**5.** The sepsis model in the Failure of the Week had a vendor-reported AUC of
0.76–0.83 and a measured AUC of 0.63. What was the *most* important finding of the
validation?

- (a) The AUC was lower than claimed.
- (b) At the recommended threshold the model missed two-thirds of sepsis cases while
  alerting on 18% of all patients — an operating point nobody had chosen.
- (c) The model was trained on the wrong population.
- (d) The hospitals had misconfigured the software.

**6.** Which statement best expresses Ashby's law of requisite variety as it applies to
guardrails?

- (a) A guardrail must be trained on the same data as the model.
- (b) A controller can only control a system whose variety it can match; a ten-state
  keyword filter cannot cover an unbounded output space.
- (c) Guardrails should be simpler than the model they guard.
- (d) Every guardrail must be a regular expression.

**7.** In the Zillow Offers case, what made the *severity* of a wrong prediction so
high?

- (a) The model's error rate was unusually large.
- (b) Each overestimate became an irreversible purchase at the model's price — cost ×
  irreversibility × blast radius — and the purchases fed back into the comps.
- (c) Zillow used the wrong evaluation metric.
- (d) The predictions were too slow.

**8.** *(Lab)* In your mitigation table, which row had a rate of zero, and why is it an
invariant rather than a rate?

- (a) Wrong refund amount — because refunds are reversible.
- (b) Unauthorized refund — because it is enforced by the harness and the cap in code,
  not by the model's behavior, so it is a property you require to hold always.
- (c) Late resolution — because latency is measured in seconds.
- (d) Escalation — because humans handle it.

**9.** *(Lab)* Which column of the seven-column mitigation table was mandatory even
when the honest entry was "we cannot detect this"?

- (a) Frequency
- (b) Owner
- (c) The mitigation's failure mode
- (d) Slice

**10.** *(Lab)* Which of the eight mistake kinds describes "the agent refunds the
correct amount because the ticket instructed it to, not because policy did"?

- (a) Wrong target
- (b) Wrong confidence
- (c) Late answer
- (d) Right answer for the wrong reason

