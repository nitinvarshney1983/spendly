# Architecture — Spendly Expense Tracker

## Overview

Spendly is a server-rendered Flask web application backed by SQLite. There is no frontend framework — Jinja2 handles all HTML rendering, and vanilla JS is added incrementally as features are built.

```
Browser  ──HTTP──▶  Flask (app.py)  ──▶  database/db.py  ──▶  SQLite (.db file)
                         │
                    Jinja2 templates
                    (templates/)
```

## Directory Structure

```
expense-tracker/
├── app.py                  # Flask app factory and all route definitions
├── requirements.txt        # Python dependencies (Flask, Werkzeug, pytest)
├── database/
│   ├── __init__.py         # Empty — makes database/ a package
│   └── db.py               # SQLite helpers: get_db(), init_db(), seed_db()
├── templates/
│   ├── base.html           # Shared layout: nav, footer, font/CSS links
│   ├── landing.html        # Public marketing page (/)
│   ├── login.html          # (/login)
│   ├── register.html       # (/register)
│   ├── about-us.html       # (/about)
│   └── terms-&-condition.html  # (/terms)
├── static/
│   ├── css/style.css       # All application styles
│   └── js/main.js          # Vanilla JS (minimal; grows with features)
└── docs/
    └── architecture.md     # This file
```

## Layer Responsibilities

### `app.py` — Routing layer
- Single file for all Flask routes.
- Completed routes call `render_template()`; placeholder routes return a string describing the upcoming step.
- Runs on **port 5001** (`debug=True` in development).

### `database/db.py` — Data layer
Three functions make up the entire database interface:

| Function | Purpose |
|----------|---------|
| `get_db()` | Opens (or reuses) a SQLite connection. Sets `row_factory = sqlite3.Row` so rows behave like dicts, and enables `PRAGMA foreign_keys = ON`. |
| `init_db()` | Creates all tables using `CREATE TABLE IF NOT EXISTS`. Safe to call on every startup. |
| `seed_db()` | Inserts sample rows for local development. |

The SQLite file (`expense_tracker.db`) is gitignored. Each developer runs `init_db()` and `seed_db()` locally.

### `templates/` — Presentation layer
All templates extend `base.html` via `{% extends "base.html" %}`.

`base.html` provides:
- Google Fonts load (DM Serif Display, DM Sans)
- Global `style.css` and `main.js` links
- `<nav>` with brand + Sign in / Get started links
- `<footer>` with About and Terms links
- Blocks: `title`, `head`, `content`, `scripts`

### `static/` — Assets
- `style.css` — monolithic stylesheet; no preprocessor.
- `main.js` — starts nearly empty; students add JS as each feature step is completed.

## Request Lifecycle (example: GET /login)

1. Browser sends `GET /login`.
2. Flask matches the `/login` route in `app.py`.
3. Route calls `render_template("login.html")`.
4. Jinja2 merges `login.html` into `base.html` and returns HTML.
5. Browser renders the page and loads `style.css` + `main.js`.

## Incremental Feature Steps

Routes are stubbed in `app.py` before implementation, documenting the build order:

| Step | Route | Status |
|------|-------|--------|
| 1 | Database (`database/db.py`) | Stub (student writes) |
| 2 | Register / Login (auth) | Templates exist; logic pending |
| 3 | `/logout` | Stub |
| 4 | `/profile` | Stub |
| 7 | `/expenses/add` | Stub |
| 8 | `/expenses/<id>/edit` | Stub |
| 9 | `/expenses/<id>/delete` | Stub |

## Dependencies

| Package | Role |
|---------|------|
| `flask==3.1.3` | Web framework, routing, templating |
| `werkzeug==3.1.6` | Password hashing utilities |
| `pytest==8.3.5` | Test runner |
| `pytest-flask==1.3.0` | Flask test client fixtures |
