# Couch Oracle

A self-hosted recommender for movies, TV shows, books and video games, for
people who no longer subscribe to Netflix, HBO and co. but miss their
"you might also like" suggestions.

> **Status:** work in progress, started for Hack Week 2026. See
> [docs/PLAN.md](docs/PLAN.md) for the plan and progress.

## Idea

1. It starts like the classic 20 Questions game: "Have you seen this? Did you
   like it?"
2. It shows Netflix-style rows of covers ("Because you liked X", "For you",
   "Short on time").
3. Every title gets quick feedback, which shapes the next suggestions:

| Button | Hidden? | Effect on suggestions |
|---|---|---|
| Seen it, liked it | Forever | Strong positive. Creates a "Because you liked X" row |
| Seen it, didn't like it | Forever | Negative. Similar titles rank lower |
| Not interested, remove it | Forever | Weaker negative (you haven't seen it) |
| Maybe later | 30 days | None |
| Skip | No | None |

All data stays on your machine.

## How recommendations work

Each title is turned into a text block (genres, keywords, synopsis, never the
title) and embedded with a small local model. Titles with nearby vectors are
similar in meaning, so a liked book can suggest a movie or a game.

Candidates are searched **per favorite**, not against an average of all
favorites, so two liked superhero films don't turn every row into superheroes.
A diversity step (MMR) keeps the picks in a row from being near copies of each
other.

## Data sources

| Type | Source | Credential |
|---|---|---|
| Movies and TV | [TMDB](https://www.themoviedb.org/) | API key or read token |
| Books | [Open Library](https://openlibrary.org/) | None |
| Games | [RAWG](https://rawg.io/apidocs) | API key |

Check each service's terms before using the data beyond personal use.

## Stack

Python 3.13, [Flask](https://flask.palletsprojects.com/) with Jinja2 templates,
[htmx](https://htmx.org/), SQLite, `sentence-transformers` (CPU build of
PyTorch) and NumPy. Dependencies are managed with [uv](https://docs.astral.sh/uv/).

## Getting started

Requirements: [uv](https://docs.astral.sh/uv/) and Python 3.13.

```
git clone https://github.com/pablo-herranz/couch-oracle.git
cd couch-oracle
uv sync
```

### API keys

Keys are read from the system keyring through `secret-tool` (Linux), so they
never live in the repo or in a `.env` file:

```
secret-tool store --label='couch-oracle TMDB key' application couch-oracle purpose tmdb-api-key
secret-tool store --label='couch-oracle RAWG key' application couch-oracle purpose rawg-api-key
```

Each command prompts for the key.

### Run

Not available yet. The fetch script and the web app are planned for the first
days of Hack Week.

## Project layout (planned)

```
src/couch_oracle/
  app.py            Flask routes
  models.py         Item and Interaction
  db.py             SQLite
  engine.py         candidates, scoring, diversity, rows
  embeddings.py     model and vectors
  sources/          tmdb.py, openlibrary.py, rawg.py
  templates/        Jinja2 + htmx
  static/           CSS, keyboard/remote navigation script
scripts/fetch_catalog.py
tests/
data/               local database (not committed)
```

## Goals and non-goals

- The UI is designed for a TV from day one: large text, covers, and navigation
  with only the arrow keys and Enter, as a first step toward a Fire TV.
- Not planned for now: user accounts, downloading or streaming content, or
  integration with media servers.

## License

Not chosen yet.
