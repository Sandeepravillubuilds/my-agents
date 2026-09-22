# World-Class Swing Trading Agent

This agent produces a long-swing candidate only when every gate is evidenced by
completed-session data. It prioritises rejection over an incomplete signal.

## Hard gates, in order

1. **Trend and setup:** Daily and weekly uptrends must be intact. Allowed
   structures are an ATH/base breakout, volatility-contraction breakout, or a
   declining-volume pullback to a rising 10/20 EMA or 50 SMA with a bullish
   trigger. Sideways, choppy, and downtrending names are rejected.
2. **Volume:** The trigger must trade at least 1.5x its prior 20-session
   average. Pullback selling volume must contract before the trigger.
3. **Risk/reward:** First measured technical target must provide at least 2.5R.
   Stops use the setup's invalidation boundary, never a fixed percentage.
4. **Momentum and leadership:** Close must be above (or reclaiming) the 20 EMA,
   RSI must be 40–70, and the stock must outperform both Nifty and its mapped
   sector over 5 and 20 sessions.
5. **Execution/context:** 20-day average volume is at least 500,000 shares;
   a same-session bid/ask snapshot must show a spread within the configured
   maximum; market regime must permit longs; and a verified event calendar must
   show no binary event within the configured window.

## Input contracts

- `sector_benchmarks` in the scanner YAML maps every ticker to a liquid sector
  benchmark ticker. Missing mapping is a rejection, not a neutral score.
- `data/processed/liquidity_snapshots.csv` requires `symbol,as_of,bid,ask`.
- `data/processed/corporate_events.csv` requires
  `symbol,event_date,event_type`. An empty, valid calendar means no known event;
  a missing file means event risk is unverified and rejects the candidate.
- Use `--period max` (the scanner default) for the ATH detector. With a shorter
  cache/history window, its output is only an ATH relative to supplied history.

## Self-audit

Record each closed trade's `r_multiple` in the trade journal. After 30 closed
R-multiple records, the scanner blocks new candidates if mean expectancy is
below the configured threshold. The scan summary exposes the audit state.
