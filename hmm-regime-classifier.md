# HMM/Ensemble Regime Classifier
**Status:** Rejected — incoherent by construction, and produces zero forecasting signal once evaluated honestly out of sample
**Tested:** July 2026, `vol-forecaster` repo (`docs/step2_regime_engine_audit.md`, commit `d88535e`)

## The Hypothesis

A four-model ensemble (Markov-switching, Gaussian HMM, KMeans, GMM) votes on which of five market regimes ("Bull Quiet," "Crisis / Stress," etc.) is currently active, and that regime label predicts forward volatility better than a simple trailing average.

## The Test

Three independent audits, run before any forecasting comparison: (1) a coherence check — permute each model's own arbitrary state-label encoding (a pure renaming; the same days stay grouped together) and confirm the ensemble's output is unaffected; (2) a look-ahead check — truncate the input data at several points in history, refit everything, and confirm the regime label assigned to a given day doesn't change depending on whether future data was available; (3) a circularity check — confirm the regime label predicts something beyond what its own contemporaneous input features already show. Then, once coherence and look-ahead issues were identified, an honest out-of-sample forecasting test: fit once on the first 60% of history, apply the fitted models (no refitting) to the remaining 40%, and score against realized forward 20-day volatility.

## What Killed It

**Incoherent by construction.** The ensemble combines models by averaging their raw integer state codes and rounding (`ensemble += norm_w[k] * v`). Regime labels are arbitrary categorical codes with no inherent order. Permuting each model's own label encoding — a renaming that should leave the ensemble's output untouched — instead flipped **53% of all days into a different ensemble group**, with 2 of 3 original groups splitting across multiple new groups. No principled fix exists without redesigning the ensembling method entirely.

**Look-ahead in the regime labels themselves, not just the downstream forecaster.** Tested at 4 truncation points (30%, 45%, 60%, 75% through the sample): raw per-model states differed from the full-history fit at 3 of 4 cutoffs, and at the 75% cutoff the *final human-readable label* for the identical calendar day came out differently ("Crisis / Stress" vs. "Bull Volatile") purely depending on whether future data was in the fit.

**Regime membership explains less forward-vol variance than one of its own input features does alone.** Regimes are statistically distinct (ANOVA F=232.5, p=3.7×10⁻⁹⁶) but explain only ~14.4% of forward 20-day realized vol variance — while `real_vol_20`, one of the four features that define the regimes in the first place, correlates 0.51 with forward vol on its own (R² ≈ 26%). Discretizing into a 5-category label destroyed information rather than adding it.

**Once evaluated out-of-sample without look-ahead, the forecast collapses to a constant.** All 1,583 out-of-sample trading days (2020-03-26 to 2026-07-15) were classified into a single regime. Correlation with realized forward vol: ~0. Rank correlation and R²: undefined (no variance in a constant series). Its RMSE/MAE looked numerically fine — a known artifact of a constant forecast sitting near the sample mean, not evidence of skill.

## Why This Matters

This isn't "the naive benchmark won by a little" — the more sophisticated tool produced literally no discriminative information once tested without look-ahead, on top of an ensembling step that was mathematically incoherent before any forecasting question was even asked. It rules out this specific four-model architecture as a volatility forecaster, and it demonstrates concretely why "the model looks smart in a full-sample demo" and "the model forecasts anything out of sample" are different claims that have to be checked separately.
