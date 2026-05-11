# Longevity Multi-Agent Pipeline

Monitors the UkhvatNews Telegram channel, parses longevity science articles with AI, and automatically creates structured tasks in ClickUp for your developer and sales teams.

## Quick start

### 1. Install dependencies
```bash
pip install -r requirements.txt
```

### 2. Your .env is already configured
All your credentials are pre-filled in `.env`. Nothing to change unless you rotate keys.

### 3. Test all connections
```bash
python orchestrator.py --test
```
You should see ✅ for Groq, ClickUp, and Telegram bot.

### 4. Run once (process latest posts right now)
```bash
python orchestrator.py --once
```

### 5. Run as a daemon (keeps running, polls every 5 minutes)
```bash
python orchestrator.py
```

You'll get a Telegram message from your bot each time it processes new posts.

---

## What it does

```
@UkhvatNews channel
        ↓
  Telegram monitor  ←── scrapes public channel (no admin needed)
        ↓                also listens via bot if bot is channel admin
  Article parser    ←── Groq/LLaMA reads article, extracts structure
        ↓
  ┌─────┴──────────────┐
  │                    │
Dev task agent    Sales/investor agent
  │                    │
  └──────┬─────────────┘
         ↓
   ClickUp tasks  ──→  Developers list / Sales list / Other list
         +
   Investor letters  ──→  saved to outputs/letters/
         +
   Telegram status  ──→  sent to your user ID after each run
```

## Output

**ClickUp tasks** are created in three lists:
- `901818019610` → Developers (research tasks, technical investigations)
- `901818019614` → Sales/Investors (outreach tasks, grant applications)
- `901818019898` → Other (PR, partnerships, legal)

**Investor letters** are saved as `.txt` files in `outputs/letters/`

**Logs** are in `logs/pipeline.log` and `logs/errors.log`

## Telegram bot setup (optional enhancement)

Currently the bot scrapes the public @UkhvatNews channel preview.
To also receive posts in real-time via the bot:

1. Open Telegram, find your bot
2. Add it as an admin to the channel you want to monitor
3. The bot will then also receive updates via `getUpdates`

The bot will always send you status messages after each pipeline run.

## Adjusting behaviour

| What to change | Where |
|---|---|
| Poll interval | `POLL_INTERVAL` in `.env` (seconds, default 300) |
| How far back to look on first run | `LOOKBACK_HOURS` in `.env` |
| AI model | `GROQ_MODEL` in `.env` |
| Agent prompts | `SYSTEM_PROMPT` constant in each `agents/*.py` file |
| Max letters per article | Change `[:3]` in `sales_investor_agent.py` |
