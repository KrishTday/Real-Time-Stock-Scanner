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

## 🤖 How the chat feature works

The "Ask about events" widget is powered by a small RAG (retrieval-augmented generation) pipeline running entirely on the backend:

1. **Embedding events**: Whenever event data changes, each event's text (title, description, location, etc.) is sent to Voyage AI to generate a vector embedding, which is cached in SQLite so it doesn't need to be recomputed on every request.
2. **Embedding the question**: When a student asks something in the chat widget, their question is embedded the same way using Voyage AI.
3. **Retrieval**: The question's embedding is compared against every cached event embedding using cosine similarity, and the closest-matching events are pulled out as the most relevant context.
4. **Generation**: Those matched events are handed to the Claude API as grounding context, along with the student's question, so Claude answers using real event data instead of guessing.
5. **UI**: The matched events also show up as clickable chips under the chat response, letting students jump straight to them in the main list.

The deployed app already has the required Voyage AI and Claude API keys configured on the backend, so students using the live site don't need to do anything to use the chat feature. If the backend is ever run somewhere without those keys set, the rest of the app works normally and the chat widget just returns a friendly "not configured" message instead of erroring.
