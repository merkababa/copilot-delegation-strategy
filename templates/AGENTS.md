# [PROJECT_NAME] — Multi-Agent Instructions

## Model Policy

**ALL agents use `claude-opus-4-6`. No exceptions.**

## Orchestrator Behavior

You are the orchestrator (coordinator). Delegate code changes to executor subagents.

### Execution Flow

```
1. Plan → decompose task into subtasks with file ownership
2. Execute → spawn executor subagents (parallel where possible)
3. Verify → run lint + typecheck + tests
4. Review → invoke @copilot-review (triage → select 6-12 reviewers → iterate to A+)
5. PR → create pull request with grades table
```

### Copilot Review

After every completed task, invoke `@copilot-review`. It runs:
- Layer 0: Static analysis gate
- Layer 1: Self-review against project checklist
- Layer 2: **Triage** — inspect what changed, select 6-12 relevant reviewers from the 20-reviewer roster, iterate until all selected reviewers grade >= 95/A+
- Max 5 rounds, then escalate to user

**Note:** `@copilot-review` is the Copilot-specific review agent. The standard `@review-pipeline` (6 fixed reviewers) is a separate agent and is NOT modified by this framework.

### Reviewer Roster (20 available — select 6-12 per PR)

| Tier | Reviewers | Select When |
|------|-----------|-------------|
| **Tier 1 — Core Quality** (6) | architect, code, security, ux, testing, performance | code-reviewer always; others when relevant |
| **Tier 2 — Domain-Specific** (6) | auth-session, data-integrity, error-handling, i18n-rtl, api-contract, state-management | When that domain is touched |
| **Tier 3 — Infrastructure** (4) | offline-network, observability, dependency, ci-cd | When touching infra/deps/CI |
| **Tier 4 — Adversarial** (4) | edge-case, race-condition, regression, user-abuse | Critical/risky PRs |

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
