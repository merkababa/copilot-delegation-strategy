# Model Comparison: Context Windows & Capabilities in Copilot

## Context Window Reality Check

The Copilot harness caps ALL models below their native capability:

| Model | Native Context | Copilot Effective Context | Multiplier | Best For |
|-------|---------------|--------------------------|-----------|---------|
| GPT-5.2-Codex | 400K | **400K (CONFIRMED full use)** | 1x | Orchestration (best ratio) |
| GPT-5.3-Codex | 128K | 128K | ~1x | Execution (50% fewer tokens) |
| GPT-5.4 | 1.05M | **400K-1M (conflicting reports)** | Unknown (new Mar 5) | TBD |
| Claude Opus 4.6 | 1M | **128-160K (harness-limited)** | 3x | Deep reasoning (but expensive) |
| Claude Sonnet 4.6 | 200K | ~160K | 1x | Balanced execution |
| Gemini 2.5 Pro | 1M | Unreliable (10-15 files max reported) | 1x | Theoretically best, practically worse |

## Key Insight: Harness > Model

> "Swapping the harness changed benchmark scores by 22%; swapping the model changed them by only 1%."

This means: the **quality of your plan and task decomposition** matters far more than which model executes it.

## Best Orchestrator Candidates (Large Context + Low Multiplier)

1. **GPT-5.2-Codex** — 400K confirmed usable, 1x multiplier. THE sweet spot.
2. **Claude Sonnet 4.6** — 160K, 1x. Good reasoning, cheaper than Opus.
3. **GPT-5.3-Codex** — 128K but uses 50% fewer tokens effectively. 1x.
4. **Gemini 2.5 Pro** — 1M advertised, 1x, but unreliable in practice.

## GPT-5.2-Codex vs Claude Opus 4.6

| Dimension | GPT-5.2-Codex | Claude Opus 4.6 |
|-----------|--------------|----------------|
| SWE-bench | ~80% | ~80% |
| Copilot context | 400K (full) | 160K (capped) |
| Multiplier | 1x | 3x |
| Speed | Fast | Slower but deeper |
| Orchestration | Single-agent | Agent Teams (multi-agent) |
| Cost per turn | $0.04 | $0.12 |

## GPT-5.4 (Released March 5, 2026)

- OpenAI's newest reasoning model with native computer-use
- 1.05M native context window
- Copilot reports suggest 400K-1M effective (too new to confirm)
- Multiplier not yet documented — likely 3x-10x based on precedent
- Worth monitoring as potential orchestrator once multiplier is known

## Sources
- [Interconnects.ai — Opus 4.6 vs Codex 5.3](https://www.interconnects.ai/p/opus-46-vs-codex-53)
- [GitHub Docs — Supported AI models](https://docs.github.com/en/copilot/reference/ai-models/supported-models)
- [VS Code Issue #298900 — Opus context under-reported](https://github.com/microsoft/vscode/issues/298900)
- [Community Discussion #186340 — context_window vs max_prompt](https://github.com/orgs/community/discussions/186340)
- [DataCamp — GPT-5.4 vs Claude Opus 4.6](https://www.datacamp.com/blog/gpt-5-4-vs-claude-opus-4-6)
