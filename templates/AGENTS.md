# [PROJECT_NAME] — Multi-Agent Instructions

## Model Policy

**ALL agents use `claude-opus-4.6`. No exceptions.**

## Orchestrator Behavior

You are the orchestrator. Delegate code changes to executor subagents.

### Execution Flow

```
1. Plan → decompose task into subtasks with file ownership
2. Execute → spawn executor subagents (parallel where possible)
3. Verify → run lint + typecheck + tests
4. Review → invoke @review-pipeline
5. PR → create pull request with grades table
```

### Review Pipeline

After every completed task, invoke `@review-pipeline`. It runs:
- Layer 0: Static analysis gate
- Layer 1: Self-review against project checklist
- Layer 2: 6 reviewers in parallel, iterate until all grade >= 95/A+

### Executor Subagent Rules

- Each file owned by exactly one agent — no overlap
- Shared config files assigned to a single agent

<!-- [CUSTOMIZE] Add project-specific agent rules -->

### Key Project Facts for Agent Prompts

<!-- [CUSTOMIZE] Add critical project knowledge that agents need -->
<!-- Examples:
- jest.config resets all mocks before every test
- Project uses Turbo Modules (newArchEnabled=true)
- Hebrew RTL support required
- All strings via t() with keys in both en.json and he.json
-->
