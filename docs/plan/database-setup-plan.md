# Design Plan: Database Setup (Step 1)

## Overview

Implement the SQLite data layer for Spendly. This is the foundation all future features depend on.

**Spec source:** `docs/specs/database-setup.md`

---

## Schema

### users

| Column        | Type    | Constraints                     |
|---------------|---------|----------------------------------|
| id            | INTEGER | PRIMARY KEY AUTOINCREMENT        |
| name          | TEXT    | NOT NULL                         |
| email         | TEXT    | UNIQUE NOT NULL                  |
| password_hash | TEXT    | NOT NULL                         |
| created_at    | TEXT    | DEFAULT (datetime('now'))        |

### expenses

| Column      | Type    | Constraints                          |
|-------------|---------|---------------------------------------|
| id          | INTEGER | PRIMARY KEY AUTOINCREMENT             |
| user_id     | INTEGER | NOT NULL, REFERENCES users(id)        |
| amount      | REAL    | NOT NULL                              |
| category    | TEXT    | NOT NULL                              |
| date        | TEXT    | NOT NULL (YYYY-MM-DD)                 |
| description | TEXT    | Nullable                              |
| created_at  | TEXT    | DEFAULT (datetime('now'))             |

**Allowed categories:** Food, Transport, Bills, Health, Entertainment, Shopping, Other

---

## Function Contracts

### `get_db()`
- Connects to `expense_tracker.db` in project root
- Sets `row_factory = sqlite3.Row` (dict-like row access)
- Executes `PRAGMA foreign_keys = ON`
- Returns the connection

### `init_db()`
- Creates both tables using `CREATE TABLE IF NOT EXISTS`
- Safe to call on every app startup (idempotent)

### `seed_db()`
- Checks if any rows exist in `users`; returns early if yes (prevents duplicate inserts)
- Inserts 1 demo user: `demo@spendly.com` / `demo123` (password hashed with werkzeug)
- Inserts 8 sample expenses covering all 7 categories, dated in current month

---

## `app.py` Wiring

```python
from database.db import get_db, init_db, seed_db

app = Flask(__name__)

with app.app_context():
    init_db()
    seed_db()
```

The `app_context()` block ensures the DB is ready before any request is handled.

---

## Implementation Rules

- No ORMs — raw `sqlite3` only
- Parameterized queries (`?` placeholders) — no string formatting in SQL
- `PRAGMA foreign_keys = ON` on every connection
- Passwords hashed with `werkzeug.security.generate_password_hash`
- Dates stored as `TEXT` in `YYYY-MM-DD` format
- `amount` stored as `REAL` (not INTEGER)

---

## Files Changed

| File | Action |
|------|--------|
| `database/db.py` | Implement `get_db`, `init_db`, `seed_db` |
| `app.py` | Add imports + startup DB initialization |

## Files NOT Changed

- `database/__init__.py` — remains empty
- `requirements.txt` — no new packages (`sqlite3` is stdlib, `werkzeug` already installed)
- All templates and static assets

---

## Verification Checklist

- [ ] `python app.py` starts without errors and creates `expense_tracker.db`
- [ ] Running again does not insert duplicate seed data
- [ ] `users` table has 1 row; `expenses` table has 8 rows
- [ ] Inserting a second `demo@spendly.com` raises `IntegrityError` (UNIQUE constraint)
- [ ] Inserting an expense with a non-existent `user_id` raises `IntegrityError` (FK constraint)
- [ ] All expense rows use parameterized queries (no string interpolation in SQL)
