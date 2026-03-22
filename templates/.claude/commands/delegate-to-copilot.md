# Delegate to Copilot

Generate a `copilot-plan.md` for Copilot CLI to execute. One plan = one PR = one focused deliverable.

## ABSOLUTE RULES

1. **Output is ALWAYS `copilot-plan.md`** — NEVER create GitHub issues, NEVER use MCP tools, NEVER call GitHub API
2. **NEVER fix issues yourself** — only generate the plan file
3. **The plan must be self-contained** — Copilot has 160K context per agent, no access to this conversation
4. **One plan = one PR** — if the task needs multiple phases, generate only Phase 1. Use `/split-plan` for multi-phase work
5. **Max 10 files per plan** — if more files are needed, split into multiple plans
6. **Max 100 lines of implementation instructions** — be specific, not verbose

## Determine Task Type

From $ARGUMENTS, determine whether this is:
- **Implementation**: "fix tests", "add feature X", "refactor Y" → generate implementation tasks
- **Review**: "review PR #N", "audit code", "review changes" → generate review tasks
- **Both**: "review and fix" → generate review tasks that include fix instructions

## Step 1: Capture Current State

```bash
git status
git log --oneline -10
git branch --show-current
pnpm typecheck 2>&1 | tail -5
pnpm test 2>&1 | tail -5
```

For review tasks, also identify files:
```bash
gh pr view <NUMBER> --json files -q '.files[].path'
```

## Step 2: Select Reviewers (3-5 relevant ones)

Pick 3-5 reviewers based on what the task touches. Do NOT use all reviewers for every task.

| Task touches | Use these reviewers |
|---|---|
| API routes, middleware | architect-reviewer, code-reviewer, security-reviewer |
| Scraper/matching code | scraping-reviewer, qa-reviewer, code-reviewer |
| Web UI/pages | designer-reviewer, product-reviewer, code-reviewer |
| Database schema | architect-reviewer, security-reviewer, qa-reviewer |
| Search/relevance | product-reviewer, scraping-reviewer, architect-reviewer |
| Extension | security-reviewer, code-reviewer, product-reviewer |
| Infra/Docker | architect-reviewer, security-reviewer, technologist-reviewer |

## Step 3: Generate copilot-plan.md

Use this EXACT structure (do not add extra sections):

```markdown
# Copilot Execution Plan
Generated: [date]
Branch: [current] → `[new-branch-name]`
Type: IMPLEMENTATION | REVIEW | BOTH
Reviewers: [3-5 selected reviewer names]

## Read First (MANDATORY)
Read these files BEFORE writing any code. They establish the patterns you must follow:
- `path/to/pattern-file-1.ts` — [why]
- `path/to/pattern-file-2.ts` — [why]
- (3-5 files max)

## Objective
[1-3 sentences. What and why.]

## File Manifest (STRICT)
Only touch these files. Any file not listed here is OUT OF SCOPE.

**Create:**
- `path/to/new-file.ts`
- `path/to/new-file.test.ts`

**Modify:**
- `path/to/existing.ts` — [what to change]

## TypeScript Interfaces
[Exact interfaces for any new functions/APIs. Copilot must implement to match these, not invent its own.]

```typescript
// Example — include real interfaces
interface ParsedTitle {
  brand: string | null;
  productType: ProductType;
  canonicalKey: string | null;
}
```

## Anti-Patterns (DO NOT)
- Do NOT inline code from other packages — import instead
- Do NOT use `any` type — use proper types or `unknown`
- Do NOT create new files outside the manifest
- [Add task-specific anti-patterns]

## Implementation Steps
1. [Step with specific details, max 2-3 sentences each]
2. [Include code snippets from existing codebase for patterns to follow]
3. [Max 10 steps, max 100 lines total]

## Verify Script
Run this after implementation. ALL must pass:
```bash
pnpm typecheck
pnpm test
pnpm build
# Task-specific checks:
[e.g., grep -r "parseProductTitle" packages/scrapers/src/matching/title-parser.ts || echo "FAIL: title-parser not created"]
[e.g., test $(pnpm test 2>&1 | grep -c "passing") -gt 3500 || echo "FAIL: test count dropped"]
```

## Output
Write results to `copilot-results.md`:
1. Files changed (must match manifest)
2. Full `pnpm typecheck` output
3. Full `pnpm test` output (last 30 lines)
4. Verify script output
5. Any deviations from plan with justification

Then create PR with title: `[type]: [short description]`
```

## Step 4: Validate Before Saving

Before writing the file, verify:
1. **Single PR scope** — could this be merged as one coherent PR? If not, split
2. **Self-contained** — no references to "this conversation" or context Copilot won't have
3. **File manifest complete** — every file listed with exact paths
4. **Interfaces included** — TypeScript types for any new code
5. **< 100 lines of instructions** — be specific, not verbose
6. **Read-first files exist** — verify the paths are real with Glob/Read

## Step 5: Save and Report

```bash
git add copilot-plan.md
git commit -m "chore: copilot execution plan — [short description]"
git push
```

Tell the user:
- Plan saved to `copilot-plan.md`
- Files in scope: [count]
- Selected reviewers: [names]
- "Run Copilot CLI to execute"
