# 📈 Real-Time Stock Scanner

A real-time momentum stock scanner built with React that screens equities against a 5-pillar model to surface low-float names showing signs of explosive demand.

**[Live Demo](https://real-time-stock-scanner.vercel.app)**

---

## The 5 Pillars

Each ticker is scored **0–5** based on how many of the following it currently clears, shown as dots in the Signal column. A 5/5 score is flagged as an explosive setup.

| Pillar | What it measures |
|---|---|
| **Relative Volume (RVol)** | Today's volume vs. a baseline |
| **Total Volume** | Raw shares traded |
| **Gap / % Gain** | Move from prior close |
| **Price Range** | $2–$20 |
| **Float** | Shares available to trade — lower float means more price-sensitivity to volume |

## Data Modes

- **Simulated** *(default)* — generates fake tickers for testing the UI. No API key needed.
- **Live** — requires a paid Polygon/Massive plan (the free tier doesn't include the snapshot endpoint). Polls every 15 seconds.
- **EOD Fallback** — kicks in automatically if a Polygon key hits a 403 on the live endpoint. Compares the two most recent completed trading sessions instead, refreshed every 5 minutes.

## Tech Stack

- **React** (functional components and hooks), built with **Vite**
- **[Polygon.io](https://polygon.io)** (now Massive) market snapshot REST API for live data
- **[Finnhub](https://finnhub.io)** company profile API for float data
- **[lucide-react](https://lucide.dev)** for icons
- No backend — runs entirely client-side, polling REST endpoints on an interval rather than using a websocket

## Quick Start (no install needed)

Don't want to touch code? Just use the hosted version:

1. Go to **[real-time-stock-scanner.vercel.app](https://real-time-stock-scanner.vercel.app)**
2. The scanner opens in **Simulated mode** — it works right away with fake tickers, no sign-up or API key required
3. Want real market data? Click **Criteria** in the app and paste in a free [Polygon.io](https://polygon.io) API key (see [Data Modes](#data-modes) above for what each key unlocks)

That's it — nothing to install.

## Setup (for developers)

```bash
npm install
npm run dev
```

Open the app, click **Criteria**, and paste in your API keys — they're kept in-session only and never persisted or committed.

- **Polygon.io key** — required for live or EOD data
- **Finnhub key** — optional, enables the float column

## Known Limitations

- RVol is computed as today's volume divided by prior-day volume — a proxy rather than a true 20-day average
- Float comes from Finnhub's shares-outstanding figure, not true tradeable float (excludes insider and locked shares)
- This is a screener only — no order execution, alerts, or backtesting
- Not financial advice, and has not been validated against live trading performance
