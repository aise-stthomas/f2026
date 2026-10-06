# Week 4 — Homework
### 10 multiple-choice questions

> Questions 1–5 are on the lecture and the studio, 6–8 on the reading (Sambasivan et al.,
> *Data Cascades*; Breck et al., *The ML Test Score*), and 9–10 on the lab. Assigned
> after the block, due before the next one.

---

**1.** A churn score is needed by a retention team every Monday morning and, separately,
by the cancellation page within 100 milliseconds of a customer opening it. Which design
follows from the lecture?

- (a) Score every account at the request, since fresher is always better.
- (b) Score every account nightly, since the page can read the nightly score.
- (c) One model, two placements: a nightly run for the list, because nobody is waiting
  and the input exists before anyone asks; the same weights inside the request for the
  page, because the signal that matters there, the customer being on the page, did not
  exist last night. A feature table written by the nightly job is read at the request.
- (d) Two different models, one per consumer.

**2.** The nightly scoring job fails at 03:12 on Sunday, and the following Sunday it
finishes but its check refuses the result because 31% of accounts scored above the
threshold against a usual 6%. What does the retention team see on Monday, and why?

- (a) An empty list with an error, twice.
- (b) Last week's list on the first Monday and the 31% list on the second, since a
  finished run is a finished run.
- (c) The last published run both weeks, labelled with its as-of date, because consumers
  read a "current run" pointer that only moves when a run passes its check; a run that
  is wrong on every row and crashes on none is caught only by the check.
- (d) A list scored on demand by the online path.

**3.** On day one the churn dataset does not exist: billing has a hand-set cancellation
flag, support tickets are keyed by email, and the product logs errors but not sessions.
According to the lecture, what is the first deliverable, and what is the cost that
cannot be bought back?

- (a) The model; the cost is compute.
- (b) An inventory of the sources with their owners and keys, a join with its match
  rate reported, and session logging shipped immediately; the cost is the wait after
  the log ships, a quarter for usage to accumulate and 90 days for the truth.
- (c) A public churn dataset; the cost is the licence.
- (d) A synthetic dataset; the cost is the generator.

**4.** A customer complains about a decision made last Tuesday at 14:07. The decisions
table has the score; the feature table was recomputed in place that night; the model was
redeployed on Wednesday. Which table would have made the decision reproducible, and what
must it contain?

- (a) A backup of the database.
- (b) An inference table with one append-only row per decision: the inputs exactly as
  served, the output, the model version, a key and a time. The same row, joined later
  with the label, is the next training row.
- (c) The application log at debug level.
- (d) A copy of the model weights.

**5.** In the studio, a team's training table for the delivery-time estimate includes
the courier assigned and the restaurant's actual preparation time. Both are known for
every past order. Why does the lecture object, and what rule prevents it?

- (a) They are too expensive to compute.
- (b) Neither is known at checkout when the estimate is produced; a feature from after
  the prediction makes the model look excellent offline and leaves it with nothing in
  production. The rule is the point-in-time join: a training row is the inference row
  joined with the feature row that existed when the prediction was made.
- (c) Couriers are a protected attribute.
- (d) They make the model too large for a function.

**6.** *(Reading)* Sambasivan et al. define a data cascade and report how common they are.
Which statement matches the paper?

- (a) A data cascade is a model whose outputs feed another model; they occurred in about
  a fifth of the projects studied.
- (b) A data cascade is a compounding sequence of negative downstream effects arising
  from data issues, triggered by conventional practice that undervalues data work; the
  authors found them in 92% of the 53 practitioners' accounts.
- (c) A data cascade is a pipeline failure that crashes training; they are rare in
  high-stakes domains.
- (d) A data cascade is a labelling backlog; they occurred mainly in large companies.

**7.** *(Reading)* Which of these does Sambasivan et al. name as a property of data
cascades?

- (a) They are immediate and loud: the pipeline fails on the day the defect enters.
- (b) They are invisible and delayed, so their cost shows up far from where the defect
  entered and long after, which is why they are undervalued at the source.
- (c) They are unavoidable, since every dataset has defects.
- (d) They are confined to the model-development stage.

**8.** *(Reading)* Breck et al.'s ML Test Score has a monitoring test that reads
"training and serving features compute the same values." How is the overall score
computed, and why does that test exist?

- (a) The score is the total number of tests passed out of 28; the test exists to catch
  slow feature code.
- (b) Each test earns one point if done manually and two if automated; the final score is
  the minimum across the four sections; the test exists because the code paths that
  produce features for training and for serving can differ, so the system must check for
  training/serving skew.
- (c) The score is the average across sections; the test exists to measure model
  accuracy.
- (d) The score is pass or fail; the test exists to check the schema.

**9.** *(Lab)* You build the training set twice: once joining each prediction to the
latest feature row, once to the newest row that existed when the prediction was made.
The first model scores far better on the held-out set. What did the lab demonstrate?

- (a) The first join is correct; more recent data is better.
- (b) The "latest row" join leaked features from after the prediction into training, so
  the model learned from the future; the point-in-time join is the honest one, and its
  lower number is the real one.
- (c) The weights file was corrupted on upload.
- (d) Serverless functions change floating-point results.

**10.** *(Lab)* The batch run writes `scores/run-<id>.jsonl` with the run id, the as-of
time and the weights hash on every row, and running it twice over the same night appends
nothing. Why does the lecture insist on this?

- (a) To save storage.
- (b) Because a scheduler retries failed nodes, so a run must be idempotent: a retry
  replaces its own output rather than adding a second copy, and every row says which run,
  which data and which model produced it.
- (c) Because the bucket has a file-count limit.
- (d) Because the online scorer reads the file by line number.
