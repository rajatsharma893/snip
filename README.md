# Snip: URL Shortener with Click Analytics

A full-stack web app where users sign up, shorten links (custom alias and expiry optional), and see
click analytics: clicks per day, device split and top referrers.

**Stack:** Python, FastAPI, SQLAlchemy, SQLite (swap to PostgreSQL via `DATABASE_URL`), JWT auth, vanilla JS frontend, pytest, Docker.

## Features
- Register / login with salted PBKDF2 password hashing and JWT sessions
- Create short links: random 6-char code or custom alias, optional expiry (410 after expiry)
- Redirect (`/<code>`) logs every click (time, referrer, device)
- Per-link dashboard: 7-day chart, devices, top referrers
- Each user can only see and delete their own links
- URL validation (only http/https), reserved-alias protection, per-IP rate limit on link creation
- 7 automated API tests

## Run locally (VS Code terminal)
```bash
python -m venv .venv
.venv\Scripts\activate          # Windows
# source .venv/bin/activate     # Mac / Linux
pip install -r requirements-dev.txt
uvicorn app.main:app --reload
```
Open http://127.0.0.1:8000 (app) and http://127.0.0.1:8000/docs (auto API docs).

## Run tests
```bash
python -m pytest -q
```

## Deploy (Render / Railway)
- Build: `pip install -r requirements.txt`
- Start: `uvicorn app.main:app --host 0.0.0.0 --port $PORT`
- Environment variable: `SECRET_KEY` = a long random string
- For permanent data use PostgreSQL and set `DATABASE_URL` (the SQLite file resets on free hosts)

## Design decisions
- Redirect uses 302 so browsers don't cache it and every click is counted.
- Click rows are stored individually, so any analytics can be added later without a schema change.
- Rate limiter is in-memory (fine for one instance); use Redis for multi-instance.

## Ideas to extend
- QR code per link, link-preview safety check, password-protected links
- Redis cache for hot redirects, Alembic migrations, PostgreSQL
