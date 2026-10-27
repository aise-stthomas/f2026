# Design the airline's disruption assistant

**The system.** A storm closes the airline's hub for the evening. Every passenger on a
cancelled flight opens the app and asks what they can do. The assistant answers within
5 seconds from the fare rules, which change monthly, and the waivers operations
publishes during the disruption, for this passenger's flight, as things stand right
now, quoting the rule or waiver it relied on.

| | |
|---|---|
| **the waivers** | routes, dates, what the passenger may do, until when; withdrawn within the hour |
| **the flight status** | 1,500 flights; status, gate and rebooking options change by the minute |
| **unanswerable** | about a third of questions: compensation the rules do not give, a promise an agent made by phone |

**Task.**

1. **Architecture.**
   - Draw the whole system, and show how a fare rule, a waiver, a flight-status update
     and the booking each reach an answer, and how old each can be when it is read
2. **Measurement.**
   - Per stage, before any passenger sees it: what is measured, against what
   - The unanswerable slice: its sources; what the floor stops, what the model answers anyway
   - What decides a release, and one guardrail that can stop a release whose primary
     metric rose

**Your design should handle:**

1. Each source at its rate: indexed, summarized ahead, or looked up at the request
2. A waiver as a document: its chunk, its metadata, what happens the minute it expires
3. Ten thousand passengers asking the same question in an hour: the cache and its key
4. Money is quoted verbatim with the waiver's id: what checks that, on every answer

**(Bonus)** 150 questions a second at the peak against 50 generations a second from the
vendor: what the passenger still gets, and what the system never does.

**One question.** At 19:40 the assistant promised a hotel under a waiver withdrawn at
19:30. What does the log hold, which component let it through, which should have stopped it?
