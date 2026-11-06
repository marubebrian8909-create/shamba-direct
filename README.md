# Bubblebee 🐝

A personal-assistant MVP. Chat in plain language; Bubblebee turns it into tasks, reminders and notes using Claude tool use, and pings you when a reminder is due.

This repository also contains the **Shamba Direct** web application under [`shamba/`](./shamba/).

## Run Bubblebee

```bash
npm install
cp .env.example .env      # add your ANTHROPIC_API_KEY
node --env-file=.env src/server.js
# open http://localhost:3000
```

`npm test` runs a smoke test of the tools and scheduler (no API key needed).

## Run Shamba Direct

1. Copy `.env.example` to `.env` and change the local database passwords.
2. Build and start the app and database:

```sh
docker compose up --build -d
```

3. Open <http://localhost:3001>. The API and frontend are served by the same app container.

## How Bubblebee works

```
browser ──POST /api/chat──▶ agent.js ──▶ Claude (tools) ──▶ tools.js ──▶ SQLite
   ▲                                                                      │
   └──────────── SSE /api/events ◀── scheduler.js (checks every 10s) ◀────┘
```

- `src/agent.js` – tool-use loop (max 6 steps), replays the last 20 turns, injects the user's local time and timezone so "tomorrow at 9" resolves correctly.
- `src/tools.js` – 8 tools: add/list/complete task, set/list/cancel reminder, save/search notes. Bad input returns an error to the model so it can self-correct.
- `src/scheduler.js` – fires due reminders and pushes them to open tabs over Server-Sent Events (toast + browser notification).
- `public/index.html` – single-file UI, no build step.

## Voice

`public/voice.js` adds speech input and spoken replies using the browser's built-in Web Speech APIs (no extra packages or server changes).

- **Mic button:** tap, speak, and the transcript is sent as a normal chat message. You can watch it appear in the input as you talk.
- **Speak replies aloud:** Bubblebee reads each reply and any due reminder. Markdown, links and emoji are stripped before speaking.
- **Hands-free conversation:** after each spoken reply the mic reopens. It pauses itself after three silent turns.
- **Language and voice** are chosen in the Voice menu and remembered per browser.

Requirements: Chrome, Edge or Safari (Firefox has no speech recognition, so only spoken replies work there). The page must be on `localhost` or HTTPS for the microphone to be allowed. Chrome's recogniser sends audio to Google's service, so it needs internet access.

## Config

| Variable | Purpose |
|---|---|
| `ANTHROPIC_API_KEY` | required |
| `BUBBLEBEE_MODEL` | defaults to `claude-sonnet-5` |
| `BUBBLEBEE_KEY` | optional shared secret; clients send `x-bubblebee-key` |
| `PORT`, `BUBBLEBEE_DB` | port and SQLite path |

## Known MVP limits

1. Single user, no accounts. Add auth and a `user_id` column before exposing publicly.
2. Reminders only reach open browser tabs. Next: web push, email, or WhatsApp/Telegram delivery.
3. The scheduler is in-process; run one instance, or move to a job queue when scaling.
4. SQLite keeps setup at zero. The queries are plain SQL.
5. Add streaming replies, recurring reminders, and calendar/email integrations.
