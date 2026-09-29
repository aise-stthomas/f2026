# Week 3 — Homework
### 10 multiple-choice questions

> Questions 1–5 are on the lecture and the studio, 6–8 on the reading (Dean & Barroso,
> *The Tail at Scale*), and 9–10 on the lab. Assigned after the block, due before the
> next one.

---

**1.** Two versions of a system are run five times each over the same eight tickets.
Comparing run-by-run gives a 95% interval of +1.5 to +23.5 points; comparing
ticket-by-ticket gives −7 to +32. Both have the same mean. Which statement is correct?

- (a) The run-by-run interval is the right one; it is narrower, so it is more precise.
- (b) The interval belongs to the unit that was sampled. The tickets were sampled, so the
  ticket-by-ticket interval is the one that speaks about the slice, and it cannot tell.
- (c) The two intervals should be averaged.
- (d) The ticket-by-ticket interval is wrong because it includes negative values.

**2.** A team wants to detect a 5-point improvement on a slice before switching models.
Using the lecture's rule of thumb (two versions disagreeing on about a fifth of the
tickets), roughly how many tickets does that slice need for a 5-point gap to clear a
95% interval?

- (a) About 15.
- (b) About 60.
- (c) About 300.
- (d) The number of tickets does not matter; the number of runs does.

**3.** A judge agrees with your hand labels on 24 of 30 outputs (80%). Cohen's κ for the
same table is 0.44 with a 95% interval of roughly 0.05 to 0.84. What is the honest
conclusion?

- (a) The judge is validated: 80% agreement is high.
- (b) The judge is broken: κ below 0.5 means it should be discarded.
- (c) Thirty labels can show the judge is not broken outright, but cannot show it is
  good; the interval is too wide to say more.
- (d) κ should be ignored in favour of raw agreement.

**4.** In the lecture's cost grid, one call per ticket on a small model cost about \$380 a
month and a chain of four calls on a large model about \$58,000 a month, for the same
million tickets. Where does the 150× come from?

- (a) Only from the price difference between the two models.
- (b) From the number of tickets.
- (c) From two design choices multiplied together: a large model at about 25× the price per
  token, and a chain of calls whose growing context sends about 6× the tokens.
- (d) From output tokens alone.

**5.** In the studio, a design puts the refund cap in the language model's prompt and
lets the model decide whether a refund is allowed. Which objection follows from the
lecture?

- (a) The prompt would be too long.
- (b) The cap is a row in a table; a model asked to remember it will sometimes ignore it,
  and a decision a lookup can make belongs in code, where it is tested rather than
  measured.
- (c) The model cannot compare numbers.
- (d) The cap should be higher.

**6.** *(Reading)* Dean and Barroso's opening example: a server answers in under a second
99% of the time; a request fans out to 100 such servers and waits for all of them. What
fraction of requests take over a second?

- (a) About 1%.
- (b) About 10%.
- (c) About 63%.
- (d) About 99%.

**7.** *(Reading)* Which of these describes a *hedged request* as the paper defines it?

- (a) Send the request to every replica at once and take the first answer.
- (b) Send the request to one replica; if it has not answered within roughly the
  95th-percentile expected latency, send it to a second replica and use whichever
  answers first, cancelling the other.
- (c) Retry the request on the same replica after a timeout.
- (d) Route the request to the replica with the fewest open connections.

**8.** *(Reading)* The lecture applied the paper's tail-tolerant techniques to a chain of
model calls and noted one change you must make yourself. What was it, and why?

- (a) Hedging is impossible with a model because the model is nondeterministic.
- (b) A retried or hedged model call is a new sample; if the step has a consequence,
  such as a refund, the duplicate must be prevented with an idempotency key, because the
  action may otherwise run twice.
- (c) Model calls cannot be cancelled.
- (d) Tied requests do not work over HTTP.

**9.** *(Lab)* After moving the triage step behind a URL, you record three runs and score
them with the existing harness. The scores match the earlier local runs within the noise
floor. Why is that the expected result, and what does it show?

- (a) The function has a cache, so the outputs are identical.
- (b) The harness scores recorded outputs; where the model runs changes latency and cost
  but not the outputs' distribution, so the measurement is unchanged. The harness can gate
  any placement.
- (c) The URL forces temperature to zero.
- (d) It is a coincidence; three runs are not enough to say.

**10.** *(Lab)* With the function's time limit set below the model's latency, calls die
while the model is still generating. What should the harness record for such a call?

- (a) Nothing; a call that did not complete is not a sample.
- (b) A retry's result in its place, silently.
- (c) A record that the call failed, with the elapsed time and the reason, counted as a
  failure in the rate for that slice, because process death under a time limit is a
  normal event the design has to survive.
- (d) The last partial token stream, scored as if complete.
