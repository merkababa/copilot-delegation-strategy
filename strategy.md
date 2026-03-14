# Final Strategy: Claude Code (Interactive) + Copilot CLI (Sprint Execution)

## TL;DR

- **Keep 1x Claude Code Max $200/mo** — for interactive work, quick questions, planning, and small messages (flat rate = cheaper for high-volume back-and-forth)
- **Add Copilot Pro+ $39/mo** — for sprint execution with Opus 4.6 orchestrator + model-tiered subagents
- **Drop 2x Max accounts** — saving **$361/month (60%)**
- **Total: $239/month** instead of $600/month, with equal or better quality

## Why This Works

### Verified Facts (Corrected from Earlier Errors)

1. **Copilot CLI supports per-subagent model selection** — `model:` frontmatter in `.agent.md` works in CLI (user-confirmed March 2026)
2. **No hard subagent limit** in either Claude Code or Copilot CLI — user runs 11 reviewers successfully in both
3. **160K context is sufficient** for all projects — verified against emuni (66K LOC), machmad (15K LOC), tortit (50K LOC), zap-killer (41K LOC). Largest PR (~15 files) uses ~75-100K tokens, well within 160K
4. **Opus 4.6 at 3x multiplier in Copilot is still massively cheaper** than Claude Code Max — 500 Opus PR sessions/month for $39 vs unlimited for $200
5. **Model downgrade restriction**: subagents can't exceed orchestrator's multiplier tier, but Opus orchestrator (3x) allows ALL models as subagents

### The Core Insight

> "Swapping the harness changed benchmark scores by 22%; swapping the model changed them by only 1%."

Your detailed plans ARE the harness. Opus in Copilot (160K) executing a well-specified plan produces the same quality as Opus in Claude Code (1M) — because your PRs never exceed 100K tokens anyway.

## Workflow

```
┌─────────────────────────────────────────────────────┐
│  Claude Code Max $200/mo (1 account)                │
│  ├── Interactive work / quick questions / small msgs│
│  ├── Roadmap planning (long thinking sessions)      │
│  ├── Architecture decisions (when 1M context helps) │
│  └── Sprint plan generation → outputs plan.md       │
└──────────────────────┬──────────────────────────────┘
                       │ plan.md / GitHub issues
                       ▼
┌─────────────────────────────────────────────────────┐
│  Copilot Pro+ $39/mo                                │
│                                                     │
│  Orchestrator: Opus 4.6 (3x multiplier)             │
│  ├── Reads plan.md / issue description              │
│  ├── Spawns model-tiered subagents:                 │
│  │   ├── Opus 4.6 → complex logic, security        │
│  │   ├── Sonnet 4.6 → standard implementation      │
│  │   └── Haiku 4.5 → trivial tasks, formatting     │
│  ├── Execution: autopilot / fleet mode              │
│  ├── Reviews: 11 reviewer subagents (Opus 4.6)     │
│  └── Opens PR with self-review                      │
│                                                     │
│  Budget: 1,500 premium reqs/mo                      │
│  = 500 Opus orchestrator sessions                   │
│  = ~100 sprints with 5 tasks each                   │
└─────────────────────────────────────────────────────┘
```

## Model Selection in Copilot

### Orchestrator: Opus 4.6 (3x)
- Best reasoning for task decomposition and delegation
- Can assign ANY model to subagents (Opus = highest tier, so no downgrade restriction)
- 3x multiplier is still dirt cheap vs Claude Code Max

### Subagent Tiering (via .agent.md files)

| Task Complexity | Subagent Model | Multiplier | Examples |
|----------------|---------------|-----------|---------|
| **Trivial** | Haiku 4.5 | 0.33x | Rename vars, add imports, fix typos |
| **Standard** | Sonnet 4.6 | 1x | Implement feature from spec, write tests, refactor |
| **Complex** | Opus 4.6 | 3x | Security-sensitive, multi-file logic, architecture |
| **Reviews** | Opus 4.6 | 3x | All 11 review perspectives |

### Custom Agent Definitions

Create `.github/agents/` in each project:

