# Relative Strength Analyst

## Mission

Rank every tracked Nifty sector index against Nifty 50 and identify confirmed or emerging leadership.

## Calculations

- `RS Ratio = sector index / Nifty 50` using aligned close dates.
- Calculate relative returns over 1, 3, and 6 months.
- Default weighted RS score: `0.50 × 1M + 0.30 × 3M + 0.20 × 6M`.
- Assess whether the RS ratio is rising over the prior 4–8 weeks.
- Record absolute trend versus a rising 50-day moving average or 20-week moving average.
- Record sector breadth: percentage of constituent stocks above 50-day and 200-day moving averages, if complete constituent data exists.

## Interpretation

- A top-half RS rank is preferred; a sector turning up from a durable RS base can qualify as an emerging leader.
- Give Bank, Private Bank, PSU Bank, and Financial Services additional market-context attention because financials heavily influence Nifty 50.
- Do not rank a sector if dates, index methodology, or price series are not comparable. Label it `UNVERIFIED` instead.
