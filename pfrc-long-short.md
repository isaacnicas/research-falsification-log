# PFRC Long-Short (Post-Filing Reaction Continuation)
**Status:** Rejected — archived by sealed pre-registered falsification test
**Tested:** July 3, 2026 (`archive/pfrc_2026-07-02/`)

## The Hypothesis

The immediate post-filing price reaction to an 8-K (EDGAR-timestamped) continues as a drift: the strongest-reacting names keep drifting the same direction, so ranking the full cross-section by reaction strength within weekly cohorts and going long the top quintile / short the bottom quintile should produce a genuine, dollar-neutral spread return — not just a directional long that happens to work.

## The Test

A single, one-shot, pre-registered falsification test (`PFRC_LONGSHORT_PREREG.md`, sealed 2026-07-03, before any long-short result was computed): rank all qualifying events cross-sectionally within each ISO-week cohort (minimum 5 events per cohort), go long the top quintile (pct_rank ≥ 0.80) and short the bottom quintile (pct_rank ≤ 0.20), dollar-neutral, fixed 20-day hold, beta-hedge the combined book to SPY. Sealed pass/fail table: **PASS** requires long-short Sharpe ≥ 0.7 *and* max drawdown < 30% *and* |beta| ≤ 0.2 *and* winsorized Sharpe ≥ 0.4; **FAIL** triggers if long-short Sharpe < 0.5 *or* |beta| > 0.2 *or* winsorized Sharpe < 0.3. One test only, no follow-up tests to rescue a fail or marginal result — that rule was written into the pre-registration itself.

## What Killed It

1,536 events qualified across 115 weekly cohorts (378 long, 263 short). The long-short book, beta-hedged (post-hedge beta ≈ 0.0000, cleanly neutralized): **Sharpe −0.037**, winsorized Sharpe **−0.222**, max drawdown **−73.4%**, hit rate 50.4% — indistinguishable from a coin flip. Both fail triggers hit simultaneously (Sharpe below 0.5, winsorized Sharpe below 0.3). Half-sample stability made it worse, not better: the first half of the sample averaged a negative cohort return, the second half positive — not even a consistent sign across the test window.

The long side alone, evaluated separately as a reference, was a different story: the same top-quintile events (n=378) produced a standalone Sharpe of 0.39 (0.20 winsorized), and the original, narrower top-decile-plus-positive-reaction corpus (n=209) that motivated this test in the first place had a standalone Sharpe of 0.52 (0.31 winsorized). **The short side didn't underperform** — bottom-quintile names (weak or negative post-filing reactions) did not continue drifting down, consistent with short-horizon mean-reversion rather than continuation on the losing side.

## Why This Matters

The edge here is a right-tail long-momentum effect, not a cross-sectional spread — a meaningfully different, narrower claim than "post-filing reaction continuation" as originally framed. A signal that looks like an anomaly in a long-only backtest can evaporate the moment it's asked to do the one thing a real cross-sectional strategy requires: for the losers to keep losing as reliably as the winners keep winning. Confirming beta was cleanly hedged before reading the Sharpe number also ruled out the more mundane explanation — that the apparent "edge" was just uncompensated market exposure — as a possible confound, and it wasn't it; the fail here isn't a hedging artifact.
