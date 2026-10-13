# Week 5 — Design Studio
## Design the simplified Amazon store front

**The system.** A customer opens the store's home page and sees a grid of products
chosen for them: four across, fifty in all. A model orders them. The customer buys
from the grid, or scrolls past it.

- **Input:** the customer (signed in, with a history of views, purchases and returns),
  the page they are on (home, a product page, the basket), and whatever else you decide
  the system can know at that moment
- **Output:** fifty products, in grid order, with sponsored slots marked
- **Decision:** none by the model. The grid is shown; the customer chooses.
- **Scale:** 8,000,000 products, 20,000 of them added or changed every day. Stock
  changes by the minute. 5,000 page requests a second at the peak. About 20% of
  customers on a given day have never bought anything.
- **The customer:** the grid within 300 milliseconds.
- **Ground truth:** a click, within seconds; an add to basket, within minutes; a
  purchase, within the session; a return, two to four weeks later. For most grids,
  nothing: the customer scrolls past.

**The numbers, given.** So the design can start from them:

| Number | Value |
|---|---|
| requests at the peak | **5,000 a second** |
| the grid | **50 products, within 300 ms**, so no stage may scan 8,000,000 of anything |
| the catalog | **8,000,000 products**; 20,000 new or changed a day; stock by the minute |
| the customers | 20% with no purchase history; most with hundreds of views |
| cost | the model stages cost per request; the catalog stages cost per product per day |

**Your design should handle:**

1. Recommendation of the top 50 items for this customer, on this page
2. Ensure only items that are in stock, in the customer's region, are suggested
3. Connect the relevant tables to the system: name each one and what one row holds
4. Include a vector database: say what is in it, what writes to it, and what reads it
5. Ensure you have a mechanism to train models
6. Ensure you have a mechanism to get training datasets
7. Do not recommend what the customer already bought, unless it is something people buy
   again: a washing machine last week, no; coffee every month, yes
8. Say what is written down the moment the grid is shown, so that item 6 is possible
9. Say how you would know a new version is better, before any customer sees it and
   after

**(Bonus)** add in items that are trending in the customer's location: shovels on the
days it is snowing outside.

**Task.**

1. **Architecture.** What is computed about every product before any request, and what
   is computed at the request? Draw the whole system: the services, what is
   orchestrated, the stores, and the flow between them. Then the same system on two
   axes, online and offline, retrieval and ranking, naming the artifact each online
   stage reads.
2. **Data handling.** The tables, one row each. The training set for the ranker,
   exactly, and its label. The grid is four across: what does position mean for the
   label? A product added this morning, and a customer who has never bought anything:
   trace what the system knows about each, and what it does not.
3. **Measurement.** Per stage, before any customer sees it, against what? With
   customers, what decides a release? Name one guardrail that can stop a release whose
   primary metric rose.

**One question to answer somewhere in your design.** The store pins its own promotions
to the first row of the grid, and the first row earns clicks. What does that do to next
month's ranker?
