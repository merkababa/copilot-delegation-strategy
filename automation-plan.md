# Automation Plan: Claude Code → Copilot Handoff

## Overview

Automate the workflow where Claude Code generates sprint plans and Copilot CLI executes them.

## Phase 1: Manual Pilot (Week 1)

No automation — validate the workflow manually.

### Steps
1. In Claude Code, generate a sprint plan using the task-template.md format
2. Copy each task block into a GitHub issue manually
3. In Copilot CLI, open the repo and run:
   ```bash
   copilot --model claude-opus-4.6
   # then paste: "Implement the task described in issue #N"
   ```
4. Or use Coding Agent: assign issue to @copilot
5. Review the PR in Copilot CLI with 25 reviewer subagents
6. Compare quality against what Claude Code would have produced

### Success Criteria
- Copilot produces code that passes CI (tests, lint, typecheck)
- Review pipeline catches any quality issues
- No rework needed beyond what reviews flag
- Time-to-PR is comparable or faster

## Phase 2: Semi-Automated (Week 2-3)

### 2a. Claude Code Skill for Plan Generation

Create a custom skill in Claude Code that generates Copilot-compatible plans:

```markdown
# .claude/skills/generate-copilot-plan.md
---
name: generate-copilot-plan
description: Generate a sprint plan formatted for Copilot CLI execution
---

When the user asks to generate a Copilot plan:

1. Read the current codebase state (git status, recent changes)
2. Ask the user for sprint goals
3. Decompose into tasks using the DAG dependency format
4. For each task, generate a self-contained task block following task-template.md
5. Each task block must:
   - Fit within 100K tokens of context
   - Include exact file paths, function signatures, patterns
   - Include acceptance criteria (tests, type safety)
   - Mark parallelizable tasks
   - Specify recommended model tier (Haiku/Sonnet/Opus)
6. Output to plan.md in the project root
7. Optionally create GitHub issues for each task
```

### 2b. Copilot Agent Definitions

Create standardized agent definitions in each project:

```
.github/agents/
├── executor-trivial.agent.md    (model: claude-haiku-4.5)
├── executor-standard.agent.md   (model: claude-sonnet-4.6)
├── executor-complex.agent.md    (model: claude-opus-4.6)
├── reviewer-security.agent.md   (model: claude-opus-4.6)
├── reviewer-performance.agent.md (model: claude-opus-4.6)
├── reviewer-architecture.agent.md (model: claude-opus-4.6)
├── reviewer-testing.agent.md    (model: claude-opus-4.6)
├── reviewer-ux.agent.md         (model: claude-opus-4.6)
├── reviewer-i18n-rtl.agent.md   (model: claude-opus-4.6)
├── reviewer-error-handling.agent.md (model: claude-opus-4.6)
├── reviewer-accessibility.agent.md (model: claude-opus-4.6)
├── reviewer-consistency.agent.md (model: claude-opus-4.6)
├── reviewer-edge-cases.agent.md (model: claude-opus-4.6)
└── reviewer-documentation.agent.md (model: claude-opus-4.6)
```

### 2c. Copilot Instructions Per Project

```markdown
# .github/copilot-instructions.md

## Project: [name]
## Stack: React Native + Expo / Next.js + NestJS / etc.
## Language: TypeScript (strict mode)
## Package Manager: pnpm
## Test Framework: Jest + React Native Testing Library
## Build: npx expo start / pnpm turbo build
## Test: pnpm test
## Lint: pnpm lint
## Typecheck: pnpm typecheck

## Coding Standards
- All code in TypeScript strict mode
- No `any` types
- No console.log in production code
- Conventional commits: feat:, fix:, chore:, docs:, refactor:
- Hebrew RTL support required (where applicable)
- Error handling: try-catch with contextual messages
- Test coverage: >80% for new code

## Architecture
[project-specific architecture notes]
```

## Phase 3: Full Automation (Week 4+)

### 3a. Claude Code Hook: Auto-Create GitHub Issues from Plan

```json
// .claude/hooks.json
{
  "postToolUse": [
    {
      "trigger": {
        "tool": "Write",
        "filePattern": "**/plan.md"
      },
      "command": "bash scripts/plan-to-issues.sh"
    }
  ]
}
```

