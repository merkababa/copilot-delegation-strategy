# Cost Analysis: Final (Corrected)

## Current Setup
- 3x Claude Max $200/mo accounts = **$600/month ($7,200/year)**
- All 3 accounts fully drained
- 5 concurrent projects: emuni, machmad, zap-killer, tortit, + 1 more
- Roadmaps, 4-5 agent pipeline reviews, 11-reviewer app/phase reviews
- 90% of execution delegated to Sonnet 4.6 by Opus orchestrator

## Proposed Setup
- 1x Claude Code Max $200/mo (interactive work, planning, small messages)
- 1x Copilot Pro+ $39/mo (sprint execution, reviews, bulk work)
- **Total: $239/month ($2,868/year)**
- **Savings: $361/month ($4,332/year) — 60% reduction**

## Why Keep 1x Max $200 (Not Go Full Copilot)

Small interactive messages are cheaper on flat-rate Max:

| Daily Messages | Copilot Cost (Opus 3x) | Claude Code Max |
|---------------|----------------------|----------------|
| 50/day | 150 reqs/day × 30 = 4,500/mo → $39 + $120 overage = **$159** | **$200** (flat) |
| 100/day | 300 reqs/day × 30 = 9,000/mo → $39 + $300 = **$339** | **$200** (flat) |
| 150/day | 450 reqs/day × 30 = 13,500/mo → $39 + $480 = **$519** | **$200** (flat) |

**Break-even: ~67 Opus messages/day.** Above that, Max is cheaper. You're well above that threshold.

## Why Opus 4.6 in Copilot (Not Sonnet)

Earlier strategy suggested using Sonnet 4.6 (1x) as executor. Corrected reasoning:

| Model in Copilot | Multiplier | Quality | Sessions in Pro+ (1,500 reqs) |
|-----------------|-----------|---------|------------------------------|
| Sonnet 4.6 | 1x | Good | 1,500 |
| **Opus 4.6** | **3x** | **Best** | **500** |

**500 Opus sessions/month is more than enough.** Your actual usage:
- ~50 sprint execution sessions
- ~20 review sessions
- ~30 bug fix sessions
- = **~100 sessions/month = 300 premium requests**

You'd use only **20% of Pro+ budget** with Opus everywhere. No reason to downgrade to Sonnet.

## Project Size vs 160K Context (Verified)

| Project | Total LOC | Typical PR (files) | PR Token Estimate | Fits 160K? |
|---------|----------|-------------------|-------------------|-----------|
| Emuni | 66K | 5-15 | ~75-100K | **YES** |
| Machmad | 15K | 6-8 | ~40-50K | **YES** |
| Tortit | 50K | 3-8 | ~30-60K | **YES** |
| Zap-killer | 41K | 8-15 | ~60-90K | **YES** |

**No project comes close to needing 1M context for PR-level work.**

## Copilot Premium Request Budget (Monthly)

### Pro+ Plan: 1,500 premium requests/month

| Activity | Volume | Model | Multiplier | Total Reqs |
|----------|--------|-------|-----------|------------|
| Sprint execution | 50 sessions | Opus 4.6 | 3x | 150 |
| Code reviews (11 agents) | 20 sessions | Opus 4.6 | 3x | 60 |
| Bug fixes | 30 sessions | Opus 4.6 | 3x | 90 |
| Standard tasks | 100 turns | Sonnet 4.6 | 1x | 100 |
| **Total** | | | | **400** |
| **Remaining** | | | | **1,100** |

**Utilization: 27%.** You could triple your volume before overages.

### Worst Case (Extremely Heavy Month)

| Activity | Volume | Reqs |
|----------|--------|------|
| 200 Opus sessions | Heavy sprint month | 600 |
| 50 review sessions | 5 projects × 10 reviews | 150 |
| 100 Opus bug fixes | High bug month | 300 |
| 200 Sonnet tasks | Misc | 200 |
| **Total** | | **1,250** |

Still within Pro+ budget. No overages.

## Comparison: API Equivalent Value

| Setup | Monthly Cost | API-Equivalent Token Value |
|-------|-------------|--------------------------|
| 3x Max $200 (current) | $600 | ~$10,000-17,000/mo |
| 1x Max $200 + Pro+ (proposed) | $239 | ~$5,000-8,000 (Max) + ~$2,000-4,000 (Copilot) |
| **Net** | **$239 (-60%)** | **~$7,000-12,000 (-30% tokens but better allocated)** |

You lose ~30% raw token volume but gain it back through better allocation:
- High-volume small messages → Max (flat rate, no waste)
- Bulk execution → Copilot (per-session billing, extremely efficient)

## Risk: Copilot Pricing Changes

Current Coding Agent pricing is labeled "preview." If it changes:

| Scenario | Impact | Action |
|----------|--------|--------|
| Multipliers increase | Need more premium reqs | Buy overages at $0.04; still cheaper |
| Coding Agent goes per-step | More expensive per PR | Move heavy execution back to Max |
| Copilot Pro+ price increase | Higher base cost | Re-evaluate at new price point |
| All pricing doubles | $78/mo Copilot | $278 total — still 54% cheaper than $600 |

**Even in worst-case pricing scenarios, the hybrid approach stays cheaper than 3x Max.**
