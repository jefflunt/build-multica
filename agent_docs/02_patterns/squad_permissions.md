# Agent Permission Patterns

Multica agent definitions utilize YAML frontmatter blocks to specify constraints, execution modes, and security permissions. This pattern ensures that agents are granted the minimum power necessary to do their jobs while preventing accidental edits or unapproved shell operations.

---

## Permission Structure

The agent definition frontmatter contains three core keys:

```yaml
---
description: [Short explanation of the agent's responsibilities]
mode: primary | utility
permission:
  bash: allow | ask | deny
  edit: allow | ask | deny
  write: allow | ask | deny
---
```

---

## Permission Archetypes

In this workspace, we utilize two distinct agent permission archetypes depending on whether they are implementation-focused or design-focused.

### 1. Conversational / Design-Focused (Analyst, Breakdown Planner)
These agents require file exploration capabilities and the power to generate artifact documents, but should not run compilation commands or execute arbitrary developer tools.

- **Bash**: `allow`. Grants the agent access to read file contents and explore the workspace recursively via commands.
- **Edit**: `ask`. Prevents the agent from directly altering pre-existing codebase files without prompting the user.
- **Write**: `allow`. Allows the agent to create new high-level design documents and step-by-step breakdown markdown files seamlessly.

### 2. Implementation-Focused (Developer, Tester)
These agents write code, execute test suites, and perform compilation steps.

- **Bash**: `allow`. Required to execute standard testing frameworks (such as `go test`, `pytest`, `npm test`) and build artifacts.
- **Edit**: `ask` or `allow`. Allows modifying application files to implement features or fix bugs.
- **Write**: `ask`. Allows creating new application files while notifying the user of additions.
