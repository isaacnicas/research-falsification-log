# PEAD Long-Short (Post-Earnings-Announcement Drift)
**Status:** Combination rejected by DSR; the long leg's own headline number is a separate, unresolved question
**Tested:** Q4 2023 pilot (`quant_lab`, `data/store.duckdb` experiment `PEAD_PILOT_Q4_2023`, 2026-07-10); long-leg reproducibility check 2026-07-15 (`docs/pead_longleg_bootstrap_ci.md`, commit `b52f980`)

## The Hypothesis

Stocks with the largest positive earnings surprises (seasonal-random-walk SUE) keep drifting up after the announcement, and the largest negative surprises keep drifting down — combining both as a long-short book should produce one combined, dollar-neutral drift-continuation return.

## What Was Killed

The Q4 2023 pilot ran 481 events (20-day hold, 10% stop-loss, 5bp costs). Combined long-short Sharpe: **−0.624**. Winsorized Sharpe: **−0.654**. Deflated Sharpe Ratio: **0.00077** (`dsr_significant = False`) — not a small positive number, effectively zero once selection bias across the declared trial count (`n_trials_declared = 12`, tracked through 4 pre-registration amendments) is priced in. The long leg and short leg individually reported wildly asymmetric Sharpes (+6.49 long, −6.64 short) that don't net to anything close to zero because of how they were weighted, but the combined, DSR-corrected figure is unambiguous: **this is not a real combined edge.**

One data-quality issue is documented rather than papered over: `compute_forward_returns`'s stop-loss only triggers on the underlying price *falling*, which is the wrong direction for the short leg (an adverse move for a short is the price *rising*). Several short positions ran +53% to +74% against the position with `stopped=False`. This was not corrected or rerun for this result — it's flagged as a caveat, and it means the short leg's own −6.64 Sharpe is itself unreliable in a specific, understood way. It does not change the combination verdict: DSR was already indistinguishable from zero before this issue is even considered.

**Verdict: killed.** The combined long-short strategy does not clear DSR and does not go live.

## What's Still Open

The long leg's own **+6.49** Sharpe — the number that made the combination look asymmetric in the first place — was separately tested for basic reproducibility, independent of the DSR verdict above. A continuous daily return series was reconstructed from the pilot's own logged parameters and the platform's actual, unmodified entry/hold/exit code (`features/engineer_pead.py`), validated event-by-event (the reconstructed per-event returns compound exactly to the platform's own reported `gross_return` for every one of 228 events, zero mismatches) before being blended into a single daily portfolio series.

That reconstructed series' Sharpe is **−1.40 to −1.41** — not close to +6.49, and not even the same sign. Two alternate ways the +6.49 might have been computed were checked and ruled out (per-event Sharpe annualized by trade frequency: 1.57; treating each event as one daily observation: 7.01) — neither matches either. The original pilot script that would show how +6.49 was actually calculated does not survive anywhere (never version-controlled, not in git history, not referenced in any log).

**This is not settled, and it is not the same claim as the DSR kill above.** The combination is dead regardless of which long-leg number is correct — DSR already rules it out. But whether the long leg's own headline Sharpe was ever a real, reproducible number is a distinct, open question that a from-scratch, newly pre-registered re-derivation would be needed to resolve. Treat +6.49 as unverified until that happens.

## Why This Matters

Two different levels of scrutiny can be in flight on the same result at once, and conflating them is a mistake worth naming explicitly: "this strategy failed a significance test" (settled, DSR ≈ 0) and "this specific number in the write-up can't currently be reproduced" (open, a data/methodology gap) are different findings that call for different next steps. Reporting them as one tidy resolved story would have been less accurate than reporting both honestly.
