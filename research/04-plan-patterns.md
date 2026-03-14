# Plan-for-Copilot Patterns

## Structuring Plans for 160K Context Window

### The "Lost in the Middle" Problem
LLMs attend strongly to start and end of context but poorly to the middle. A model claiming 200K tokens becomes unreliable around 130K. **For 160K target, plan for ~100-120K of usable reliable context.**

### Recommended Plan Format
```
# Implementation Plan: [Feature Name]
## Overview (goals, non-goals, constraints, acceptance criteria)
## Architecture (affected systems, data flow, key interfaces)
## Task List (numbered, with status/priority/dependency columns)
## Per-Task Blocks (each self-contained)
## Cross-Cutting Concerns (shared types, error handling patterns)
## Verification Checklist
```

### Context Compression Strategies
- Include function **signatures** from related files, not full file contents
- A 500-line file → 20 lines of signatures + 30 lines of relevant functions
- Summarize older conversation parts to free space
- Use retrieval-augmented approaches for relevant chunks only

## Optimal Task Granularity

**One task = one bounded change with clear acceptance criteria.**

- **Too coarse:** "Implement the authentication system" (requires architectural judgment)
- **Right level:** "Add JWT validation middleware in `src/middleware/auth.ts` that checks `Authorization` header, validates against `config.env.JWT_SECRET`, attaches `req.user` with `{ id, email, role }`. See pattern in `src/middleware/logging.ts`."
- **Too fine:** "Add import statement for jsonwebtoken on line 3" (wastes context overhead)

## Self-Contained Task Block Template

Each task block should include:

1. **Task ID and Title** — `Task 2.1: Implement UserService`
2. **Objective** — 1-2 sentences on what and why
3. **File Paths to Modify/Create** — exact paths
4. **File Paths to Read (Context)** — reference files (don't modify)
5. **Function Signatures / Interfaces** — exact signatures to implement
6. **Expected Behavior** — including edge cases
7. **Code Snippets / Patterns** — examples from existing codebase
8. **Test Cases** — specific inputs/outputs
9. **Acceptance Criteria** — measurable "done" conditions
10. **Dependencies** — blocking tasks and shared artifacts
11. **Constraints** — token budget, do-not-modify lists, security requirements

## High Parallelism Plan Structure (DAG-based)

```
## Phase 1 (Sequential — Foundation)
- Task 1.1: Define shared types in types.ts [BLOCKS: 2.1, 2.2, 2.3]

## Phase 2 (Parallel — Implementation)
- Task 2.1: Implement UserService [DEPENDS: 1.1] [PARALLEL]
- Task 2.2: Implement AuthMiddleware [DEPENDS: 1.1] [PARALLEL]
- Task 2.3: Write unit tests for types [DEPENDS: 1.1] [PARALLEL]

## Phase 3 (Sequential — Integration)
- Task 3.1: Integration tests [DEPENDS: 2.1, 2.2, 2.3]
```

Use git worktrees for parallel execution — each agent on separate branch.

## Cross-Task Dependency Handling

1. **Shared Interface Contracts** — Define all types/interfaces upfront before execution
2. **Sequential Integration Phases** — Parallel tasks never touch same files; merge in integration phase
3. **Git-based Coordination** — Each executor on separate branch, coordinator handles merges
4. **"Land the Plane" Pattern** — Agents clean up state at session end, generate ready-to-paste prompts for next session

## Documented Multi-Tool Workflows

1. **Boris Tane (Claude Code creator):** research.md → plan.md → implementation (any tool can execute)
2. **Addy Osmani:** Claude Code for complex reasoning/planning, Cursor for focused editing, automated review tools
3. **"Unbundled AI Stack":** Opus for architecture review, cheaper models for drafting, Copilot for inline
4. **Pimzino spec-workflow:** `/spec-create` with Opus → `/spec-execute` with Sonnet
5. **cc-sdd:** Universal spec-driven development across 8 AI coding tools

## Automation Tools for Bridging

| Tool | Purpose |
|------|---------|
| **cc-copilot-bridge** | Routes Claude Code CLI through Copilot (40+ models, 3-char aliases) |
| **cc-sdd** | Universal spec-driven dev across 8 tools (Claude, Copilot, Cursor, etc.) |
| **Copilot CLI programmatic mode** | `copilot -p PROMPT` for scripted execution |
| **Claude Code Hooks** | PostToolUse hook: detect plan.md written → trigger Copilot execution |
| **spec-workflow-mcp** | MCP server for spec-driven workflows with web dashboard |

## Hook-Based Automation Example

PostToolUse hook: when Claude Code writes `plan.md`, automatically invoke Copilot:
```bash
# In Claude Code hook config
# After plan.md is created, parse into tasks and dispatch to Copilot CLI
```

## Sources
- [Anthropic — Effective context engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [Boris Tane — How I Use Claude Code](https://boristane.com/blog/how-i-use-claude-code/)
- [Addy Osmani — My LLM coding workflow](https://addyosmani.com/blog/ai-coding-workflow/)
- [cc-copilot-bridge](https://github.com/FlorianBruniaux/cc-copilot-bridge)
- [cc-sdd](https://github.com/gotalab/cc-sdd)
- [Pimzino spec-workflow](https://github.com/Pimzino/claude-code-spec-workflow)
- [Anthropic — 2026 Agentic Coding Trends Report](https://resources.anthropic.com/2026-agentic-coding-trends-report)
