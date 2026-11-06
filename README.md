
## Run Shamba Direct

1. Copy `.env.example` to `.env` and change the local database passwords.
2. Build and start the app and database:

```sh
docker compose up --build -d
```

3. Open <http://localhost:3001>. The API and frontend are served by the same app container.


## Known MVP limits

1. Single user, no accounts. Add auth and a `user_id` column before exposing publicly.
2. Reminders only reach open browser tabs. Next: web push, email, or WhatsApp/Telegram delivery.
3. The scheduler is in-process; run one instance, or move to a job queue when scaling.
4. SQLite keeps setup at zero. The queries are plain SQL.
5. Add streaming replies, recurring reminders, and calendar/email integrations.
