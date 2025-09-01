# MAHAR KING BOT

Signal‑driven MT5 grid trading bot with a Tkinter dashboard and Telegram control. It implements a two‑leg strategy (King/Queen), batch order placement, runtime configuration, and an auto‑update system powered by GitHub releases.

> Important: Trading involves substantial risk. Use a demo account first. You are solely responsible for any financial outcomes.


## Features

- King/Queen grid strategy with batch pending orders
- Separate profit targets for King and Queen legs
- Safety cap on running King positions (`MAX_POSITIONS`)
- Telegram integration for BUY/SELL signals
- Tkinter dashboard: start/stop, adopt existing grid, wait for next signal, manual batches
- Live status: MT5 connection, balance/equity, P/L, open positions/orders
- Runtime configuration + save to `.env`
- Profiles (save/load), theme (light/dark)
- Auto‑update from GitHub releases (preserves `.env`, prefs, profiles, logs)


## Requirements

- Windows (tested) with MetaTrader 5 terminal installed
- Python 3.10+ (3.11 recommended)
- MT5 terminal must be logged in or credentials provided via `.env`

Python packages (see `requirements.txt`):

```
MetaTrader5
python-dotenv
python-telegram-bot>=20,<21
```

Install:

```
python -m pip install -r requirements.txt
```

Optional helper scripts:
- `install_requirements.bat` – install dependencies
- `run_dashboard.bat` or `START BOT.vbs` – launch the dashboard (no console)


## Quick Start

1) Clone or download this repository.
2) Install requirements (see above).
3) Create a `.env` file in the project folder (see Configuration).
4) Launch the dashboard:
   - Double‑click `START BOT.vbs` (no console), or
   - Double‑click `run_dashboard.bat`, or
   - Run: `python bot_dashboard.py`
5) In the dashboard:
   - Use Account Settings to set MT5/Telegram info and click Apply (or save to `.env`).
   - Use Configuration to adjust strategy values and click Apply.
   - Press “Start Bot” to begin; Telegram thread starts automatically if token is set.


## Telegram Setup

- Create a bot via @BotFather and copy the API token into `.env` as `TELEGRAM_TOKEN`.
- Decide the chat/channel you will use to send signals (BUY/SELL). Put its ID into `.env` as `TELEGRAM_CHANNEL_ID`.
- Not sure of the chat ID? DM the bot with `/getid` after the bot is running; the bot replies with the chat ID.
- The bot only processes plain text `BUY` or `SELL` messages from the configured chat.


## Configuration (.env)

Most settings can be changed at runtime in the dashboard and saved to `.env` for persistence. Example `.env`:

```
# MT5 login (optional if your terminal is already logged in)
MT5_LOGIN=1234567
MT5_PASSWORD=your_password
MT5_SERVER=YourBroker-Server

# Telegram
TELEGRAM_TOKEN=123456789:ABCDEF_your_bot_token
TELEGRAM_CHANNEL_ID=-1001234567890

# Trading config
SYMBOL=XAUUSDm
LOT_SIZE=0.01
MAX_POSITIONS=25
GRID_STEP_PIPS=1000       # points, not pips
PROFIT_PIPS=4000          # King profit target (points)
QUEEN_PROFIT_PIPS=3000    # Queen profit target (points)
QUEEN_POSITIONS_LIMIT=20
KING_POSITIONS_LIMIT=30
HEDGE_AFTER_POSITIONS=15
OPEN_DELAY=0.5
LOOP_MS=1500
SLIPPAGE=30
LOG_FILE=grid_bot.log
REPLACE_CLOSED_POSITIONS=Yes
# Optional manual magics (default set from main magics + 1)
KING_MAGIC_NUMBER=654321
QUEEN_MAGIC_NUMBER=123456
KING_MANUAL_MAGIC_NUMBER=654322
QUEEN_MANUAL_MAGIC_NUMBER=123457
```

Notes:
- Values called “PIPS” are expressed in MT5 points (respect instrument `digits`).
- If MT5 credentials are omitted, the bot will try to attach to an already‑logged‑in terminal instance.


## Strategy Overview

- On a new BUY/SELL signal (“King” direction), the bot places a batch of pending orders around price (`KING_POSITIONS_LIMIT`).
- When running King positions reach `HEDGE_AFTER_POSITIONS`, it creates a “Queen” batch of opposing stop orders (`QUEEN_POSITIONS_LIMIT`).
- King has a safety cap on running positions (`MAX_POSITIONS`). Pending orders do not count toward the cap.
- Each leg has its own profit target (`PROFIT_PIPS` for King, `QUEEN_PROFIT_PIPS` for Queen).
- Optional: reopen closed King positions as new pending limits (`REPLACE_CLOSED_POSITIONS=Yes`).

Manual controls in the dashboard:
- Detect & Continue: adopt existing King direction from current positions
- Wait For Signal: arm the bot and wait for next Telegram/GUI signal
- Manual Start Grid: immediately start a King grid in selected direction
- Manual Batches: start King or Queen batches with specific counts
- Cancel Pending Orders: All/King‑only/Queen‑only; Close All Positions


## Auto‑Update (GitHub Releases)

The dashboard can auto‑check GitHub for a new release of the bot and update itself.

- Default repo is set to: `maharshoaib786/MAHAR-KING-BOT`
- Settings → Updates:
  - Set/confirm the repo
  - Toggle “Auto‑update at launch”
  - “Check for Updates” to trigger manually
- On update, the app downloads the zip, extracts, replaces files, and relaunches.
- It preserves: `.env`, `dashboard_prefs.json`, `profiles.json`, `grid_bot.log`.

Publishing a release:
1) Bump the version string in `bot_dashboard.py` (e.g., `self.app_version = "1.01"`).
2) Create a Git tag like `v1.01` and a GitHub release for that tag.
3) Attach a zip of the full project (or rely on the tag source zip).
4) Clients will auto‑update at next check/launch.


## Files

- `Bot.py` — Core trading logic, MT5/Telegram integration
- `bot_dashboard.py` — Tkinter dashboard UI and updater
- `auto_updater.py` — GitHub release checker and utilities
- `update_helper.py` — Helper process to copy files and relaunch
- `requirements.txt` — Python dependencies
- `run_dashboard.bat` / `START BOT.vbs` — Windows launchers


## Troubleshooting

- MT5 initialize failure: ensure the terminal is installed and running, and credentials or a logged‑in session exist.
- Symbol not found: make sure `SYMBOL` is visible/ tradable in your terminal and enabled.
- Telegram not receiving messages: verify `TELEGRAM_TOKEN` and `TELEGRAM_CHANNEL_ID`, and that messages are plain `BUY`/`SELL` text.
- Update failed: check network connectivity and that GitHub has a published release with a higher tag version.
- Permission issues on update: close other running copies before updating so files aren’t locked.


## Contributing

Pull requests and issues are welcome. Please discuss major changes in an issue first.


## Disclaimer

This software is provided “as is” without warranties. Trading is risky; use at your own risk and test thoroughly on a demo account.

