# Week 3 — Design Studio
## Design the support desk

**The system.** Customers submit support tickets through a web form. The ticket has two
elements: the order, and a free-text message.

There are two models in this system:

- **A classifier**, a pre-trained binary logistic regression: given the ticket text, it
  returns a **score from 0 to 1** for "money is involved." It answers in milliseconds and
  is small enough to ship inside a function.
- **A language model**, the triage step: given the ticket and the account, it proposes
  - an **action** (answer · refund · hold · escalate)
  - a **refund amount**
  - a one-sentence **rationale** for the action
- **Decision:** the action. Refunds above the cap need a person. Nothing is refunded
  without the cap being checked.
- **Scale:** 50,000 tickets a month; a bad Monday is ten times an ordinary hour.
- **The customer:** a confirmation immediately; an answer within an hour.
- **The support team:** a queue of holds and escalations they can clear, with the
  rationale next to each.

**Task.** Develop the architecture for the system above: a rough idea of the scale the
models must handle, then the system architecture itself.

1. **The three numbers.** Tickets per second on the bad Monday, the latency budget for
   the confirmation and for the answer, and the model cost per month with both models.
   Pick numbers and commit.
2. **The architecture.** Cover the full system. Name the tables. Draw the path of one
   ticket and mark each hop **sync** or **async**, with the reason. The two models need
   not live in the same place, and the classifier's score may decide what the language
   model sees. Include:
   - the web server
   - any databases
   - the two models
   - a queue mechanism for the manual review
   - anything else you need
3. **The failure path** *(if time)*. What the customer and the support team see, and what
   runs, when the classifier is unavailable for an hour; and separately, when the
   language model is.
