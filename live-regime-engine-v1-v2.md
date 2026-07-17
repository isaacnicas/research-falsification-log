# Live-System Regime Engine (v1 and v2)
**Status:** Rejected — both versions failed the predictive-power gate on an honest, pre-committed protocol
**Tested:** July 4, 2026 (`C:\QuantTrading\regime\` and `C:\QuantTrading\regime_predictive\`)

## The Hypothesis

**v1:** a point-in-time-corrected version of the same four-model regime ensemble (Markov/HMM/KMeans/GMM), expanding-window refit every 63 trading days, would forecast forward market outcomes above a naive baseline once look-ahead was fixed.

**v2 ("Path B"):** if v1's contemporaneous features (realized vol, returns) were the problem — reacting to stress rather than anticipating it — leading indicators (credit spreads, rate curve, cross-asset rotation, vol term structure) would forecast regime shifts *before* they showed up in realized volatility.

## The Test

A three-gate protocol, criteria pre-committed before results were seen: **Gate 1** (point-in-time causality — does each day's label use only data available through that day), **Gate 2** (predictive power — does the classifier beat a majority-class null and a naive-persistence null against a price-derived, independent 21-day-forward outcome, evaluated on an expanding-window walk-forward with a 63-day refit cadence and an embargo equal to the forecast horizon), **Gate 3** (definitional stability — does what each label *means* stay consistent across refits). Gate 3 was pre-committed to be skipped if Gate 2 failed, since checking the stability of a non-predictive signal isn't informative.

## What Killed It

**v1 passed Gate 1, failed Gate 2.** Fully causal by construction (`REGIME_PIT_NOTES.md` documents four specific look-ahead fixes: expanding-window feature scaling, expanding-window model refits, in-window-only regime naming, and a dwell-time filter replacing the original's incoherent integer-EMA smoothing). But on the clean evaluation window (2,694 days, 2015-10-16 onward): 21-day predictive accuracy **0.369 vs. a 0.523 majority-class null** (delta −0.154). High-confidence (>70%) "Crisis / Stress" calls (n=181) had an empirical realized-stress rate of **2.2%** — the model was confidently wrong. Gate 3 came back mixed (moderate definitional drift, mean coefficient of variation 1.073 for the Crisis/Stress label vs. 0.312 for Bull Quiet), but this was academic once Gate 2 had already failed.

**v2 tested the specific hypothesis that leading indicators would fix this, and they didn't.** Built alongside v1 (not replacing it), using 20 features across four families (credit-ETF ratios, rate curve/MOVE, cross-asset rotation, VIX term structure) — FRED's actual credit-spread series were unobtainable (no API key, direct HTTPS blocked) and documented as such rather than faked; ETF-ratio proxies were used instead. Same walk-forward protocol, 3,499 labeled days, 57 refits, 33 realized stress episodes: 21-day accuracy **0.396 vs. majority null 0.544** (delta −0.148) and **vs. persistence null 0.487** (delta −0.091). High-confidence stress calibration improved over v1 but was still wrong (n=276, empirical rate 19.2% against a >70% claim). Transition recall (catching a real stress episode before or at onset) was 0.394 (13 of 33); transition precision was 0.190 (4 of 21 flagged runs actually preceded something). All four 2018 episodes and the COVID onset were missed entirely. No individual feature family beat either null; credit was "least bad" with an edge over the persistence null of −0.022 — indistinguishable from none.

## Why This Matters

Two independently-built engines — one reactive, one explicitly designed to lead — failed the same predictive-power gate on the same honest protocol. That's stronger evidence than either result alone: it rules out "wrong feature set" as the explanation and points at something structural about trying to time regime shifts with this class of model on this data. Regime-timing was not this system's edge on two separate attempts; both were shelved for observational research only, with nothing wired into governance or live sizing.
