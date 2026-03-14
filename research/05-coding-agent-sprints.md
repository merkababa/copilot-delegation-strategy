# Copilot Coding Agent: Real-World Sprint Delegation

## Maximum Complexity Handled Well

**Low-to-medium complexity in well-tested codebases:**
- Adding features following existing patterns
- Bug fixes with clear reproduction steps
- Extending test suites
- Refactoring (consolidating helpers, naming conventions, dedup)
- Documentation improvements

**Struggles with:**
- Multi-file advanced logic with highly interdependent components
- Architectural judgment or novel design decisions
- Broad, underspecified tasks (makes unintended edits)

> "Refactorings across multiple files in large codebases is a tough challenge for any software engineering agent today." — GitHub Docs

## Issue Description Requirements

Treat issue descriptions as prompts. Include:
- Clear problem statement (what and why)
- Acceptance criteria (what "done" looks like)
- File/function pointers (which files/functions)
- Formatting/linting rules
- Background context

Also: `.github/copilot-instructions.md` for repo-wide instructions.

## Concurrency

- Can assign multiple issues simultaneously
- No documented hard concurrency limit, but rate limiting applies
- **Rate limit cascade problem**: many concurrent issues → most/all halt, creating orphaned draft PRs
- Community requests queue-based approach instead of hard limits
- **Agent HQ / Mission Control** (preview Feb 2026) — unified interface for managing multiple agents

## Session Constraints

- **59-minute time limit** per session
- Stopping mid-session and restarting = new session = new premium request + lost context
- Cold boot latency: ~90+ seconds for GitHub Actions environment

## Self-Review (New, Feb 2026)

- Agent runs Copilot code review against its own changes before opening PR
- Iterates on feedback and improves patch before tagging human reviewer
- Developers can comment `@copilot` on PR for further iteration

## Quality: Copilot Coding Agent vs Claude Code

| Dimension | Copilot Coding Agent | Claude Code |
|-----------|---------------------|-------------|
| Speed | Faster (~30ms completions) | Slower but more deliberate |
| Single-file work | Superior | Good but overkill |
| Multi-file reasoning | Good, struggles with interdependencies | Significantly better |
| Complex logic | 15% higher error rate | Higher accuracy |
| Test generation | Strong (best in 5-way test) | Strong |
| Large refactors | Capable but limited | Clear winner with Agent Teams |

## Full Sprint Delegation: Current State

**No documented cases of successful full sprint delegation.** GitHub positions it for "straightforward issues on your backlog."

Best use cases: test coverage, UI improvements, documentation drafts, incremental features.

## Coding Agent Model Selection

Available models (March 2026):
- Claude Opus 4.5, 4.6 (premium)
- Claude Sonnet 4.5, 4.6 (default for Auto)
- GPT-5.1-Codex-Max, GPT-5.2-Codex, GPT-5.3-Codex (premium)

## Billing Anti-Patterns

- Creating PR → stopping midway → retrying (each restart = new session)
- Rapid-fire course corrections mid-session
- Re-running code review for minor fixes
- Auto model selection picking expensive model for trivial task

## Cost Optimization

- GPT-5 mini, GPT-4.1, GPT-4o = **0 premium requests** on paid plans
- "Run-to-completion" with front-loaded context = most cost-effective
- Use Auto model selection for 10% discount

## Sources
- [GitHub Docs — About Copilot coding agent](https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent)
- [GitHub Blog — Coding Agent 101](https://github.blog/ai-and-ml/github-copilot/github-copilot-coding-agent-101-getting-started-with-agentic-workflows-on-github/)
- [GitHub Blog — What's new with coding agent](https://github.blog/ai-and-ml/github-copilot/whats-new-with-github-copilot-coding-agent/)
- [SitePoint — GitHub Copilot vs Claude Code 2026](https://www.sitepoint.com/github-copilot-vs-claude-code-accuracy-speed-2026/)
- [MorphLLM — Claude Code vs Copilot](https://www.morphllm.com/comparisons/claude-code-vs-copilot)
