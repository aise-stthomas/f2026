# Week 4 — Design Studio
## Design the delivery-time estimate

**The system.** A food-delivery app shows a delivery time next to every restaurant in
the list, and again at checkout: *"35–45 min."* A model produces the number. The
customer decides whether to order from it; the restaurant and the courier are
dispatched after it.

- **Input:** the order (items, restaurant, the customer's address, the time of day), and
  whatever else you decide the system can know at that moment
- **Output:** a number of minutes, and the range shown to the customer
- **Decision:** none by the model. The estimate is shown; a person is not involved.
- **Scale:** 2,000,000 orders a day. The Friday 19:00 peak is forty times a Tuesday
  15:00.
- **The customer:** the estimate on the page within 200 milliseconds, for every
  restaurant in the list and again at checkout.
- **The truth:** the actual delivery time, known when the courier marks the order
  delivered, 30 to 60 minutes after checkout. Never, if the customer cancels.

**The numbers, given.** So the design can start from them:

| Number | Value |
|---|---|
| estimates per second, Friday 19:00 | about 2,000 orders a minute at the peak, and the list page shows 20 restaurants: **about 700 estimates a second**, in bursts |
| latency, one estimate | **under 200 ms** for the page, so under 5 ms for each of 20 estimates |
| cost per month | the nightly job: pennies; the online path: arithmetic inside a process already running |

Any design that makes a network call per estimate at the peak has failed the latency
number before it is drawn.

**Task.** A scoring system: answer the three questions for it.

1. **How it is built.** What is computed before the order arrives, for every restaurant
   and every zone, and on what schedule? What is computed at the order, for this
   customer and this basket? Name the table between the two and say how old its
   contents may be. Then the full architecture: name the tables, draw the path of one
   order from the restaurant list to the delivered meal, and mark each hop **sync** or
   **async**, with the reason.
2. **How its data is handled.** Where does the label come from, and when? Which orders
   never get one? What did the training table know about an order that the serving path
   cannot know at checkout, and what is the one rule that keeps that out of the training
   set? What is written down at the moment of each estimate?
3. **How it is measured.** The estimate is a promise. What rate, on which slices, says
   the system is keeping it, and where do the numbers come from?
4. **The live estimate** *(optional)*. After checkout, the tracking page shows
   *"arriving in 12 min"* and updates as the courier moves. What triggers a
   re-estimate, what is written down at each one, and which estimate does the label
   score?

**One question to answer somewhere in your design.** A long estimate makes the
customer cancel, and a cancelled order has no label: what does that do to next month's
training set?
