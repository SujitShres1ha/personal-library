# CLAUDE.md

Guidance for Claude Code when working in this repository.

## Project

A personal library that gathers the articles, videos and posts I save across
X, Reddit, Facebook, LinkedIn, YouTube and daily.dev into one organized place.
Each item has my own note on why it matters, sits in a collection with tags,
and can be found by browsing or searching.

This is a single-user app. It is also a learning project: I am building it to
understand the full process, from design to deployment, using industry
standards.

## How we work together

I want to learn, not just ship. Please follow these rules:

- **Explain before you write.** For any non-trivial change, describe the
  approach and the reasoning first, and mention the main alternative and why
  we are not choosing it. Wait for my go-ahead before large changes.
- **Keep changes small.** One concern per change, small enough to review in a
  few minutes. Prefer several small steps over one big one.
- **Let me write parts myself.** When I say "my turn", give me guidance and
  hints instead of the full solution, then review what I wrote.
- **Review like a senior engineer.** Point out bugs, unclear naming, missing
  tests, security issues and anything that would not pass code review.
- **Name the concepts.** When something introduces a new concept (for example
  idempotency, migrations or CORS), name it and explain it in a sentence or two.
- **No magic.** Do not add dependencies, abstractions or config I did not ask
  for without explaining why they are needed.
- **Suggest a log entry.** When we finish a piece of work, suggest a short dev
  log entry for me, labelled Progress, Learned, Problem or Decision.
- **Record decisions.** When we make a significant technical decision, suggest
  an Architecture Decision Record in `docs/adr/`.

## Tech stack

| Layer | Choice |
|---|---|
| Backend | Python, FastAPI, SQLAlchemy 2, Alembic, Pydantic |
| Database | PostgreSQL with pgvector |
| Background jobs | FastAPI background tasks, APScheduler |
| Web app | React, TypeScript, Vite, TanStack Query, React Router, Tailwind |
| Mobile | Installable PWA, plus an iOS Shortcut for capture |
| Extension | TypeScript, WXT (Manifest V3) |
| Shared types | pnpm workspaces, openapi-typescript |
| Tooling | uv, Ruff, pytest, pnpm, ESLint, Prettier, Vitest, Docker Compose |
| CI | GitHub Actions |

## Repository layout

```
backend/              FastAPI app (Python, managed with uv)
apps/web/             React + Vite web app and PWA
apps/extension/       WXT browser extension
packages/api-client/  TypeScript types generated from the API schema
docs/adr/             Architecture Decision Records
```

## Conventions

### General
- Secrets live in `.env` files, which are never committed. Every required
  variable is listed in `.env.example` with a placeholder value.
- Formatting is automatic and not up for debate: Ruff for Python, Prettier for
  everything else.
- Prefer clear names over comments. Use comments to explain *why*, not *what*.

### Python (backend)
- Python 3.12+, fully type-annotated.
- Ruff for linting and formatting. Imports sorted by Ruff.
- Keep API schemas (Pydantic) separate from database models (SQLAlchemy).
- Routes stay thin: business logic lives in service functions that can be
  tested without HTTP.
- Tests use pytest, live in `backend/tests/`, and follow arrange, act, assert.

### TypeScript (web app and extension)
- Strict TypeScript. No `any` without a comment explaining why.
- Function components and hooks only.
- Server data goes through TanStack Query, never ad-hoc `useEffect` fetching.
- API types come from `packages/api-client`, never hand-written.

### Database
- Every schema change is an Alembic migration, reviewed before it is applied.
- Migrations must be reversible unless there is a documented reason.

### Git
- `main` is always deployable. Work happens on short-lived branches named
  `type/short-description`, for example `feat/capture-endpoint`.
- Commit messages follow Conventional Commits: `feat:`, `fix:`, `docs:`,
  `chore:`, `refactor:`, `test:`, `ci:`.
- Changes reach `main` through pull requests, even when working solo.

## Project docs

Read these when you need context. They are the source of truth.

- `docs/project-brief.md`: what the app is and everything decided so far.
- `docs/roadmap.md`: every step by phase, with "Done when" lines, concepts and
  dependencies.
- `docs/dev-log.md`: what has actually been done, learned and decided.
- `docs/adr/`: the reasoning behind significant decisions.
- `docs/getting-started.md`: machine setup and project commands.

If a request conflicts with the brief or an ADR, point it out before going
ahead. When a decision changes, remind me to update `docs/project-brief.md`.

## Workflow commands

- `/next`: work out the next step and plan it.
- `/learn <concept>`: explain a concept in the context of this project.
- `/review`: review my current changes like a senior engineer.
- `/log <type> <text>`: add an entry to the dev log.
- `/adr <title>`: draft an Architecture Decision Record.
- `/done <step>`: check a step is finished, quiz me, and draft log entries.

## Commands

To be filled in as each part of the project is set up. Keep this section and
`docs/getting-started.md` in sync.

## Current focus

Progress, concepts learned and status are tracked in the Library build map:
https://claude.ai/artifact/9HvvNdDJcaGArAFBAXoYyY
Current step: Foundations, "Repo and workspace". Recommended next: "Data
model design".
