Put your schema/seed file here as e.g. 01-schema.sql.
MySQL runs every .sql file in this folder, in alphabetical order,
only the first time the database volume is created.
To re-run them: docker compose down -v && docker compose up -d --build
