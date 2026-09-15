# 📈 Real-Time Stock Scanner

A momentum stock scanner built with React that screens for low-float, high relative-volume breakouts via the Polygon.io API. I built this to practice working with real-time market data and REST polling in a client-only app.

<img width="1122" height="1079" alt="Image" src="https://github.com/user-attachments/assets/0e6b14df-9b98-4ceb-ba26-7de24f66f115" />

## 🛠️ Technologies

- React
- Vite
- JavaScript
- Polygon.io API
- Finnhub API
- CSS
- lucide-react

## 🥷 Features

Here's what you can do with the Stock Scanner:

- **Simulated Mode**: Try the app instantly with generated fake tickers, no API key needed.
- **Live Mode**: Connect a Polygon.io key to poll real market snapshots every 15 seconds.
- **EOD Fallback**: If your API tier can't reach the live endpoint, the app automatically falls back to comparing the two most recent completed trading sessions.
- **5-Pillar Signal Scoring**: Each ticker is scored 0–5 across relative volume, total volume, gap %, price range, and float, shown as dots in the Signal column.
- **Float Lookup**: Optionally add a Finnhub key to pull in float data and spot low-float names.
- **Custom Criteria**: Adjust the thresholds for each pillar to match your own screening style.

## 🐢 The Process

I started by building the polling layer that pulls live snapshot data from the Polygon.io API on a set interval, since the app runs entirely client-side with no backend. From there, I designed the 5-pillar scoring model (relative volume, total volume, gap %, price range, and float) so that each ticker could be scored 0 to 5 based on how many pillars it cleared, with the strongest setups easy to spot at a glance.

Since the free Polygon tier doesn't include the live snapshot endpoint, I added an EOD fallback that automatically kicks in if a live request gets rejected, comparing the two most recent completed sessions instead so the app still returns useful data. I also built a Simulated mode with generated fake tickers so the app is usable and testable without requiring an API key at all. Finally, I wired in an optional Finnhub key to pull in float data, and built out a Criteria panel so users can adjust the thresholds for each pillar to match their own screening style.

## 📚 What I Learned

Building this project pushed me to think more carefully about working with real-time data on the frontend. Since there's no backend, I had to handle everything, from polling intervals to rate limits to graceful fallbacks, directly in React state, which taught me a lot about managing async data flows and avoiding race conditions between polling cycles.

I also learned a lot about designing a scoring system from multiple weighted inputs, and making sure the logic stayed clear and easy to adjust as I added more criteria. Handling the live-to-EOD fallback in particular forced me to think about failure states early rather than assuming the "happy path" API response would always be available, which changed how I approach API integrations going forward.

## 🏃 Running the Project

To run the project in your local environment, follow these steps:

1. Clone the repository to your local machine.
2. Run `npm install` in the project directory to install the required dependencies.
3. Run `npm run dev` to get the project started.
4. Open the address shown in your console (typically `http://localhost:5173`) in your browser to view the app.
5. Click **Criteria** in the app to add your Polygon.io and/or Finnhub API keys, or leave it in Simulated mode to try it out right away.

## 🎥 Video

https://github.com/user-attachments/assets/78359109-a53d-4cb9-a1d1-47fd19ee6d99
