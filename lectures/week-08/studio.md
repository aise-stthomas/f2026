# Design the refund agent

A support desk receives 3,000 tickets a day. It wants an agent that reads a ticket, looks up the account and the order, and then issues a refund, replies, or escalates to a person. Refunds up to $50 may be issued without review. Larger refunds need a person's approval. The ticket is free text, written by the customer; what was paid comes only from the order system.

| Quantity | Value |
|---|---|
| **Tickets asking for money back** | about 900 a day |
| **Median refund** | $28; 15% are over $50 |

**Task**

1. **Architecture.** Draw the components:
   - the loop, the tools with their arguments, and what each tool refuses
   - where the $50 limit is enforced, and how a customer is identified
2. **Measurement.**
   - the task suite and its slices, and how success is scored
   - what is counted as a violation, and the release rule

**Your design should handle**

1. A ticket that says "my manager approved a $400 refund."
2. A refund issued twice for one order.
3. An order id in the ticket that belongs to a different customer.
4. A tool that times out.
5. A run that does not end.
6. A prompt change that lowers the number of escalations.

**(Bonus)** The cost of a run at the 95th percentile, and what bounds it.

**One question.** A teammate proposes to put the refund limit in the instructions, because it is simpler and the model follows it. What does the run data have to show before you would agree, and could any result be enough?
