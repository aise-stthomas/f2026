# Week 1 — Homework
### 10 multiple-choice questions · ~20 minutes

> Weekly homework: ten questions, deliberately quick. Questions 1–7 are on the lecture
> and the studio; 8–10 can only be answered by having done the lab. Assigned after the
> block; due before the next one — people have lives, so it gets the full week. Lowest
> weekly score is dropped.

---

**1.** A fraud model flags 2% of 50 million daily transactions for analyst review. The
team has 40 analysts who each clear 150 cases a day. What is the most accurate
diagnosis?

- (a) The model's precision is too low and should be retrained.
- (b) The review threshold was set without reference to review capacity — a step-1
  requirement (review volume must fit headcount) was never written.
- (c) The analysts need better tooling.
- (d) The false-negative rate is too high.

**2.** Which of the following is a *route* rather than a *score*?

- (a) 0.87
- (b) Step-up authentication
- (c) The model's confidence
- (d) AUC

**3.** A team's only requirement is "the model must be at least 94% accurate." Which
two things is that sentence missing that would tell you what happens to a wrongly
declined cardholder?

- (a) A latency budget and a cost budget
- (b) A training-set size and a validation split
- (c) A remainder policy and an owner
- (d) A model architecture and a feature list

**4.** Why do declined transactions make the *next* version of a fraud model worse, even
when every decline was correct?

- (a) Declines are expensive to store.
- (b) Declined transactions never receive a fraud label, so the training data is biased
  toward transactions the system chose to approve.
- (c) Declines reduce the total transaction volume.
- (d) They don't; correct declines only improve the model.

**5.** "Temperature" on a language model is best described as:

- (a) A creativity setting that makes the model more imaginative.
- (b) A parameter that reshapes the output distribution before sampling, trading
  variance for coverage.
- (c) A safety setting that reduces hallucination.
- (d) A measure of how confident the model is.

**6.** Which of the four properties explains why an agent's authorization problem has
three parties instead of two?

- (a) Stochastic
- (b) Data-specified
- (c) Non-stationary
- (d) Acting

**7.** In *Moffatt v. Air Canada*, which of the following was **not** one of the
missing pieces identified in the walkthrough?

- (a) No acceptable rate of wrong policy answers was ever written down.
- (b) There was no metric for whether answers matched the policy on the page.
- (c) The language model was substantially less capable than competitors' models.
- (d) The remainder policy amounted to arguing the chatbot was a separate legal entity.

**8.** *(Lab)* You ran one fixed ticket through the model 100 times at temperature 0.
Which conclusion is supported?

- (a) Temperature 0 guarantees identical output on every call.
- (b) Temperature 0 narrows the distribution but does not by itself guarantee identical
  outputs; batching, numerics, and vendor changes still apply.
- (c) Temperature 0 makes the model's answer correct.
- (d) The number of distinct outputs at temperature 0 is always exactly one.

**9.** *(Lab)* In the scaffold's ten-item harness, which ticket category failed at
least 30% of the time?

- (a) `[category A — fill from scaffold]`
- (b) `[category B]`
- (c) `[the seeded failing category]`
- (d) `[category D]`

**10.** *(Lab)* Why were the usage alarms the *first* thing you were asked to configure
on AWS?

- (a) AWS requires them before any service can be used.
- (b) Cost per request is a first-class requirement in this course, and a Learner Lab
  with no alarms has no signal until the budget is gone — and the lab with it.
- (c) The alarm is required by the Gemini free tier.
- (d) It enables the always-free services.

