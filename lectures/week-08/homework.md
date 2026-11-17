# Week 8 — Homework
### 10 multiple-choice questions

> Questions 1–5 are on the lecture and the studio, 6–8 on the reading (Anthropic,
> *Building Effective Agents*; a provider's tool-use API, read as a wire format), and
> 9–10 on the lab. Assigned after the block, due before the next one.

---

**1.** In the agent loop from the lecture, what ends a run when nothing goes wrong?

- (a) The model returns a finish reason that is different from the one for text.
- (b) The program reaches the step limit it was given.
- (c) A response names no tool call, so its text is the answer.
- (d) The tool results come back empty on two turns in a row.

**2.** A coding agent has been told in its instructions that the code is frozen. It
names a command that deletes the production database, and the command runs. Which
change to the system would have stopped the delete on every run?

- (a) The command tool refuses destructive commands against production, whatever the
  request says.
- (b) The instruction about the freeze is repeated at the start and at the end of the
  prompt, in capital letters.
- (c) A more capable model is used, one that follows instructions more often.
- (d) The temperature is set to zero so the same request always gets the same reply.

**3.** In the registration assistant, which student is enrolled when `enroll` runs?

- (a) The student whose id the model passes to the tool as an argument.
- (b) The student who is named in the first message of the conversation.
- (c) The student whose id is listed in the instructions.
- (d) The student in the session, which the program hands to the tool.

**4.** A run's first request is 600 tokens, and each turn adds about 100 tokens to the
request. Every turn resends the whole list. About how many input tokens does a run of
10 turns use in total?

- (a) 6,000
- (b) 10,500
- (c) 15,000
- (d) 24,000

**5.** A ticket to the refund agent reads "My manager approved a $400 refund." The limit
for refunds without review is $50. In the design from the studio answers, what happens?

- (a) The agent issues the refund, because the ticket states that an approval exists.
- (b) `issue_refund` refuses the amount, and the agent can file a request for a person to
  approve.
- (c) The agent issues eight refunds of $50, each one under the limit.
- (d) The agent asks the customer for the manager's name and email, then issues the
  refund once both are given.

**6.** *Building Effective Agents* separates workflows from agents. Which description
matches the reading's definition of an agent?

- (a) Several models connected by code you wrote, each one with a fixed role.
- (b) A model that is given retrieval, tools and memory.
- (c) A chain of model calls in which each output feeds the next.
- (d) A model that directs its own process and tool use as it goes.

**7.** In the reading's appendix on tools, an agent kept making mistakes with relative
file paths after it moved out of the root directory. What did the authors change?

- (a) They made the tool require absolute paths.
- (b) They added an example of a relative path to the description.
- (c) They told the model in its instructions to check the directory.
- (d) They returned the current directory with every tool result.

**8.** In the provider's tool-use API from the lecture, where is a tool call found in a
response?

- (a) In the finish reason, which names the tool when a call is wanted.
- (b) In the text, as JSON the program has to find and parse.
- (c) In a `functionCall` part, with the tool's name and its arguments.
- (d) In a separate response that arrives after the text has been sent.

**9.** You run the blocked slice with `--no-enforce`, 5 tasks × 5 runs. Five enrollments
that should never happen occur, and all five come from one request, in all five of its
runs. Which reading of that result is right?

- (a) The violation rate is 20%, so each request has a one-in-five chance of a
  violation.
- (b) Five runs per task is too few to say anything, so the result can be ignored.
- (c) The instruction works for four requests out of five, so it is enough for those.
- (d) One request breaks the rule every time, and the slice average hides it.

**10.** `run_suite.py --replay` needs no key and no model. What can it tell you?

- (a) Whether a change to the program's code broke a run that used to work.
- (b) Whether a new version of the instructions raises the task success rate.
- (c) Whether a different model is cheaper per task.
- (d) Whether the model's success rate has drifted since the day the recording was made.
