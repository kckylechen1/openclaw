---
name: quant-analyzer
description: Use the Quant_Analyzer_2026 project for stock analysis via its V8 engine. Call this skill when asked to analyze specific stocks, run market scans, or perform backtests using this project.
allowed-tools: Bash Exec Read Write
---

# Quant Analyzer 2026 Skill

Call this project to analyze stocks using its V8 engine (RSI/BB/MACD/缠论/波浪/量价/风险 seven dimensions).

## Absolute Rules

1. **Always use project's venv**: `./.venv/bin/python` — NEVER use system `python3`
2. **Always set CWD**: `~/Desktop/Quant_Analyzer_2026`
3. **Always snapshot first**: Never analyze a stock without running `snapshot`
4. **No fabricating**: Base all analysis strictly on code output.

## Project Structure

```
~/Desktop/Quant_Analyzer_2026/
├── .venv/bin/python          # Use this Python
├── scripts/cli.py            # Unified CLI entry
├── GEMINI.md                 # Analysis rules and protocols
├── auth.py                   # Credential loading (checks .env, ~/.secrets/master.env, system env)
├── engine/v8/                # V8 engine modules
├── watchlist.json            # Stock watchlist
└── shadow_fund_state.json    # Shadow fund positions
```

## Credential Loading Order

The project loads credentials in this order:

1. Project `.env`
2. `~/.secrets/master.env` (fallback)
3. System environment

If `check_env.py` reports missing credentials, check `auth.py` and `~/.secrets/master.env` — the check script may not account for the fallback path.

## Commands

When calling the `exec` tool, it is highly recommended to set `workdir: "/Users/kckylechen/Desktop/Quant_Analyzer_2026"` and run the commands directly without `cd`.

### Environment Check

```bash
cd ~/Desktop/Quant_Analyzer_2026 && ./.venv/bin/python scripts/cli.py check
```

Note: This may report missing credentials even when `~/.secrets/master.env` has them. Verify with `auth.py` logic.

### Single Stock Snapshot (REQUIRED before any analysis)

```bash
cd ~/Desktop/Quant_Analyzer_2026 && ./.venv/bin/python scripts/cli.py snapshot <SYMBOL>
```

Example symbols: `002384.SZ` (东山精密), `300502.SZ` (新易盛), `600519.SH` (茅台)

### Batch Scan

```bash
cd ~/Desktop/Quant_Analyzer_2026 && ./.venv/bin/python scripts/cli.py scan --group ai_compute_chain
cd ~/Desktop/Quant_Analyzer_2026 && ./.venv/bin/python scripts/cli.py scan --symbols 300502.SZ 002475.SZ
```

### Market Discovery Scan

```bash
cd ~/Desktop/Quant_Analyzer_2026 && ./.venv/bin/python scripts/cli.py discover --mode all --universe a_hot_100
cd ~/Desktop/Quant_Analyzer_2026 && ./.venv/bin/python scripts/cli.py discover --mode chan --universe watchlist
```

### Backtest

```bash
cd ~/Desktop/Quant_Analyzer_2026 && ./.venv/bin/python scripts/cli.py backtest --days 120
```

## Analysis Protocol

1. **Run snapshot** on the target stock(s) using the commands above.
2. **Base analysis ONLY on snapshot output** — never fabricate data or guess RSI/MACD.
3. **Label data sources**: `[V8快照 HH:MM]`
4. **If snapshot fails**, report the blocker — do not proceed with speculative analysis.

## Common Issues

| Issue               | Check                                                          |
| ------------------- | -------------------------------------------------------------- |
| Missing credentials | `~/.secrets/master.env` exists? Check `auth.py` fallback logic |
| PyYAML missing      | `./.venv/bin/pip install PyYAML`                               |
| Wrong Python used   | Always use `./.venv/bin/python`, never system `python3`        |
| QuoteContext fails  | Check LongPort credentials in `~/.secrets/master.env`          |

## Quick Smoke Test

```bash
cd ~/Desktop/Quant_Analyzer_2026 && ./.venv/bin/python -c "from auth import get_quote_ctx; ctx = get_quote_ctx(); print('QUOTE_CTX_OK')"
```

If this prints `QUOTE_CTX_OK`, the environment is ready.
