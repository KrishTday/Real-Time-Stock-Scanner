# 🎓 Laurier Campus Events App

A full-stack events tracker for Wilfrid Laurier students. Surfaces campus events and RSVP deadlines in one place instead of scattered club emails. Students browse events by category or search, see how far off an RSVP deadline is at a glance, and track the ones they care about, with no account required.

<img width="1902" height="1079" alt="Image" src="https://github.com/user-attachments/assets/ad3a6d4d-0fe6-4976-8c8a-70f4d294b3d3" />

https://github.com/user-attachments/assets/360ba211-67a5-46cd-aade-742aa5d86c61

## 🛠️ Technologies

- Java (JDK's built-in `HttpServer`, no framework)
- SQLite via JDBC
- TypeScript (compiled directly to browser JS, no bundler)
- CSS
- HTML
- Docker
- Render (hosting)
- Voyage AI (embeddings) + Claude API (generation) for the RAG chat feature

## 🎉 Features

Here's what you can do with the Laurier Campus Events App:

- **Browse and Search**: Filter events by category or search across title, description, and location.
- **RSVP Countdowns**: See how much time is left before an RSVP deadline, with a red highlight once it's within 48 hours.
- **Track Events**: Save the events you care about to a "my tracked events" view, persisted per browser with no login needed.
- **Organizer Mode**: Add or remove events through the same REST API the UI uses, standing in for a real event-management workflow.
- **Ask About Events**: A chat widget backed by a real RAG pipeline. Event text is embedded with Voyage AI and cached in SQLite, a student's question is embedded and ranked against those vectors by cosine similarity, and the top matches are handed to Claude as grounding context so answers stay tied to real events instead of the model guessing. Matched events show up as clickable chips that jump straight to them in the main list.

## 🐢 The Process

I built the backend in plain Java using the JDK's built-in `HttpServer` instead of a framework, so the whole backend only needed the JDK plus a single SQLite JDBC driver jar. I wrote a small hand-rolled JSON reader and writer to keep dependencies minimal, then set up SQLite as the database so the app had no external database service to manage.

On the frontend, I used TypeScript compiled straight to browser JavaScript with no bundler and no client-side framework, updating the DOM directly from `main.ts`. I built the category filters and search first, then layered in the RSVP countdown logic and the red 48-hour warning state. Once the core browsing experience worked, I added per-browser event tracking using a random id stored in `localStorage`, and built out an organizer mode that hits the same REST API as the rest of the app. For deployment, I containerized the whole app with Docker and deployed it to Render.

## 📚 What I Learned

This project taught me a lot about building a backend without leaning on a framework. Working directly with Java's `HttpServer` and hand-rolling JSON parsing gave me a much clearer picture of what frameworks normally abstract away, from routing requests to serializing responses.

I also learned how far you can get on the frontend without a bundler or framework, and where that approach starts to show its limits. Building tracking without user accounts pushed me to think through trade-offs around using `localStorage` as a lightweight identity, and containerizing and deploying the app to Render taught me more about production environments and config than working locally ever did.

## 🏃 Running the Project

To run the project in your local environment, follow these steps:

1. Clone the repository to your local machine.
2. Build the frontend:
   ```
   cd frontend && npm install && npm run build
   ```
3. Build and run the backend:
   ```
   cd ../backend
   javac -cp libs/sqlite-jdbc-3.53.4.0.jar -d out $(find src -name "*.java")
   java -cp "out:libs/sqlite-jdbc-3.53.4.0.jar" com.laurier.events.Main
   ```
4. Open `http://localhost:8080` in your browser to view the app. The database seeds itself with sample Laurier events on first run.

## 🤖 Chat feature setup

The "Ask about events" widget needs two API keys set as environment variables on the backend. Without them, the rest of the app works normally and the chat widget returns a friendly "not configured" message instead of erroring.

1. Get a Voyage AI key at [voyageai.com](https://www.voyageai.com) (embeddings; free tier available).
2. Get an Anthropic key at [console.anthropic.com](https://console.anthropic.com) (Claude API; used to generate answers).
3. Set them before running locally:
   ```
   export VOYAGE_API_KEY=your_voyage_key
   export ANTHROPIC_API_KEY=your_anthropic_key
   ```
4. On Render, add the same two keys in the service's Environment tab (they're already declared as secrets in `render.yaml`, so Render will prompt for them on deploy).

Never commit these keys to the repo — they're read from the environment only.
