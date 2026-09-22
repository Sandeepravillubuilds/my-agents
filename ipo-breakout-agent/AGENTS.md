# IPO Base Breakout Agent — India

## Mission

Build a reproducible research and screening system for NSE/BSE equities that identifies post-IPO base structures and their breakout lifecycle. It is a decision-support tool, never a prediction or execution system. All numeric claims must be traceable to deterministic calculations and an identified data source.

## Architecture and responsibilities

The package is organized as small typed modules. The daily orchestrator calls these logical agents in order: Universe (eligible IPOs), Data (provider and validation), Pattern (base features), Pivot (resistance zone), Breakout (lifecycle), RS and Regime, Scoring, Risk/Quality, Watchlist, Report/Chart, and Backtest. Agents exchange dataclasses/dataframes; generated prose only summarizes computed values.

Providers are interchangeable interfaces: `MarketDataProvider`, `SecurityMasterProvider`, `IPODataProvider`, and `FundamentalDataProvider`. The MVP `CsvMarketDataProvider` reads user-supplied, cached CSV files. Do not add scraping or synthesize live values. Official, licensed, or explicitly permitted APIs belong behind a provider adapter.

## Universe and detection rules

The default universe is active NSE/BSE common equities listed 0–8 years ago, with configurable exchange, listing-age, price, market-cap, liquidity, and sector filters. A security record carries symbol, company name, exchange, ISIN, listing date, IPO price, sector, industry, market cap, free float, lot size, and status where available.

IPO-base detection is feature-based: post-listing history, initial high, base duration/depth, ATR contraction, range contraction, volume contraction, higher-low tendency, tightness, and distance to a resistance pivot. It must not classify a stock simply because it trades below a high. Detectors return the complete feature set, levels, confidence, and calculation-derived explanation.

The pivot engine returns a zone (`pivot_zone_low`, `pivot_zone_high`) and a representative pivot, with an explanation based on repeated/base resistance. Breakout states are `BASE_FORMING`, `NEAR_PIVOT`, `BREAKOUT_TODAY`, `CONFIRMED_BREAKOUT`, `RETEST`, `EXTENDED`, and `FAILED_BREAKOUT`. A close above a pivot is not confirmed without configured volume and closing-strength evidence.

## Configuration and scoring

All tunable thresholds and score weights live in `config/default.yaml`; no strategy thresholds are embedded across the codebase. The score is 0–100 with configurable pillars: base quality (25), price structure (15), volume (15), breakout confirmation (15), relative strength (15), fundamentals (10), and market/sector context (5). Every score must expose its components and classification (`A+`, `A`, `B`, `WATCHLIST`, or `REJECT`). Fundamentals remain a separate optional layer and include value, source, and as-of date.

## Data quality, anti-lookahead, and backtesting

Reject invalid inputs before signal generation: duplicate dates, missing required OHLCV fields, nonpositive prices, invalid OHLC ordering, zero/negative volume, stale series, and obvious split/bonus discontinuities. Mark affected candidates `DATA_QUALITY_REJECT`; never repair silently.

Backtests must slice each security history at the detection date and call the same detector used in production. Future prices are only accessed after the signal is fixed to calculate labelled forward outcomes (1/5/10/20/40/60 sessions, MFE and MAE). Parameter selection and reporting must use separated training, validation, and out-of-sample periods, preferably walk-forward. Never tune and claim validation results on the same full sample.

## Coding, testing, and operations

Use Python 3.11+, type hints, dataclasses, small pure calculation functions, ISO dates, INR-aware labels, and structured logging. Do not use an LLM for calculations. Handle insufficient history explicitly; newer IPOs must not be penalized solely for missing long moving averages.

Tests cover base depth, pivots, breakouts, volume ratios, RS, insufficient history, corporate-action anomalies, missing sessions, score boundaries, watchlist transitions, and lookahead prevention. Keep synthetic fixtures separate from real data. Run `pytest` after meaningful changes.

Store secrets only in environment variables; commit `.env.example`, never `.env`. Parameterize SQL and validate CSV schemas. Log provider, as-of date, scan ID, row counts, validation rejections, and recoverable errors without credentials or PII.

## Storage, outputs, and definition of done

SQLite stores securities, IPO metadata, prices, bases, pivots, signals, scores, watchlist states, scan runs, and backtest results. Parquet may be added for larger history. A signal includes identifiers, as-of date, pattern/status, pivot zone, base metrics, volume/RS metrics, score breakdown, levels, explanation, and quality flags.

MVP is done only when a user can configure a CSV/provider, initialize the schema, run `python -m ipo_agent daily`, obtain ranked results and per-symbol chart HTML, open the Streamlit dashboard, observe persistent watchlist transitions, run an anti-lookahead backtest, and pass automated tests. Before future changes, read this file and preserve these controls.
