# Week 3 — Homework
### 15 multiple-choice questions

> Questions 1–11 are on the lecture and the studio, 12–13 on the reading (Dean & Barroso,
> *The Tail at Scale*), and 14–15 on the lab. Assigned after the block, due before the
> next one.

---

**1.** Two versions of a system are run five times each over the same eight tickets.
Comparing run-by-run gives a 95% interval of +1.5 to +23.5 points; comparing
ticket-by-ticket gives −7 to +32. Both have the same mean. Which statement is correct?

- (a) The run-by-run interval; it is narrower, so it is the more precise of the two.
- (b) The run-by-run interval; the runs were the unit sampled, so it speaks about the
  slice.
- (c) Both; the two intervals should be averaged, since they share a mean.
- (d) The ticket-by-ticket interval; the tickets were the unit sampled, and it cannot
  tell.

**2.** A team wants to detect a 5-point improvement on a slice before switching models.
Using the lecture's rule of thumb (two versions disagreeing on about a fifth of the
tickets), roughly how many tickets does that slice need for a 5-point gap to clear a
95% interval?

- (a) About 300.
- (b) About 60.
- (c) About 15.
- (d) The number of tickets does not matter; the number of runs does.

**3.** A judge agrees with your hand labels on 24 of 30 outputs (80%). Cohen's κ for the
same table is 0.44 with a 95% interval of roughly 0.05 to 0.84. What is the honest
conclusion?

- (a) The judge is validated: 80% agreement is high and κ is positive.
- (b) The judge is not shown broken, but not shown good either; the interval is too
  wide.
- (c) The judge is broken: κ below 0.5 is the usual cutoff for discarding it.
- (d) The judge is validated: κ can be ignored when raw agreement is this high.

**4.** In the lecture's cost grid, one call per ticket on a small model cost about \$380 a
month and a chain of four calls on a large model about \$58,000 a month, for the same
million tickets. Where does the 150× come from?

- (a) From the price difference between the two models alone.
- (b) From the number of tickets, which multiplies whatever each one costs.
- (c) From the output tokens, which are priced four times higher than input.
- (d) From the model price and the token count multiplied: about 25× and about 6×.

**5.** The support desk's requirement is a confirmation instantly and an answer within
an hour. The language model call takes seconds. Following the lecture's order of
questions for choosing a placement, where does the language model call go?

- (a) Off the request, queued; nothing that must be instant has a model in its path.
- (b) In the request, synchronous; the customer is waiting for the answer.
- (c) Precomputed nightly for every account; nobody is waiting on it then.
- (d) Streamed to the browser; the first token arrives within the budget.

**6.** In the studio's design, the request handler does three things before the customer
hears anything: it writes the ticket row, pushes the ticket id onto the queue, and
returns 202. A teammate proposes returning the 202 first, so the confirmation is faster,
and writing the row afterwards. What is wrong with that?

- (a) Nothing; the two orders differ by microseconds, and the queue still holds the
  ticket id either way.
- (b) The queue push must come before the 202, but the row can be written afterwards.
- (c) A process dying between the reply and the write loses a ticket the customer was
  told is recorded.
- (d) HTTP forbids sending a response before the database write behind it has committed.

**7.** In the studio's design, the classifier scores every ticket in milliseconds before
the language model sees any of them. What is the score for?

- (a) It writes the reply for template tickets, so the language model sees fewer.
- (b) It checks the proposed refund against the cap before the reply goes out.
- (c) It judges whether the language model's proposed action was correct.
- (d) It orders the queue, and can decide which tickets the language model sees at all.

**8.** On the bad Monday, 240 tickets a minute arrive for an hour and then 24 a minute;
the model serves 25 a minute. The queue is in place. Which statement is correct?

- (a) Every ticket is acknowledged and answered, but about 13,000 are waiting at the end
  of the hour.
- (b) The model serves 240 a minute for the hour, since the queue smooths the arrivals.
- (c) Nothing changes from the design without a queue; the customers still time out.
- (d) The backlog clears within minutes once arrivals fall back to 24 a minute.

**9.** In the cascade, a classifier flags money tickets for the large model and sends the
rest to rules. On a labelled set the classifier flags 90% of the money tickets, and the
large model gets 99% of what it reads right. Roughly what rate can the system reach on
money tickets, and where do the rest go?

- (a) About 99%: the large model decides every money ticket it is given, and it is given
  them all.
- (b) About 89%: the classifier caps the slice, and its misses leave as template
  answers.
- (c) About 95%: the two stages' rates average out over the slice.
- (d) It cannot be computed without knowing the rules' accuracy on the tickets they
  answer.

**10.** In the studio, the language model is unavailable for an hour. What does a strong
design do?

- (a) Show an error page to customers submitting tickets until the model is back.
- (b) Hold every ticket in the queue and answer nothing until the model is back.
- (c) Have the classifier draft the replies, since it has a score for every ticket.
- (d) Confirm as usual; rules answer what they can and people get the rest.

**11.** In the studio, a design puts the refund cap in the language model's prompt and
lets the model decide whether a refund is allowed. Which objection follows from the
lecture?

- (a) The prompt would be too long for the model to read reliably with the cap in it.
- (b) The cap is a row in a table, and a lookup is tested where a model is measured.
- (c) The model cannot compare the amount with the cap reliably.
- (d) The cap should be set higher, so the model rarely has to apply it in practice.

**12.** *(Reading)* Dean and Barroso's opening example: a server answers in under a second
99% of the time; a request fans out to 100 such servers and waits for all of them. What
fraction of requests take over a second?

- (a) About 1%.
- (b) About 10%.
- (c) About 63%.
- (d) About 99%.

**13.** *(Reading)* The lecture applied the paper's tail-tolerant techniques to a chain of
model calls and noted one change you must make yourself. What was it, and why?

- (a) A retried call is a new sample; a step with a consequence needs an idempotency
  key.
- (b) Hedging is impossible with a model, because two calls never return the same
  answer.
- (c) Model calls cannot be cancelled once the request has been sent to the vendor.
- (d) Tied requests do not work over HTTP, so only plain retries apply to a model call.

**14.** *(Lab)* After moving the triage step behind a URL, you record three runs and score
them with the existing harness. The scores match the earlier local runs within the noise
floor. Why is that the expected result, and what does it show?

- (a) The function has a cache, so its outputs are identical to the local runs, token
  for token.
- (b) The URL forces temperature to zero, so the outputs cannot vary between placements.
- (c) The harness scores outputs; where the model runs changes latency, not their
  distribution.
- (d) It is a coincidence; three runs over the URL are not enough to say either way.

**15.** *(Lab)* With the function's time limit set below the model's latency, calls die
while the model is still generating. What should the harness record for such a call?

- (a) Nothing; a call that did not complete is not a sample of the model.
- (b) A failure, with the elapsed time and the reason, counted in the slice's rate.
- (c) A retry's result in its place, since the retry is the call that completed.
- (d) The last partial token stream, scored as if the call had completed.

