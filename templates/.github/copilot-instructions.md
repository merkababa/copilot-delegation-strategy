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

Copilot's cost model allows a strictly better pipeline: **25 reviewers across 5 tiers, cooperative iteration until 95/A+.**

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

### Layer 2: 25-Reviewer Panel (ALL run in parallel via /fleet)

#### Tier 1 — Core Quality (always relevant)

| # | Role | Agent | Focus |
|---|------|-------|-------|
| 1 | **Architect** | `@architect-reviewer` | System design, scalability, patterns, component boundaries |
| 2 | **Code Quality** | `@code-reviewer` | TypeScript/language strictness, maintainability, DRY, naming |
| 3 | **Security** | `@security-reviewer` | Auth, data validation, secrets, OWASP Top 10 |
| 4 | **UX & Accessibility** | `@ux-reviewer` | UI consistency, a11y, responsive design, i18n |
| 5 | **Testing** | `@testing-reviewer` | Coverage, edge cases, failure modes, mock correctness |
| 6 | **Performance** | `@performance-reviewer` | Memory, CPU, bundle size, network, caching |

#### Tier 2 — Domain-Specific (most PRs)

| # | Role | Agent | Focus |
|---|------|-------|-------|
| 7 | **Auth & Session** | `@auth-session-reviewer` | Auth flows, session management, token handling, RBAC |
| 8 | **Data Integrity** | `@data-integrity-reviewer` | Schema validation, migration safety, type alignment |
| 9 | **Error Handling** | `@error-handling-reviewer` | Try-catch coverage, error boundaries, user messages, crash context |
| 10 | **i18n & RTL** | `@i18n-rtl-reviewer` | Translation quality, RTL layout, locale formatting, key completeness |
| 11 | **API Contract** | `@api-contract-reviewer` | Request/response shapes, validation, breaking changes, backward compat |
| 12 | **State Management** | `@state-management-reviewer` | Store patterns, cache strategy, stale state, race conditions |

#### Tier 3 — Infrastructure & Ops (when touching infra/deps/CI)

| # | Role | Agent | Focus |
|---|------|-------|-------|
| 13 | **Offline & Network** | `@offline-network-reviewer` | Offline behavior, network errors, retry logic, sync conflicts |
| 14 | **Observability** | `@observability-reviewer` | Error reporting context, analytics, structured logging, monitoring |
| 15 | **Dependency** | `@dependency-reviewer` | Package health, deprecated deps, security advisories, bundle impact |
| 16 | **CI/CD** | `@ci-cd-reviewer` | Pipeline impact, migration safety, deployment order, env config |

#### Tier 4 — Adversarial & Regression (critical PRs)

| # | Role | Agent | Focus |
|---|------|-------|-------|
| 17 | **Edge Cases** | `@edge-case-reviewer` | Boundary values, empty states, null/undefined, scale limits, timezone |
| 18 | **Race Conditions** | `@race-condition-reviewer` | Async timing, double-submit, stale closures, concurrent writes |
| 19 | **Regression** | `@regression-reviewer` | Cross-feature impact, shared component changes, type contract breaks |
| 20 | **User Abuse** | `@user-abuse-reviewer` | XSS, injection, unauthorized access, rate abuse, input validation bypass |

#### Tier 5 — Orchestration (always runs)

| # | Role | Agent | Focus |
|---|------|-------|-------|
| 21-25 | **Pipeline + Executors** | `@review-pipeline`, `@plan-executor`, etc. | Orchestration, execution, coordination |

<!-- [CUSTOMIZE] Replace/add reviewers relevant to your domain.
     Examples: @data-reviewer (ML/data pipelines), @mobile-reviewer (iOS/Android specifics),
     @infra-reviewer (DevOps/IaC) -->

### Grading Scale

- **A+ (95-100)**: Exemplary — no issues, production-ready
- **A (90-94)**: Minor suggestions only (INFO level)
- **B+ (85-89)**: 1-2 fixable issues (WARN level)
- **B (80-84)**: 3-5 fixable issues
- **C or below**: Major issues (BLOCK level)

### Cooperative Iteration Protocol (Max 5 Rounds)

**Target: ALL 25 reviewers at 95/A+ or above. Iterate until achieved or 5 rounds exhausted.**

```
Round 1: All 25 reviewers run in parallel
  → Collect ALL findings (BLOCK, WARN, INFO)
  → Fix EVERYTHING in one batch
  → Re-run Layer 0
Round 2: Re-review with ALL 25 reviewers
  → If ALL ≥ 95/A+ → DONE
  → If any < 95/A+ → fix and iterate
Round N: Continue until ALL pass or Round 5
  → After Round 5 if not at A+ → ESCALATE to user
```

**Every round re-reviews ALL 25 reviewers** — not just the ones that failed. Fixes in one area can introduce issues detectable by other reviewers.

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
