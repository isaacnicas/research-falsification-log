# Research Falsification Log

Every strategy here was pre-registered, tested against out-of-sample data and multiple-testing controls, and rejected on statistical grounds when it failed. This is the negative-results record — the strategies that are actually live are documented in [quant-portfolio](https://github.com/isaacnicas/quant-portfolio). Being wrong quickly and honestly is the whole point of this process.

| Strategy | Rejected Because | Full Write-up |
|---|---|---|
| HMM Regime Classifier | Incoherent ensemble + look-ahead + circularity | [hmm-regime-classifier.md](hmm-regime-classifier.md) |
| Live Regime Engine (v1/v2) | Failed Gate 2 (no predictive power) | [live-regime-engine-v1-v2.md](live-regime-engine-v1-v2.md) |
| QQQ RSI Oversold-Bounce | DSR far below threshold | [qqq-rsi-oversold-bounce.md](qqq-rsi-oversold-bounce.md) |
| PFRC Long-Short | Sharpe −0.037 on falsification test | [pfrc-long-short.md](pfrc-long-short.md) |
| PEAD Long-Short | Combination killed; long-leg reproducibility separately open | [pead-long-short.md](pead-long-short.md) |

Every figure in every write-up is cited against a specific committed file, database table, or commit hash — not recalled from memory or summary.
