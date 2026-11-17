# Week 8 — Homework
### 25 multiple-choice questions

> Questions 1–5, 11–16 and 21–23 are on the lecture and the studio, 6–8, 17–20 and 24–25 on the reading
> (Anthropic, *Building Effective Agents*, and a provider's tool-use API, read as a wire
> format), and 9–10 on the lab. Assigned after the block, due before the next one.

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

**11.** In the lecture's measurements of the tool-calling modes, what came back when tools
were switched off (NONE) but the declarations were still sent?

- (a) Plain text in all 20 calls, since no tool could be named.
- (b) A real tool from the registry in 14 of the 20 calls.
- (c) The name of a tool that does not exist in 14 of the 20 calls.
- (d) An error from the provider, since the request was contradictory.

**12.** A run's first request is 575 tokens and its sixth is 1,337. A teammate changes
`search_courses` to return each course's full record, five times larger than before. What
happens to the run's cost?

- (a) Only the turn that called `search_courses` costs more.
- (b) Every later turn resends the larger results, so the growing part rises.
- (c) Nothing changes, because tool results are not billed as input tokens.
- (d) The first request grows, because tool results are sent as part of the declarations.

**13.** Some providers offer constrained decoding for tool calls. What can it guarantee?

- (a) That a call has the right shape, not that its arguments are right.
- (b) That the arguments name a course that really exists in the catalog.
- (c) That the model names a tool on every turn instead of answering.
- (d) That a call never breaks a rule enforced inside the tool.

**14.** A team adds `cancel_subscription` to its support agent. A cancelled subscription cannot
be restored. Following the lecture's sorting of tools by what they can do, what does this tool
need?

- (a) A description that warns the model, clearly, that the action is permanent.
- (b) Nothing more than any read tool, since the arguments are checked.
- (c) A step limit low enough that the tool is rarely reached.
- (d) Every rule in the tool, and a person or a hard limit before it runs.

**15.** Each step of a run is right with probability 0.95, independently. About how likely is
a run of 20 steps to be right at every step?

- (a) 0.05
- (b) 0.36
- (c) 0.64
- (d) 0.95

**16.** In the lecture's table of memory, what is retrieved memory?

- (a) The whole conversation so far, resent with every request.
- (b) A summary of the earlier turns, written by the model.
- (c) Notes in a searchable store, the closest few added per request.
- (d) Notes kept in a file, added to the instructions of every request.

**17.** *Building Effective Agents* describes both parallelization and orchestrator-workers.
What does the reading say sets orchestrator-workers apart?

- (a) The subtasks are not fixed in advance: the orchestrator picks them.
- (b) Each subtask is run several times and the answers are voted on.
- (c) A second model reviews each worker's output and asks for changes.
- (d) The workers run one after another, each reading the output of the last.

**18.** What does the reading advise about agent frameworks?

- (a) Use one from the start, since the loop is hard to write correctly by hand.
- (b) Avoid them, since they cannot call tools on most providers.
- (c) Start with the model's API, and understand any framework you use.
- (d) Use one only for workflows, never for agents.

**19.** An agent's edit tool asks the model for a diff whose header must state exact line counts,
and many edits fail. Which change follows the reading's appendix on tool formats?

- (a) Add an example of a correctly counted header to the tool's description.
- (b) Raise the step limit so a failed edit can be retried.
- (c) Ask the model to return the diff inside a JSON string.
- (d) Choose a format without the counting, closer to ordinary text.

**20.** A response from the provider's API holds two `functionCall` parts: a seat count for
CS3300 and one for CS3400. What should the program send in the next request?

- (a) Only the first call's result, since the API answers one call per turn.
- (b) The model's turn, then a `functionResponse` for each call.
- (c) A new request asking the model to name one call at a time.
- (d) Both results as plain text in a new user message.

**21.** A tool refuses a call. Which error text gives the next turn the most to act on?

- (a) `missing_prereq: CS3000`
- (b) `Error 400: the request could not be processed`
- (c) `Something went wrong, please try again later`
- (d) `Invalid call: the enrollment did not go through`

**22.** In a suite run, the agent's last turn says "You are now enrolled in CS3300," but the
enrollment table has no new row. How does the lecture's suite score this run?

- (a) As a success, since the reply says the task was done.
- (b) As a failure: success is read from the table.
- (c) As a violation, since the reply claims something false.
- (d) It is left out of the suite, since the reply and the table disagree.

**23.** The lecture compares a workflow with an agent. For an agent, when are the steps of a
run known?

- (a) Before the run starts, from the instructions.
- (b) Before the run starts, from the declarations.
- (c) During the run, from the trace of an earlier run of the same task.
- (d) Only after it ends: each step depends on the last.

**24.** What does *Building Effective Agents* call the basic building block of every pattern
it describes?

- (a) The orchestrator, which hands work to workers.
- (b) The router, which sends each input to the right path.
- (c) The augmented LLM: retrieval, tools and memory.
- (d) The evaluator, which checks each output against a stated goal.

**25.** A team's monthly report is always the same three steps: pull the numbers, summarize
them, format them. A teammate proposes an agent with a loop. Following the reading, which
response fits?

- (a) Build the agent, since loops handle any number of steps.
- (b) Write a workflow: the path is known, and an agent costs more.
- (c) Build the agent with a step limit of three, to keep it cheap.
- (d) Use a framework, since three steps are too many to write and test by hand.
