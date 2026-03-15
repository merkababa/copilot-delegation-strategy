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

**Core principle: inspect what changed, then select 6-12 relevant reviewers from the 20-reviewer roster. Never blindly run all reviewers.**

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

### Layer 2: Triage → Select → Review

#### Step 1: Triage — Inspect What Changed

Analyze the diff to classify:
- **Domains touched**: UI, API, auth, data/schema, state, payments, i18n, tests, infra, CI/CD, deps
- **Risk level**: routine (docs, tests, refactors) vs critical (auth, payments, data migrations)
- **Scope**: narrow (1-3 files) vs broad (many files, cross-cutting)

#### Step 2: Select 6-12 Reviewers

Pick from the 20-reviewer roster based on what the PR actually touches.

**Tier 1 — Core Quality (select all that apply):**

| Role | Agent | Select When |
|------|-------|------------|
| **Code Quality** | `@code-reviewer` | **Always** — every PR |
| **Architect** | `@architect-reviewer` | New files, new patterns, cross-module changes |
| **Security** | `@security-reviewer` | Auth, input, API endpoints, data access, config |
| **Testing** | `@testing-reviewer` | New features, bug fixes, logic changes |
| **Performance** | `@performance-reviewer` | Loops, queries, rendering, caching, bundle |
| **UX & A11y** | `@ux-reviewer` | UI components, user-facing text, navigation |

**Tier 2 — Domain-Specific (select when domain is touched):**

| Role | Agent | Select When |
|------|-------|------------|
| **Auth & Session** | `@auth-session-reviewer` | Auth flows, tokens, RBAC, login/logout |
| **Data Integrity** | `@data-integrity-reviewer` | Schemas, migrations, type changes, transforms |
| **Error Handling** | `@error-handling-reviewer` | Try-catch, error boundaries, fallback UI |
| **i18n & RTL** | `@i18n-rtl-reviewer` | Translation files, user-facing strings, RTL |
| **API Contract** | `@api-contract-reviewer` | API routes, request/response, breaking changes |
| **State Management** | `@state-management-reviewer` | Stores, context, caching, derived state |

**Tier 3 — Infrastructure (select when infra is touched):**

| Role | Agent | Select When |
|------|-------|------------|
| **Offline & Network** | `@offline-network-reviewer` | Network calls, retry, offline mode, sync |
| **Observability** | `@observability-reviewer` | Logging, analytics, error reporting |
| **Dependency** | `@dependency-reviewer` | package.json, lock files, new/updated deps |
| **CI/CD** | `@ci-cd-reviewer` | CI config, build scripts, deployment, env vars |

**Tier 4 — Adversarial (select for critical/risky PRs):**

| Role | Agent | Select When |
|------|-------|------------|
| **Edge Cases** | `@edge-case-reviewer` | Complex logic, boundaries, nullable, date/time |
| **Race Conditions** | `@race-condition-reviewer` | Async flows, concurrent writes, debounce |
| **Regression** | `@regression-reviewer` | Shared components, widely-imported modules |
| **User Abuse** | `@user-abuse-reviewer` | User input, public endpoints, rate limits |

<!-- [CUSTOMIZE] Add project-specific reviewers to the roster if needed -->

**Selection rules:**
- Minimum 6 — always include `@code-reviewer` + 5 others
- Maximum 12 — if you need more, the PR is probably too large
- When in doubt, include the reviewer
- For critical domains (auth, payments, migrations): include all of Tier 4

#### Step 3: Run Selected Reviewers in Parallel (/fleet)

Each reviewer receives the diff, changed file list, and grades on 0-100 with BLOCK/WARN/INFO findings.

### Grading Scale

- **A+ (95-100)**: Exemplary — no issues, production-ready
- **A (90-94)**: Minor suggestions only (INFO level)
- **B+ (85-89)**: 1-2 fixable issues (WARN level)
- **B (80-84)**: 3-5 fixable issues
- **C or below**: Major issues (BLOCK level)

### Cooperative Iteration Protocol (Max 5 Rounds)

**Target: ALL selected reviewers at 95/A+ or above. Iterate until achieved or 5 rounds exhausted.**

```
Round 1: Selected reviewers run in parallel
  → Collect ALL findings (BLOCK, WARN, INFO)
  → Fix EVERYTHING in one batch
  → Re-run Layer 0
Round 2: Re-run SAME selected reviewers
  → If ALL ≥ 95/A+ → DONE
  → If any < 95/A+ → fix and iterate
  → If fixes touched a new domain → add that domain's reviewer
Round N: Continue until ALL pass or Round 5
  → After Round 5 if not at A+ → ESCALATE to user
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
