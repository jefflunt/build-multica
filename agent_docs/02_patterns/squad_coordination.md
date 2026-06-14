# Squad Coordination Patterns

This project relies on Multica's implicit message-passing routing architecture to orchestrate multi-agent collaboration. This pattern eliminates the need for a complex coding runtime to manage agent execution, utilizing markdown-formatted agent mentions instead.

---

## State Transition via Mentions

To transition control from one specialist to another, an agent outputs **exactly one comment** containing an explicit `@`-mention formatted using Multica's unique URI syntax:

```text
[@AgentName](mention://agent/<agent-uuid>)
```

When the Multica parser detects this format, it halts execution of the current agent, schedules the target agent with the current conversation history, and registers the transfer.

### The Linear Routing Pattern
In `design-v1`, coordination flows linearly across different states to maximize the efficiency of each phase:

1. **User Proposal** $\rightarrow$ **Analyst**: The Leader routes the conversation to the Analyst upon detecting a new feature proposal.
2. **Conversation Loop**: The Analyst and User engage back and forth. The Analyst continues to loop to itself or back to the user until a finalized `design.md` file is written.
3. **Draft Complete** $\rightarrow$ **Leader**: The Analyst saves `design.md` and routes back to the Leader to notify them of completion.
4. **Human Review**: The Leader asks the Human for approval and halts completely.
5. **Approved** $\rightarrow$ **Breakdown Planner**: Upon human sign-off, the Leader routes the task to the Breakdown Planner.
6. **Breakdown Complete** $\rightarrow$ **Leader**: The Breakdown Planner writes `steps/*.md` files and routes control back to the Leader.
7. **Final Delivery**: The Leader reviews the outputs, presents them to the User, and terminates the session.

---

## Benefits of Declarative Coordination

- **Zero Middleware**: No external Python/JS orchestrators are needed; routing guidelines are contained entirely within Markdown files.
- **Traceable History**: Humans and agents can inspect the thread's comment history to see exactly which agent passed control and why.
- **Resilience**: If an agent fails to run or produce the correct output, the coordinator can re-route back to the same specialist with debugging context.
