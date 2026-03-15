# Copilot Delegation Strategy

Claude Code → Copilot CLI delegation framework for 60% cost reduction with equal or better code quality.

## What This Is

A complete framework for using Claude Code CLI for planning/interactive work and GitHub Copilot CLI for sprint execution, with a 25-reviewer pipeline that iterates to 95/A+.

## Cost Savings

| Before | After | Savings |
|--------|-------|---------|
| 3x Claude Max $200 = $600/mo | 1x Max $200 + Copilot Pro+ $39 = $239/mo | $361/mo (60%) |

## The 3-Skill Workflow

```
Claude Code              Copilot CLI              Claude Code
/delegate-to-copilot → @plan-executor → /review-copilots-work
                        └→ @review-pipeline (25 reviewers, iterate to A+)
```

## Quick Start

See [templates/SETUP.md](templates/SETUP.md) for setup instructions.

## Repository Structure

- `research/` — Deep research on billing, models, delegation patterns
- `strategy.md` — Final strategy and workflow
- `cost-analysis.md` — Detailed cost breakdown
- `templates/` — Copy to any project (see SETUP.md)

## Applied To

- BabyTracker, Nanny (emuni), PET_CARE_APP_PLAN (Machmad), Tortit, vaad-bayit, zap-killer
