# Deep Dive: Conversational Planning Workflow

The conversational planning workflow is the primary process executed by the `design-v1` squad. Unlike standard automation pipelines, this workflow is optimized for human-in-the-loop validation, ensuring that requirements are fully understood and structured before any code implementation begins.

---

## Detailed Step-by-Step Flow

```text
  [Human User]
       │
       ▼ (1) Proposes Feature
  [Leader Agent]
       │
       ▼ (2) Delegates Conversation
  [Analyst Agent] <───► [Human User] (3) "Grill Me" Interview Loop
       │
       ▼ (4) Saves design.md
  [Leader Agent] <───► [Human User] (5) Explicit Approval Check (HALT!)
       │
       ▼ (6) Delegates Decomposition
  [Breakdown Planner]
       │
       ▼ (7) Generates steps/*.md
  [Leader Agent]
       │
       ▼ (8) Delivers Final Backlog
  [Human User]
```

### 1. Requirements Gathering & "Grill Me" Interview
The Analyst agent engages the human user directly.
- **Goal Exploration**: Rather than accepting a feature request at face value, the Analyst examines what exists in `agent_docs/` or source code, and grills the user with multiple targeted questions at once.
- **Devil's Advocate**: For each proposed solution, the Analyst plays devil's advocate to expose security flaws, scalability bottlenecks, or simpler alternatives.
- **User Green Light**: The conversation continues in a highly conversational format. The Analyst writes the high-level `design.md` file *only* when the user signals they are satisfied and ready to proceed.

### 2. High-Level Design Format
The output written to `agent_docs/04_plans/<feature-name>/design.md` must adhere to a standardized schema:
- **User Story**: Contextualizes the objective and outcome.
- **Implementation Backlog**: High-level milestones tracked as `Pending`, `Current`, or `Completed`.
- **Architecture Overview**: Contains folder layout specifications and a text-based Mermaid dependency diagram.
- **Checklist**: Enumerates exact acceptance criteria.

### 3. Human Sign-Off (The Critical HALT!)
Once `design.md` is saved, the Leader takes control. To ensure total alignment, the Leader **halts execution**. It presents the design link to the human and asks for explicit confirmation.
- **Design Is Authoritative**: If the human requests changes, the Leader delegates back to the Analyst.
- **Approval Transition**: The Leader only invokes the Breakdown Planner once the human responds with positive confirmation (e.g., "approve").

### 4. Granular Task Breakdown
The Breakdown Planner decomposes the high-level milestones into sequential, self-contained **Logical Units of Work (LUoW)**.
- **Step Generation**: The Planner creates separate step files under `agent_docs/04_plans/<feature-name>/steps/` (e.g., `01-create-db-migration.md`, `02-implement-repository.md`).
- **No Overlapping Steps**: Each file is isolated, describing its goal, context, files to create/edit, logic guidance, and precise verification/test checklists.
- **Alignment Check**: The Planner performs a self-audit to ensure the sum of all steps perfectly equals the `design.md` specification.

---

## Why Separate Design From Implementation?

Standard development agents are highly "eager" to write code, often jumping to conclusions and implementing incorrect solutions due to missing context. 

By separating the design phase into its own dedicated `design-v1` squad:
1. **Scope Creep Prevention**: Requirements are locked in before a single line of production code is written.
2. **Context Enrichment**: Downstream implementation agents (like the Developer and Tester) are handed a bulletproof, human-approved blueprint and step backlog, yielding extremely high success rates on the first try.
3. **Control**: The Human retains 100% control over both the conceptual blueprint and the step-by-step implementation order.
