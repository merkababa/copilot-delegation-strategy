# Copilot CLI Delegation Architecture

## Modes Available

### Autopilot Mode
- Executes autonomously without stopping for approval
- Best for "well-defined tasks like writing tests, refactoring, fixing CI failures"
- NOT ideal for: open-ended exploration, novel design decisions, tasks requiring nuanced judgment
- Burns premium requests faster than interactive mode

### Plan Mode (Shift+Tab)
- Creates structured implementation plan with checkboxes
- Saved to `plan.md` in session folder
- Ctrl+Y to view/edit in Markdown editor before execution
- Can transition to autopilot after plan approval

### Fleet Mode (/fleet)
- Decomposes work into parallel subagents
- Each subagent has isolated context window
- Orchestrator manages dependencies and converges results
- `/tasks` shows background task status
- **All subagents use session's default model** (no per-agent model control)

## Subagent Model Control

- `model:` frontmatter in `.agent.md` files is **confirmed working in CLI** (user-verified March 2026)
- Earlier research incorrectly stated this was ignored by CLI — this has been corrected
- **Model downgrade restriction**: subagents can't exceed orchestrator's multiplier tier (irrelevant when using Opus everywhere)

## Context Sharing Between Orchestrator and Subagents

- Subagents have **isolated context windows** — do NOT share orchestrator's history
- Each subagent receives only task-specific context (filtered by orchestrator)
- Results flow back to orchestrator for synthesis
- Deliberate design: prevents context window bloat

## Configuration Files (CLAUDE.md Equivalents)

| File | Location | Purpose |
|------|----------|---------|
| `.github/copilot-instructions.md` | Repo root | Always-on project-wide guidance |
| `.github/instructions/*.instructions.md` | `.github/instructions/` | Path-specific instructions |
| `AGENTS.md` | Repo root or cwd | Multi-agent instructions |
| `CLAUDE.md` | Repo root | Claude-model-specific (read by CLI when using Claude) |
| `~/.copilot/config` | User home | User-level config (URLs, tools) |
| `.github/agents/*.agent.md` | `.github/agents/` | Custom agent definitions |

All instruction files **combine** (not override) — merged together.

## Custom Agent Definitions (.agent.md)

- YAML frontmatter: `name`, `description`, `tools`, `mcpServers`
- Markdown body: behavioral instructions, persona, boundaries
- Max 30,000 characters for instruction body
- Invoked with `@agent-name` in prompts
- Tool filtering via frontmatter

## Tool Capabilities Available to Subagents

**File/Code:** create, edit, view, search (grep/glob), shell/bash execution
**Git:** log, show, diff, status
**Package Managers:** npm list, cargo check, go mod verify (read-only)
**GitHub MCP:** 21 tools across repos, issues, PRs, workflows
**Web:** `web_fetch` (controlled via config)
**Control flags:** `--available-tools`, `--excluded-tools`, `--disable-builtin-mcps`

## Auto-Compaction
- Fires at **95% context window usage**
- Known information loss issue — [Issue #1688](https://github.com/github/copilot-cli/issues/1688)
- Performance degrades significantly at 45-60% context usage with slower models

## Sources
- [GitHub Docs — Autopilot Mode](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/autopilot)
- [GitHub Docs — Fleet Command](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/fleet)
- [GitHub Docs — Custom Agents](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli)
- [DeepWiki — Agent Modes & Subagents](https://deepwiki.com/github/copilot-cli/3.6-agent-modes-and-subagents)
- [DeepWiki — Context & Token Management](https://deepwiki.com/github/copilot-cli/3.7-context-and-token-management)
- [GitHub Blog — How to write a great agents.md](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/)
