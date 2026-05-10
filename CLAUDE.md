# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Spendly** is a Flask-based personal expense tracker for Indian users (currency: ₹). This is a step-by-step learning project — students implement features incrementally. The app uses SQLite (via `database/db.py`) and Jinja2 templates.

## Running the App

```bash
# Create and activate virtual environment
python -m venv venv
venv\Scripts\activate   # Windows

pip install -r requirements.txt
python app.py           # runs on http://localhost:5001
```

## Running Tests

```bash
pytest                  # all tests
pytest tests/test_db.py # single test file
```

## Architecture

See [`docs/architecture.md`](docs/architecture.md) for the full architecture document, including directory structure, layer responsibilities, request lifecycle, and the incremental feature step map.

## Step-by-Step Feature Map

The project is structured around numbered steps. Placeholder routes in `app.py` document what's coming:

| Step | Feature |
|------|---------|
| 1 | Database setup (`database/db.py`) |
| 3 | Logout |
| 4 | Profile page |
| 7 | Add expense |
| 8 | Edit expense |
| 9 | Delete expense |

## Database Conventions

- SQLite file: `expense_tracker.db` (gitignored)
- `get_db()` must set `row_factory = sqlite3.Row` and `PRAGMA foreign_keys = ON`
- Use `CREATE TABLE IF NOT EXISTS` in `init_db()`
- The `database/` package has an empty `__init__.py`

## Key Constraints

- Database file is gitignored — run `init_db()` / `seed_db()` locally after cloning.
- The app runs on port **5001** (not the Flask default 5000).
- No JS framework — vanilla JS only.
