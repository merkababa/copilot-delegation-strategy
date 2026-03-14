# [PROJECT_NAME] — Copilot Instructions

> Base project rules and conventions are in `CLAUDE.md` (loaded automatically for Claude models).
> This file contains Copilot-specific enhancements — primarily the enhanced review pipeline.

---

## Model Policy (ABSOLUTE)

**All agents use `claude-opus-4.6`. No exceptions. No model tiering. No cost optimization.**

- Orchestrator: Opus 4.6
- Executors: Opus 4.6
- Reviewers: Opus 4.6
- Every subagent, every role, every task: Opus 4.6

---

## Enhanced Review Pipeline (OVERRIDES CLAUDE.md Review Section)

**When running in Copilot CLI or VS Code Copilot, IGNORE the review pipeline in CLAUDE.md. This pipeline REPLACES it entirely.**

Copilot's cost model allows a strictly better pipeline: **6 reviewers, cooperative iteration until 95/A+.**

### Layer 0: Static Analysis Gate

```bash
# [CUSTOMIZE] Replace with your project's commands
npm run lint          # Linter
npx tsc --noEmit      # Type checker
npm test              # Test suite
```

Only code that passes Layer 0 proceeds to reviews.

### Layer 1: Self-Review (Orchestrator)

Before spawning reviewers:
1. Read project quality checklist (if exists)
   <!-- [CUSTOMIZE] Replace with your checklist path, or remove if none -->
2. Verify every changed file against checklist items
3. Fix ALL violations immediately
4. Re-run Layer 0
5. Then proceed to Layer 2

### Layer 2: Six-Reviewer Panel (ALL run in parallel via /fleet)

| # | Role | Agent | Focus |
|---|------|-------|-------|
| 1 | **Architect** | `@architect-reviewer` | System design, scalability, patterns, component boundaries |
| 2 | **Code Quality** | `@code-reviewer` | TypeScript/language strictness, maintainability, DRY, naming |
| 3 | **Security** | `@security-reviewer` | Auth, data validation, secrets, OWASP Top 10 |
| 4 | **UX & Accessibility** | `@ux-reviewer` | UI consistency, a11y, responsive design, i18n |
| 5 | **Testing** | `@testing-reviewer` | Coverage, edge cases, failure modes, mock correctness |
| 6 | **Performance** | `@performance-reviewer` | Memory, CPU, bundle size, network, caching |

<!-- [CUSTOMIZE] Replace/add reviewers relevant to your domain.
     Examples: @data-reviewer (ML/data pipelines), @api-reviewer (backend APIs),
     @mobile-reviewer (iOS/Android specifics), @infra-reviewer (DevOps/IaC) -->

### Grading Scale

- **A+ (95-100)**: Exemplary — no issues, production-ready
- **A (90-94)**: Minor suggestions only (INFO level)
- **B+ (85-89)**: 1-2 fixable issues (WARN level)
- **B (80-84)**: 3-5 fixable issues
- **C or below**: Major issues (BLOCK level)

### Cooperative Iteration Protocol (NO ROUND CAP)

**Target: ALL 6 reviewers at 95/A+ or above. Iterate until achieved.**

```
Round 1: All 6 reviewers run in parallel
  → Collect ALL findings (BLOCK, WARN, INFO)
  → Fix EVERYTHING in one batch
  → Re-run Layer 0
Round 2: Re-review with ALL 6 reviewers
  → If ALL ≥ 95/A+ → DONE
  → If any < 95/A+ → fix and iterate
Round N: Continue (max 5 rounds, then escalate to user)
```

### Reviewer Rules

1. **Evidence-based grades**: Every finding cites specific `file:line` references
2. **Classify ALL findings**: BLOCK / WARN / INFO with severity
3. **Checklist-covered items are NOT findings** unless violated
4. **Focus on what a checklist CANNOT catch**: architecture, logic, race conditions, security
5. **Each reviewer = separate subagent**: No single agent reviews multiple perspectives

---

## Executor Rules

### Pre-commit verification:

```bash
# [CUSTOMIZE] Replace with your project's commands
npm run lint
npx tsc --noEmit
npm test
```

### File Ownership

- Each file belongs to exactly one executor agent — no overlap
- Shared config files (e.g., i18n JSONs) — assign to one agent only
- Orchestrator merges via `git checkout <branch> -- <file>` per agent

---

## Git Workflow

- Never commit to main
- Feature work: `feature/descriptive-name`
- Bug fixes: `fix/descriptive-name`
- Commit types: `feat`, `fix`, `test`, `refactor`, `docs`, `chore`
- Pre-commit: lint + typecheck + tests must pass

---

## Build & Test Commands

```bash
# [CUSTOMIZE] Replace with your project's commands
npm start              # Dev server
npm test               # Test suite
npm run lint           # Linter
npx tsc --noEmit       # Type checker
```

## Tech Stack Reference

<!-- [CUSTOMIZE] Replace with your stack -->
- Framework: [e.g., React Native 0.84 + Expo 55]
- Language: [e.g., TypeScript 5.8 strict]
- State: [e.g., Zustand 5.0]
- Backend: [e.g., Firebase + Supabase]
- Testing: [e.g., Jest 30.2]
- Package Manager: [e.g., npm]
