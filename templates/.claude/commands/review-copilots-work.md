# Review Copilot's Work

Verify quality of work executed by Copilot CLI. Run 3-5 relevant reviewers until all grade A+.

## Step 1: Pull and Identify Changes

```bash
git pull origin $(git branch --show-current)
```

Read `copilot-plan.md` (what was requested) and `copilot-results.md` (what was delivered).

## Step 2: Manifest Compliance Check

Compare files changed vs the plan's File Manifest:
```bash
# Files Copilot actually changed
git diff --name-only $(git merge-base HEAD main)..HEAD
```

Flag:
- **Off-manifest changes** — files changed that weren't in the plan (red flag)
- **Missing files** — files in the plan that weren't created/modified (incomplete)
- **Scope creep** — unrelated changes mixed in

## Step 3: Run Verification Gate

Run independently — don't trust Copilot's self-reported results:
```bash
pnpm typecheck
pnpm test
pnpm build
```

If the plan included a verify script, run that too.

## Step 4: Interface Compliance Check

If the plan specified TypeScript interfaces:
- Read the implemented files
- Compare function signatures and return types against the plan spec
- Check that test assertions match the implementation (not a different shape)

## Step 5: Code Quality — Run 3-5 Relevant Reviewers

Select reviewers based on what files were changed (same table as delegate-to-copilot).
Launch them in parallel. Each reviewer should focus ONLY on the changed files, not the full codebase.

```
Agent prompt template:
"Review ONLY these files for [reviewer-focus]:
[list of changed files]
Grade each area A+ to F. List ALL issues as BLOCK/WARN/INFO with file:line.
Focus on: [reviewer-specific focus areas]"
```

## Step 6: Fix Loop (Until All A+)

If any reviewer grades below A+:
1. Collect all BLOCK + WARN findings
2. Fix them via executor agents (orchestrator never writes code)
3. Re-run ONLY the reviewers that found issues
4. Repeat until all selected reviewers grade A+
5. Max 3 rounds — if not A+ after 3 rounds, report to user

## Step 7: Check for Copilot Anti-Patterns

Common mistakes Copilot makes — check for these specifically:
- [ ] **Inlined code** — functions copy-pasted from other packages instead of imported
- [ ] **Test/implementation mismatch** — tests assert one shape, implementation returns another
- [ ] **Invented patterns** — new patterns that don't match existing codebase conventions
- [ ] **Dead code** — unused imports, unreachable branches, commented-out code
- [ ] **Missing error handling** — try/catch without meaningful error messages
- [ ] **Hardcoded values** — magic numbers, URLs, or config that should use env/constants

## Step 8: Report

Tell the user:
1. **Manifest compliance**: files matched / off-manifest / missing
2. **Verification**: typecheck / test / build pass/fail
3. **Reviewer grades**: table of reviewer → grade (all must be A+)
4. **Anti-pattern check**: any Copilot-specific issues found
5. **Verdict**: MERGE / FIX (with specific issues) / REJECT (with reason)

## Step 9: Clean Up (if MERGE)

```bash
rm -f copilot-plan.md copilot-results.md
git add -A && git commit -m "chore: clean up copilot handoff files"
```

If PR exists, merge it:
```bash
gh pr merge --squash --delete-branch
```
