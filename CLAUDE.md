# Copilot Delegation Strategy Project

## Purpose
Research, templates, and automation for delegating sprint execution from Claude Code CLI (planning) to GitHub Copilot CLI (execution). Achieves 60%+ cost reduction while maintaining or improving code quality.

## Project Structure

### Research
- `research/` — Raw findings from 5 investigation agents (billing, models, delegation, patterns, sprints)
- `strategy.md` — Synthesized strategy with corrected findings
- `cost-analysis.md` — Cost comparison ($600/mo → $239/mo)

### Templates (Reusable — copy to any project)
- `templates/SETUP.md` — Step-by-step setup guide for new projects
- `templates/.github/copilot-instructions.md` — Copilot-specific review pipeline (25 reviewers across 5 tiers, iterate to A+)
- `templates/.github/agents/` — All agent definitions (plan-executor, review-pipeline, 20 reviewers across 4 tiers)
- `templates/.claude/commands/` — Claude Code skills (delegate-to-copilot, review-copilots-work)
- `templates/AGENTS.md` — Multi-agent orchestration instructions

All templates have `[CUSTOMIZE]` markers for project-specific values.

### Other
- `task-template.md` — Template for generating Copilot-compatible task blocks
- `automation-plan.md` — Phased rollout plan (manual → semi-auto → full auto)

## Workflow (3 Skills)

```
Claude Code                     Copilot CLI                     Claude Code
/delegate-to-copilot  ──►  @plan-executor  ──►  /review-copilots-work
                            └─► @review-pipeline (25 reviewers, iterate to A+)
```

## Applied To
- BabyTracker (pilot project — fully configured, March 2026)
- emuni
- Machmad
- Tortit
- vaad-bayit
- zap-killer
