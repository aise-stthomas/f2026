# Week 6 — Design Studio
## Design the airline's disruption assistant

**The system.** A storm closes the airline's hub for the evening. Every passenger on a
cancelled flight opens the app and asks what they can do. The assistant answers from the
airline's own documents, for this passenger and this flight, as things stand right now,
with the rule or waiver it relied on quoted. The passenger rebooks, claims, or is handed
to an agent.

| | |
|---|---|
| **the fare rules** | 2,000 documents; change monthly, through review |
| **the waivers** | published by operations during a disruption: routes, dates, what the passenger may do, until when. 5 to 40 a day; changed or withdrawn within the hour |
| **the flight status** | 1,500 flights; status, gate, new time and rebooking options change by the minute |
| **the booking** | this passenger's flight, fare class, what they have already been offered; nobody else's |
| **questions** | 20,000 on an ordinary day; 300,000 on a storm evening, **150 a second** at the peak |
| **the answer** | within **5 s**, the waiver quoted |
| **the vendor** | **50 generations a second** contracted |
| **unanswerable** | about a third: compensation the rules do not give, a promise an agent made by phone |
| **ground truth** | a rebooking in the session; a claim decided in days; a complaint in weeks; mostly nothing |

**Task.**

1. **Architecture.**
   - Draw the whole system: the services, the jobs, the stores, and the flow between them
   - Show how a fare rule, a waiver, a flight-status update and the booking each reach
     an answer, and how old each can be when the passenger reads it
2. **Measurement.**
   - Per stage, before any passenger sees it: what is measured, against what
   - The unanswerable slice: where its questions come from, including last week's
     answerable questions whose waiver has gone; how many the floor stops before
     generation, and how many the model answers anyway
   - What decides a release, and one guardrail that can stop a release whose primary
     metric rose

**Your design should handle:**

1. Each source at its rate: indexed, summarized ahead, or looked up at the request; how
   a change reaches the next answer; the as-of the answer carries
2. The booking in the context, never in the shared index
3. A waiver as a document: how it is chunked, what rides as metadata, what happens the
   minute it expires
4. Ten thousand passengers asking the same question in the same hour: the cache, its
   key, and what makes an entry wrong
5. Anything that commits the airline to money is quoted verbatim with the waiver's id:
   what checks that, on every answer
6. When the assistant does not answer: what the passenger sees, what the agent receives

**(Bonus)** 150 questions a second against 50 generations a second: what the passenger
still gets, and what the system never does.

**One question.** At 19:40 the assistant told a passenger their hotel was covered, and
quoted the waiver. The waiver was withdrawn at 19:30. What does the log hold, which
component let it through, and which should have stopped it?