```yaml
# .github/agents/security-reviewer.agent.md
---
name: security-reviewer
description: Reviews code for OWASP Top 10, auth issues, injection, XSS
model: claude-opus-4.6
tools:
  - read_file
  - search_code
  - list_files
---

You are a security-focused code reviewer. Check for:
- SQL/NoSQL injection
- XSS vulnerabilities
- Authentication/authorization bypasses
- Secrets in code
- Input validation gaps
...
```

```yaml
# .github/agents/standard-executor.agent.md
---
name: standard-executor
description: Implements well-specified features from task blocks
model: claude-sonnet-4.6
tools:
  - read_file
  - edit_file
  - create_file
  - run_terminal_cmd
  - search_code
---

You are a precise code executor. Follow task specifications exactly.
Never deviate from the spec. Never add unrequested features.
...
```

## What Goes Where

| Activity | Tool | Why |
|----------|------|-----|
| "Hey, quick question about X" | **Claude Code** | Flat rate, instant back-and-forth |
| "Fix this small bug" (interactive) | **Claude Code** | Small messages, flat rate cheaper |
| Planning sprint with 10 tasks | **Claude Code** | Long thinking, 1M context if needed |
| Executing sprint (10 PRs) | **Copilot CLI** | 10 × 3 = 30 reqs ($1.20 overage if any) |
| Code review (11 reviewers) | **Copilot CLI** | Per-subagent model selection works |
| Full app review (major release) | **Either** | Both can do it; Claude Code if >160K context needed |
| Roadmap across all 5 projects | **Claude Code** | May need cross-project context |

## Cost Breakdown

### Monthly Budget

| Item | Cost |
|------|------|
| Claude Code Max 20x | $200 |
| Copilot Pro+ | $39 |
| **Total** | **$239** |
| **Previous** | **$600** |
| **Savings** | **$361/mo ($4,332/year)** |

### Premium Request Budget (Copilot Pro+ 1,500/month)

| Activity | Sessions/mo | Reqs/session | Total Reqs |
|----------|------------|-------------|------------|
| Sprint execution (Opus orchestrator) | 50 | 3 | 150 |
| Code reviews (11 Opus subagents) | 20 sessions | 3 | 60 |
| Bug fixes (Opus interactive) | 30 | 3 | 90 |
| Misc Sonnet tasks | 100 | 1 | 100 |
| **Total** | | | **400** |
| **Remaining budget** | | | **1,100** |

You won't even come close to the 1,500 limit. You could do **3-4x this volume** before hitting overages.

## Tasks NOT to Delegate to Copilot

- Tasks where you need to think interactively with the AI across many small messages (cheaper in Claude Code Max)
- Cross-project planning requiring context from multiple codebases simultaneously
- Exploratory work where you don't know what you're building yet
- Anything that feels like a "conversation" rather than a "job"

## Key Risks and Mitigations

| Risk | Mitigation |
|------|-----------|
| Copilot 160K not enough for edge case | Fall back to Claude Code (you still have Max account) |
| "Preview pricing" on Coding Agent may change | Monitor; worst case you're still under $600/mo |
| Rate limiting on concurrent sessions | Stagger assignments; use Agent HQ |
| Session timeout (59 min) | Break large tasks into <59-min chunks |
| Model downgrade restriction blocks tiering | Use Opus as orchestrator — it's the highest tier, so no restriction applies downward |

## Next Steps

1. **Set up Copilot Pro+ account** ($39/mo)
2. **Create agent definitions** for each project (`.github/agents/`)
3. **Create `copilot-instructions.md`** and `AGENTS.md` per project
4. **Pilot test**: Pick one project, run one sprint through Copilot, compare quality
5. **Build automation**: Claude Code hook → generates plan → creates GitHub issues → Copilot executes
6. **Scale**: Roll out to all 5 projects once validated
7. **Cancel 2 Max accounts** after pilot confirms quality

## Open Questions

1. Which project to pilot first? (machmad is smallest at 15K LOC — lowest risk)
2. Do you already have Copilot Pro+ or need to set it up?
3. Want to build the automation (hooks + scripts) now or after manual pilot?
