# Action plan

Hack Week 2026, solo project. Tick items as they are done.

## Goal

By Friday, a demo that shows: onboarding questions, rows of covers mixing
movies, TV, books and games, feedback buttons, and rows that change after the
feedback. It must be usable with only the keyboard arrows and Enter.

## Decisions already made

- **Stack:** Python, Flask, Jinja2, htmx, SQLite, sentence-transformers, uv.
  Chosen to keep as few new tools as possible.
- **Interface:** local web UI with covers, designed for a TV.
- **Sources:** TMDB (movies, TV), Open Library (books), RAWG (games).
- **Similarity:** embeddings of genres, keywords and synopsis, never titles.
- **Duration:** TMDB `runtime`, RAWG `playtime`, Open Library page count.
  HowLongToBeat is excluded because its terms don't allow automated access.
- **Feedback:** the five buttons in the README table. The weights need testing
  with real use.

## Before Monday

- [x] TMDB key in the keyring
- [x] RAWG key in the keyring
- [x] Repository created and public
- [x] Project initialized with uv, dependencies installed
- [x] README

## Monday: base, movies and TV (about 6 h)

- [ ] `models.py`: `Item` and `Interaction`
- [ ] `db.py`: create the SQLite tables
- [ ] `sources/tmdb.py`: movies and TV with Spanish synopsis, runtime and poster
- [ ] `scripts/fetch_catalog.py`: about 300 titles per type, with embeddings
- [ ] **Done when:** the database has about 600 titles with poster and vector

## Tuesday: recommendation engine (about 6 h)

- [ ] Store interactions for the five buttons
- [ ] Candidates: nearest titles to each favorite separately
- [ ] Scoring: similarity, minus similarity to disliked titles, plus a little popularity
- [ ] Diversity with MMR, and row generation
- [ ] Tests: hidden titles never come back, "maybe later" expires after 30 days
- [ ] **Done when:** tests pass and a Python call returns rows

## Wednesday: web UI (about 6 h)

- [ ] Base template for 1920x1080: large text, horizontal rows of covers
- [ ] Feedback buttons with htmx, updating the row without reloading
- [ ] Keyboard navigation script with a clearly visible focus
- [ ] 20 Questions onboarding using the same buttons
- [ ] **Done when:** the whole app works with only the keyboard

## Thursday: books and games (about 6 h)

- [ ] `sources/openlibrary.py`: subjects as fallback text, page count as duration
- [ ] `sources/rawg.py`: per-game detail for description, `playtime` as duration
- [ ] Grow the catalog to about 1,200 titles; check the wide RAWG images crop well
- [ ] Try cross-type recommendations, for example book to movie
- [ ] **Done when:** rows mix the four types with covers

## Friday: buffer and demo

- [ ] Fix whatever slipped
- [ ] Update the README with real run instructions
- [ ] Full demo using only the keyboard

## If a day runs long, cut in this order

1. 20 Questions onboarding (start by liking titles in the popular rows)
2. "Short on time" row
3. Cross-type recommendations

## Risks

- **Embeddings across types:** a book synopsis and a game description are written
  differently, so cross-type matches may be weaker. Test early on Thursday.
- **Open Library:** many works have no synopsis. Subjects are the fallback.
- **Feedback weights:** the values are a first guess. Adjust after real use.

## After Hack Week

- Run it on a Fire TV browser and test the remote.
- Run the server on an always-on machine.
- Optional: send a liked title to Seerr, if a media server is ever set up.
