# [PROJECT_NAME] — Copilot Instructions

> Base project rules and conventions are in `CLAUDE.md` (loaded automatically for Claude models).
> This file contains Copilot-specific configuration.

---

## Model Policy (ABSOLUTE)

**All agents use `claude-opus-4-6`. No exceptions. No model tiering. No cost optimization.**

- Orchestrator (coordinator): Opus 4.6
- Executors: Opus 4.6
- Reviewers: Opus 4.6
- Every subagent, every role, every task: Opus 4.6

---

## Copilot Review Pipeline

**Copilot uses `@copilot-review` for code review — NOT `@review-pipeline`.**

`@copilot-review` is the enhanced, Copilot-specific review agent that triages changes and selects 6-12 relevant reviewers from the 20-reviewer roster. The standard `@review-pipeline` (6 fixed reviewers) is for general use and is NOT modified by this delegation framework.

### How It Works

1. **Layer 0**: Static analysis gate (lint, typecheck, tests)
2. **Layer 1**: Self-review against project checklist
3. **Layer 2**: Triage → Select 6-12 reviewers → Run in parallel → Iterate to A+ (max 5 rounds)

### Reviewer Roster (20 available — @copilot-review selects 6-12 per PR)

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

### Grading Scale

- **A+ (95-100)**: Exemplary — no issues, production-ready
- **A (90-94)**: Minor suggestions only (INFO level)
- **B+ (85-89)**: 1-2 fixable issues (WARN level)
- **B (80-84)**: 3-5 fixable issues
- **C or below**: Major issues (BLOCK level)

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
