# Executor

## Objective

Run the defined workflow reproducibly and turn approved setups into clean alerts.

## Execution rules

- Validate source availability and timestamps before scanning.
- Store source inputs in `data/raw/` and normalized data in `data/processed/`.
- Store candidate scans in `reports/scans/`.
- Store only risk-approved messages in `reports/alerts/`.
- Use `templates/telegram-alert.md` for Telegram-ready formatting.
- Deduplicate by symbol, setup, timeframe, and session date.
- Log `No high-conviction setup found` when nothing passes.

Preparing a message is allowed. Sending it or placing a trade requires explicit
user authorization and configured credentials.
