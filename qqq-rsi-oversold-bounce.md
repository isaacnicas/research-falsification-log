# QQQ RSI Oversold-Bounce
**Status:** Rejected — Deflated Sharpe Ratio far below the noise ceiling once selection bias is priced in
**Tested:** July 2026, `quant_lab` (`paper_trading_spec.md`, killed 2026-07-09; `docs/backtester_mcp_audit.md`)

## The Hypothesis

`ret_1d_z < 1.0 AND rsi < 30` on QQQ daily closes — buy when the 1-day return z-score is unremarkable but 14-period RSI shows oversold — captures a short-horizon mean-reversion bounce. Backtested net Sharpe: 1.284.

## The Test

Deflated Sharpe Ratio (DSR), which corrects the observed Sharpe for how many trials were actually run to find it. This signal was the best performer of a full 18-ticker × 200-signal sweep (n_trials = 3,600) — not just competing against its own 200 QQQ-only variants. DSR asks: given that many independent trials, what Sharpe would pure noise have produced by chance at the 95th percentile (`SR0`), and does the observed Sharpe clear it by enough to be distinguishable from luck.

## What Killed It

**DSR = 0.0146** (current, correct value — computed against the experiments table as regenerated 2026-07-09–07-10 against a later `end_date`; an earlier reference value of **DSR = 0.042** reflects the original 2026-07-07 sweep and is the superseded number, not the other way around — confirmed to 9 decimal places against the authoritative computation, `docs/backtester_mcp_audit.md`). At n_trials = 3,600, `SR0 = 1.817` — the expected maximum Sharpe that many pure-noise trials would produce by chance — and the observed net Sharpe of 1.284 doesn't even clear that bar, before DSR is applied at all. Even under the more forgiving assumption of n_trials = 200 (competing only against its own QQQ-only variants, ignoring that it was actually selected from the full 18-ticker sweep), DSR is still only 0.170.

The signal had looked FDR-significant in isolation (p = 0.0015) — that's the exact failure mode DSR exists to catch: FDR controls the expected rate of false discoveries across a batch, but it does not correct for the winner's-curse bias of reporting only the single best result out of thousands of trials.

## Why This Matters

A signal can pass a standard significance test and still be pure noise if it was selected as the best of a large search — the sweep size, not just the single test, has to enter the statistics. This also stress-tested the DSR pipeline itself: a general-purpose backtesting library (`backtester-mcp`) was checked against the project's own custom DSR implementation and diverges structurally, because it hardcodes the null-hypothesis special case (mean=0, std=1) instead of the trial population's actual empirical mean/std — a reminder that off-the-shelf statistical tooling can silently fail to reproduce a project-specific gate even when it looks like a drop-in replacement.
