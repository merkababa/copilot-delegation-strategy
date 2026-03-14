# Task Block Template for Copilot Execution

This template is used by Claude Code (Opus 4.6) when generating implementation plans
that will be executed by Copilot Coding Agent or Copilot CLI.

## Template

```markdown
## Task [PHASE].[NUMBER]: [TITLE]

### Objective
[1-2 sentences: what this task achieves and why]

### Dependencies
- Blocks: [list of task IDs this blocks]
- Depends on: [list of task IDs that must complete first]
- Parallelizable: [YES/NO]

### Files to Modify
- `[exact/path/to/file.ts]` — [what changes]
- `[exact/path/to/file2.ts]` — [what changes]

### Files to Read (Context Only)
- `[exact/path/to/reference.ts]` — [why needed: "contains base class pattern"]
- `[exact/path/to/types.ts]` — [why needed: "shared type definitions"]

### Interface Contract
\`\`\`typescript
// Exact signatures to implement
export interface UserService {
  getById(id: string): Promise<User | null>;
  create(data: CreateUserDto): Promise<User>;
  update(id: string, data: UpdateUserDto): Promise<User>;
  delete(id: string): Promise<void>;
}
\`\`\`

### Implementation Details
[Step-by-step instructions with code snippets showing the pattern to follow]

1. [Step 1 with code example]
2. [Step 2 with code example]
3. [Step 3 with code example]

### Pattern Reference
Follow the same pattern as `[ExistingService]` in `[path/to/existing.ts:lines]`:
\`\`\`typescript
// Relevant excerpt showing the pattern
\`\`\`

### Test Cases
| Input | Expected Output | Edge Case? |
|-------|----------------|------------|
| [input1] | [output1] | No |
| [input2] | [output2] | Yes — [why] |

### Acceptance Criteria
- [ ] All functions match the interface contract above
- [ ] TypeScript compiles with zero errors
- [ ] All tests in `__tests__/[test-file].test.ts` pass
- [ ] No console.log or debug statements
- [ ] Follows error handling pattern from `[reference file]`

### Constraints
- Do NOT modify: [list of files/functions to avoid]
- Max token budget: ~[X]K for this task
- Security: [any security requirements]
```

## Usage Instructions for Claude Code

When generating plans for Copilot delegation, use this template for each task block.

### Rules:
1. Each task block must be **self-contained** — executor has no memory of other tasks
2. Include function signatures from related files, NOT full file contents
3. Target **100K tokens max** per task block (leaves room for Copilot's system prompt)
4. Always include a pattern reference from the existing codebase
5. Acceptance criteria must be **measurable and automated** (tests pass, types compile)
6. Mark parallelizable tasks explicitly for fleet mode execution

### GitHub Issue Format
When creating GitHub issues for Copilot Coding Agent:
- Title: `[Task PHASE.NUMBER] TITLE`
- Body: The task block above
- Labels: `copilot-agent`, `phase-N`
- Assignee: Leave empty (assign @copilot via comment or Agent HQ)
