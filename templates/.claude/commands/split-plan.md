# Split Plan

Split a large task into sequential single-PR copilot plans. Each plan is self-contained and independently mergeable.

## When to Use
- Task requires changes to > 10 files
- Task has multiple distinct phases (e.g., "build X, then use X to do Y")
- Task touches multiple packages/apps that should be reviewed separately

## Process

### Step 1: Analyze the Task
From $ARGUMENTS, identify the natural split points:
- **Data layer** changes (schema, migrations, DB queries) → Plan A
- **Logic layer** changes (matching, processing, business logic) → Plan B
- **API layer** changes (routes, middleware, endpoints) → Plan C
- **UI layer** changes (pages, components, styles) → Plan D
- **Test/monitoring** (integration tests, dashboards) → Plan E

### Step 2: Determine Dependencies
Build a dependency graph:
```
Plan A (schema) → Plan B (logic uses new schema)
Plan B (logic) → Plan C (API exposes new logic)
Plan C (API) → Plan D (UI calls new API)
Plans A-D → Plan E (tests verify everything)
```

### Step 3: Generate Plan Sequence
For each plan in dependency order:
1. Run `/delegate-to-copilot` with the scoped task
2. Name the plan: `copilot-plan-1-[name].md`, `copilot-plan-2-[name].md`, etc.
3. Each plan's "Read First" section should include files from previous plans' output
4. Each plan has its own verify script and reviewer set

### Step 4: Save Manifest
Create `copilot-plan-sequence.md`:
```markdown
# Plan Sequence
Total plans: [N]
Estimated PRs: [N]

## Order (execute sequentially)
1. `copilot-plan-1-schema.md` — [description] — Reviewers: [list]
2. `copilot-plan-2-logic.md` — [description] — Reviewers: [list]
3. `copilot-plan-3-api.md` — [description] — Reviewers: [list]

## Between Plans
After each plan's PR is merged:
1. Run `/review-copilots-work`
2. Merge if A+
3. `git pull origin main` before starting next plan
```

### Step 5: Report
Tell the user:
- Number of plans generated
- Dependency order
- Total files across all plans
- "Execute plans in order. Merge each before starting the next."
