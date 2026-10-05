# Personal library: project brief

The single source of truth for what this project is and what has been decided.
Last updated: 2026-10-05.

## What it is

A personal library: one organized place for every article, video and post I
judge worth coming back to, from the apps I use. Each item has my own note on
why it matters, sits in a collection with tags, and can be found by browsing
or searching. It is a library, not a read-later queue: success means nothing
important gets lost, everything has a sensible place, and I can find any item
months later.

It is single-user (just me) and also a learning project. I am an intermediate
developer building it to understand the full process, from design to
deployment, using industry standards. Backend, databases, APIs and AI are what
I most want to learn.

## Key product decisions

- **Links, not copies.** Store the link plus light metadata (title,
  description, thumbnail, author, site, source app), not full article text or
  transcripts.
- **My own context.** Every item can carry a note on why I saved it, ideally
  written at the moment of saving.
- **Organization.** Collections (broad shelves I define) plus tags (finer
  detail, several per item), plus search across titles, descriptions, tags and
  notes. Later, AI suggests a collection and tags, which I accept or correct.
- **Inbox question undecided.** Every item gets a status (new or kept) so I can
  decide later whether to review items before they count as "in the library".
- **Reminders are secondary.** Resurfacing and digests come after the core
  library works.

## Where things come from

| Source | How it gets in |
|---|---|
| X | Bookmarks API (pay-per-use; reading own bookmarks costs cents) |
| Reddit | API sync of saved posts (OAuth) |
| YouTube | API sync of a regular playlist I save to; Watch Later is not available through the API |
| daily.dev | Public bookmarks RSS feed |
| LinkedIn | No API. Data export import, plus iPhone Shortcut for ongoing saves |
| Facebook | No API. Data export import, plus iPhone Shortcut for ongoing saves |
| Any website | Browser extension on desktop |

My habits: YouTube and articles mostly on desktop browser; everything else
mostly on iPhone. The iPhone Shortcut works from every app's share menu, so it
covers all sources from day one. Automatic syncs are a convenience upgrade.

## Clients

- **Web app:** responsive, installable as a PWA on iPhone.
- **iPhone Shortcut:** share menu, extracts the URL, asks for an optional note,
  posts to the capture endpoint with a token. (iOS web apps cannot be share
  targets, hence the Shortcut.)
- **Browser extension:** toolbar popup to save the current page with a note
  first; later right-click saving, a YouTube save button, and detecting saves
  on LinkedIn and Facebook as a stretch goal.
- All clients use the same capture endpoint.

## Tech stack

| Layer | Choice |
|---|---|
| Backend | Python, FastAPI, SQLAlchemy 2, Alembic, Pydantic |
| Database | PostgreSQL with pgvector |
| Background jobs | FastAPI background tasks, APScheduler |
| Web app | React, TypeScript, Vite, TanStack Query, React Router, Tailwind |
| Mobile | Installable PWA plus iOS Shortcut |
| Extension | TypeScript, WXT (Manifest V3) |
| Shared types | pnpm workspaces, openapi-typescript |
| AI | Claude for tagging; embedding provider to be chosen later |
| Hosting | Managed Postgres with pgvector, Railway/Render/Fly.io for the API, Cloudflare Pages or Vercel for the web app |
| Tooling | uv, Ruff, pytest, pnpm, ESLint, Prettier, Vitest, Docker Compose, GitHub Actions, pre-commit |

Reasoning is recorded in docs/adr/0002-tech-stack.md.

## MVP

Goal: anything worth keeping, from any app, ends up in one organized place,
and I can find it again.

In: data model, Postgres, FastAPI with token auth, capture endpoint with
duplicate detection, item routes, metadata fetcher, backend tests, keyword
search (Postgres full-text), web app (login, library list, item detail with
notes, collections, tags, search, responsive, PWA manifest), deployment, CI,
iPhone Shortcut, basic extension popup.

Out for now: automatic connectors, AI suggestions, embeddings and semantic
search, inbox status, knowledge graph, digests, advanced extension features.

Success test: route every save through it for two weeks and see whether I go
back to it to find things.

## Data model (MVP)

- **items:** id, url, normalized_url (unique), title, description,
  thumbnail_url, site_name, source, note, collection_id, status, metadata
  state, created_at, updated_at
- **collections:** id, name
- **tags:** id, name
- **item_tags:** item_id, tag_id

No users table; it is single-user. Password and capture token come from
environment variables.

## API (MVP)

| Endpoint | Purpose |
|---|---|
| POST /auth/login | Log into the web app |
| POST /capture | Save a URL with an optional note (Shortcut, extension) |
| GET /items | List with search, collection and tag filters |
| GET, PATCH, DELETE /items/{id} | View, edit, remove an item |
| GET, POST, PATCH, DELETE /collections | Manage collections |
| GET /tags | Tags for autocomplete |

## How I am tracking it

- **Library build map:** an interactive roadmap (trunk, branches, leaves) with
  45 steps, the concepts each step teaches, status, and a dev log.
  https://claude.ai/artifact/9HvvNdDJcaGArAFBAXoYyY
- **Dev log entry types:** Progress, Learned, Problem, Decision.
- **ADRs** in docs/adr/ for significant decisions.
- **Working with Claude Code in VS Code**, guided by CLAUDE.md in the repo.

## Status

Starter repository prepared (CLAUDE.md, VS Code config, editorconfig,
gitignore, monorepo folders, ADRs 0001 and 0002). Current step: Foundations,
"Repo and workspace". Recommended next: data model design.
