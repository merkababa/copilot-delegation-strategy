# Copilot Delegation Setup Guide

## How to Set Up a New Project for Claude Code → Copilot Delegation

### Prerequisites
- GitHub Copilot Pro+ subscription ($39/month)
- Claude Code Max subscription (at least 1 account)
- Project with a GitHub remote

### Step 1: Copy Template Files

Copy the following 25 files into your project:

```
templates/
├── .github/
│   ├── copilot-instructions.md        → YOUR_PROJECT/.github/copilot-instructions.md
│   └── agents/
│       ├── plan-executor.agent.md     → YOUR_PROJECT/.github/agents/plan-executor.agent.md
│       ├── review-pipeline.agent.md   → YOUR_PROJECT/.github/agents/review-pipeline.agent.md
│       │
│       │  Tier 1 — Core Quality (6 reviewers)
│       ├── architect-reviewer.agent.md
│       ├── code-reviewer.agent.md
│       ├── security-reviewer.agent.md
│       ├── ux-reviewer.agent.md
│       ├── testing-reviewer.agent.md
│       ├── performance-reviewer.agent.md
│       │
│       │  Tier 2 — Domain-Specific (6 reviewers)
│       ├── auth-session-reviewer.agent.md
│       ├── data-integrity-reviewer.agent.md
│       ├── error-handling-reviewer.agent.md
│       ├── i18n-rtl-reviewer.agent.md
│       ├── api-contract-reviewer.agent.md
│       ├── state-management-reviewer.agent.md
│       │
│       │  Tier 3 — Infrastructure & Ops (4 reviewers)
│       ├── offline-network-reviewer.agent.md
│       ├── observability-reviewer.agent.md
│       ├── dependency-reviewer.agent.md
│       ├── ci-cd-reviewer.agent.md
│       │
│       │  Tier 4 — Adversarial & Regression (4 reviewers)
│       ├── edge-case-reviewer.agent.md
│       ├── race-condition-reviewer.agent.md
│       ├── regression-reviewer.agent.md
│       └── user-abuse-reviewer.agent.md
├── .claude/
│   └── commands/
│       ├── delegate-to-copilot.md     → YOUR_PROJECT/.claude/commands/delegate-to-copilot.md
│       └── review-copilots-work.md    → YOUR_PROJECT/.claude/commands/review-copilots-work.md
└── AGENTS.md                          → YOUR_PROJECT/AGENTS.md
```

**Alternatively**, use the `/deploy-copilot-agents` Claude Code skill to deploy all agent files to a project automatically.

### Step 2: Customize

Each file has `[CUSTOMIZE]` markers. Replace them with your project's:
- Tech stack (React Native? Next.js? Python?)
- Package manager (npm, pnpm, yarn, bun)
- Build/test/lint commands
- Project-specific review concerns
- Project-specific checklist (if you have one like EXECUTOR_CHECKLIST.md)

### Step 3: Add Override to CLAUDE.md

Add this to your existing CLAUDE.md in the Code Review section:

```markdown
> **Copilot override:** When running in Copilot CLI or VS Code Copilot,
> IGNORE this section and follow `.github/copilot-instructions.md` review pipeline instead
> (25 reviewers across 5 tiers, iterate to 95/A+, max 5 rounds).
```

### Step 4: Test

1. In Claude Code: `/delegate-to-copilot <describe a small task>`
2. In Copilot CLI: `@plan-executor execute copilot-plan.md`
3. Back in Claude Code: `/review-copilots-work`

### Workflow Summary

```
Claude Code                     Copilot CLI                     Claude Code
/delegate-to-copilot  ──►  @plan-executor  ──►  /review-copilots-work
    generates plan          executes + @review-pipeline      verifies quality
                            └→ 25 reviewers, iterate to A+
```