```bash
#!/bin/bash
# scripts/plan-to-issues.sh
# Parses plan.md into individual tasks and creates GitHub issues

PLAN_FILE="plan.md"
REPO=$(gh repo view --json nameWithOwner -q .nameWithOwner)

# Parse task blocks from plan.md
# Each task block starts with "## Task" and ends before the next "## Task"
# Create a GitHub issue for each task block

awk '/^## Task /{if(task)print task; task=""} /^## Task /{collecting=1} collecting{task=task "\n" $0} END{if(task)print task}' "$PLAN_FILE" | while IFS= read -r -d '' task_block; do
  TITLE=$(echo "$task_block" | head -1 | sed 's/^## //')
  BODY="$task_block"
  gh issue create --title "$TITLE" --body "$BODY" --label "copilot-agent"
done

echo "Issues created. Assign to @copilot via Agent HQ or manually."
```

### 3b. Copilot CLI Batch Execution Script

```bash
#!/bin/bash
# scripts/execute-sprint.sh
# Executes all copilot-agent labeled issues via Copilot CLI

ISSUES=$(gh issue list --label "copilot-agent" --state open --json number,title -q '.[].number')

for ISSUE_NUM in $ISSUES; do
  echo "Executing issue #$ISSUE_NUM..."

  # Read issue body as the task spec
  TASK_SPEC=$(gh issue view "$ISSUE_NUM" --json body -q .body)

  # Execute via Copilot CLI programmatic mode
  copilot -p "Implement the following task specification exactly as described. Create a branch, implement, run tests, and open a PR.\n\n$TASK_SPEC" --model claude-opus-4.6

  # Mark issue as in-progress
  gh issue edit "$ISSUE_NUM" --add-label "in-progress"
done
```

### 3c. Review Automation Script

```bash
#!/bin/bash
# scripts/review-prs.sh
# Reviews all open PRs from copilot-agent with 25 reviewer agents

PRS=$(gh pr list --label "copilot-agent" --state open --json number -q '.[].number')

for PR_NUM in $PRS; do
  echo "Reviewing PR #$PR_NUM with 25 agents..."

  DIFF=$(gh pr diff "$PR_NUM")

  copilot -p "/fleet Review this PR from 25 perspectives using these agents:
  @reviewer-security
  @reviewer-performance
  @reviewer-architecture
  @reviewer-testing
  @reviewer-ux
  @reviewer-i18n-rtl
  @reviewer-error-handling
  @reviewer-accessibility
  @reviewer-consistency
  @reviewer-edge-cases
  @reviewer-documentation

  PR diff:
  $DIFF" --model claude-opus-4.6
done
```

## Phase 4: Monitoring & Optimization

### Track These Metrics
1. **Premium request consumption** — are you staying within 1,500/month?
2. **Quality comparison** — does Copilot output pass reviews at the same rate as Claude Code?
3. **Time-to-PR** — is Copilot faster, slower, or same?
4. **Rework rate** — how often do reviews flag issues requiring re-implementation?
5. **Cost per sprint** — track actual premium request usage per sprint

### Optimization Levers
- If quality is equal: move MORE work to Copilot, reduce Max usage
- If quality degrades on specific task types: keep those in Claude Code
- If premium requests run low: downgrade non-critical subagents to Sonnet/Haiku
- If Copilot pricing changes: re-evaluate break-even

## Tools to Evaluate

| Tool | Purpose | Priority |
|------|---------|----------|
| [cc-sdd](https://github.com/gotalab/cc-sdd) | Universal spec format across tools | High |
| [cc-copilot-bridge](https://github.com/FlorianBruniaux/cc-copilot-bridge) | Route between providers | Medium |
| [Agent HQ](https://github.blog/ai-and-ml/github-copilot/how-to-orchestrate-agents-using-mission-control/) | Manage multiple Copilot agents | High |
| [spec-workflow-mcp](https://github.com/Pimzino/spec-workflow-mcp) | MCP server for spec-driven dev | Medium |
