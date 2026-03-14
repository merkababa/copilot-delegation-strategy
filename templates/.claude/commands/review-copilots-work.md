# Review Copilot's Work

Verify the quality of work executed by Copilot CLI from a delegated plan.

## Process

### Step 1: Pull Latest and Read Results

```bash
git pull origin $(git branch --show-current)
```

Read `copilot-results.md` to understand:
- What tasks were executed
- What the review grades were
- Any issues or skipped tasks

### Step 2: Read the Original Plan

Read `copilot-plan.md` to understand what was requested.

### Step 3: Verify Acceptance Criteria

Run independently (don't trust Copilot's self-reported results):

```bash
# [CUSTOMIZE] Replace with your project's commands
npm test
npm run lint
npx tsc --noEmit
```

### Step 4: Spot-Check Quality

Spawn 2-3 review agents to spot-check the most critical files:
- Focus on files flagged as high-risk in the plan
- Focus on integration points between parallel tasks
- Check for issues that 160K context reviewers might miss (cross-file consistency)

### Step 5: Report

Tell the user:
1. **Pass/Fail**: Did Copilot's work meet the acceptance criteria?
2. **Quality assessment**: How does the code compare to what you would have produced?
3. **Issues found**: Anything the 160K reviewers missed?
4. **Recommendation**: Merge as-is, fix specific issues, or redo in Claude Code?

### Step 6: Clean Up

If approved:
```bash
rm copilot-plan.md copilot-results.md review-results.md
git add -A && git commit -m "chore: clean up copilot handoff files"
```

If issues found: list them for the user to decide next steps.
