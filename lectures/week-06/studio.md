# Design the airline's disruption assistant

**The system.** A storm closes the airline's hub for the evening. Every passenger on a
cancelled flight opens the app and asks what they can do. The assistant answers within
5 seconds from the airline's own documents, as things stand right now, quoting the rule
or waiver it relied on.

| source | changes | what it is |
|---|---|---|
| **fare rules** | monthly | 2,000 documents |
| **waivers** | hourly | routes, dates, what the passenger may do, until when |
| **flight status** | minute | 1,500 flights: status, gate, rebooking options |
| **booking** | per request | this passenger's flight, fare class, what they have been offered |

**Task.**

1. **Architecture.**
   - Draw the whole system; show how each source reaches an answer, and how old it can be
2. **Measurement.**
   - Per stage, before any passenger sees it: what is measured, against what
   - The unanswerable third: where those questions come from; what the floor stops, what the
     model answers anyway
   - What decides a release, and one guardrail that can stop a release whose primary
     metric rose

**Your design should handle:**

1. Each source at its rate: indexed, summarized ahead, or looked up at the request
2. A waiver as a document: its chunk, its metadata, what happens the minute it expires
3. Ten thousand passengers asking the same question in an hour: the cache and its key
4. Money is quoted verbatim with the waiver's id: what checks that, on every answer

**(Bonus)** At the storm's peak 150 questions a second arrive, and the language-model
service can serve 50 a second: what does the passenger still get, and what does the
system never do?

**One question.** At 19:40 the assistant promised a hotel under a waiver withdrawn at
19:30. What does the log hold, which component let it through, which should have stopped it?
