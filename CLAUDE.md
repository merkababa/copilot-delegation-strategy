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
- `templates/.github/copilot-instructions.md` — Copilot-specific config (references @copilot-review, NOT @review-pipeline)
- `templates/.github/agents/` — All agent definitions:
  - `plan-executor` — orchestrator/coordinator for Copilot task execution
  - `review-pipeline` — standard 6-reviewer pipeline (NOT modified by this framework)
  - `copilot-review` — Copilot-specific: triages changes, selects 6-12 from 20-reviewer roster, iterates to A+
  - 20 reviewer agents across 4 tiers
- `templates/.claude/commands/` — Claude Code skills:
  - `delegate-to-copilot` — generate single-PR plans with file manifest, verify script, 3-5 reviewers
  - `review-copilots-work` — manifest compliance, interface check, 3-5 reviewers to A+, anti-pattern checklist
  - `split-plan` — decompose large tasks into sequential single-PR plans with dependency ordering
- `templates/AGENTS.md` — Multi-agent orchestration instructions

All templates have `[CUSTOMIZE]` markers for project-specific values.

### Other
- `task-template.md` — Template for generating Copilot-compatible task blocks
- `automation-plan.md` — Phased rollout plan (manual → semi-auto → full auto)

## Workflow (4 Skills)

```
Claude Code                     Copilot CLI                     Claude Code
/split-plan (if large task)
  └─► /delegate-to-copilot  ──►  @plan-executor  ──►  /review-copilots-work
       (1 plan = 1 PR)           └─► @copilot-review       (3-5 reviewers → A+)
                                     (triage → select reviewers → iterate)
```

### Key Design Principles (v2 — March 2026)
- **One plan = one PR** — never multi-phase plans (use /split-plan instead)
- **Max 10 files per plan** — keeps scope reviewable
- **3-5 relevant reviewers** — not all 20; selected by what the task touches
- **Mandatory verify script** — Copilot runs it; Claude validates on review
- **File manifest** — strict list of files to touch; off-manifest changes flagged
- **Anti-pattern checklist** — catches common Copilot mistakes (inlined code, test/impl mismatch)

## Key Distinction

- `@review-pipeline` = standard 6-reviewer pipeline (unchanged, general-purpose)
- `@copilot-review` = Copilot-only enhanced review (triage, 20-reviewer roster, select 6-12)

## Applied To
- BabyTracker (pilot project — fully configured, March 2026)
- Nanny / emuni (C:\Users\t2tec\Projects\Nanny)
- PET_CARE_APP_PLAN / Machmad (C:\Users\t2tec\Israeli_Premium_App_Project\PET_CARE_APP_PLAN)
- Tortit
- vaad-bayit
- zap-killer
- Israeli_Premium_App_Project (parent repo)
