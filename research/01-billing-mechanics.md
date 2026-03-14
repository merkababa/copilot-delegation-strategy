# Billing Mechanics Research

## Key Finding: Subagent Billing Bypass is PATCHED

GitHub enforces a **model downgrade restriction** since Feb 2026. Subagents **cannot** use a more expensive model than the orchestrator.

- If orchestrator = Sonnet 4.6 (1x), subagents are forced to Sonnet 4.6 max
- The exploit (VS Code Issue #292452) was reported Feb 3, 2026 — a user launched hundreds of Opus 4.5 subagents via GPT-5 Mini (0x) orchestrator, consuming only 3 credits
- MSRC said billing bypass was out of scope; GitHub fixed it with the model downgrade restriction

## Copilot CLI Billing

- **Per user message/turn** — each prompt costs 1 × model multiplier
- Subagent calls within a turn are **free** (don't independently consume requests)
- But subagents can't use a more expensive model than the orchestrator
- `/fleet` subagents also use the session's default model (no per-subagent model control)

## Copilot Coding Agent Billing (PR-based)

- **1 premium request per session** (since July 10, 2025)
- **Model multiplier DOES apply** — Opus 4.6 = 1 × 3x = 3 premium requests per session
- Entire PR's worth of work regardless of complexity or file count
- 59-minute session time limit
- GitHub Actions minutes billed separately
- "Preview pricing" — may change at GA
- Dedicated SKU tracking since Nov 2025

## Model Multiplier Table (March 2026)

| Model | Multiplier | Effective $/request |
|-------|-----------|-------------------|
| GPT-5 Mini, GPT-4.1, GPT-4o | **0x** (free) | $0.00 |
| Gemini 2.0 Flash | **0.25x** | $0.01 |
| Claude Haiku 4.5 | **0.33x** | $0.013 |
| Claude Sonnet 4.5/4.6, GPT-5.3-Codex | **1x** | $0.04 |
| Claude Opus 4.6 | **3x** | $0.12 |
| Claude Opus 4 | **10x** | $0.40 |
| Claude Opus 4.6 Fast | **30x** | $1.20 |
| GPT-4.5 | **50x** | $2.00 |

## Overage Pricing
- $0.04 per premium request beyond monthly allowance
- Pro: 300/month, Pro+: 1,500/month

## Sources
- [VS Code Issue #292452 — Billing bypass disclosure](https://github.com/microsoft/vscode/issues/292452)
- [GitHub Changelog — Coding Agent 1 request per session](https://github.blog/changelog/2025-07-10-github-copilot-coding-agent-now-uses-one-premium-request-per-session/)
- [GitHub Docs — Requests in Copilot](https://docs.github.com/en/copilot/concepts/billing/copilot-requests)
- [GitHub Docs — Premium Requests Billing](https://docs.github.com/en/billing/concepts/product-billing/github-copilot-premium-requests)
- [Community Discussion #181267 — 3x bug report](https://github.com/orgs/community/discussions/181267)
