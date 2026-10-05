# 2. Tech stack

Date: 2026-10-05

## Status

Accepted

## Context

The app has three surfaces: a web app that must work well on an iPhone, a
browser extension, and a backend with AI features (tagging, embeddings,
semantic search). The browser parts must be JavaScript or TypeScript. The
backend language is open. My learning goals are backend, databases, APIs and
AI.

## Decision

- **Backend:** Python with FastAPI, SQLAlchemy 2 and Alembic. Python is where
  the AI and data tooling is strongest, and FastAPI generates an OpenAPI
  schema used to type the frontends.
- **Database:** PostgreSQL with pgvector, covering relational data, full-text
  search and vector search in one system.
- **Web app:** React, TypeScript and Vite as a single-page app, since the API
  is separate. TanStack Query for server state.
- **Mobile:** an installable PWA plus an iOS Shortcut for capture, instead of a
  native app.
- **Extension:** TypeScript with WXT.
- **Repository:** one monorepo with pnpm workspaces for the TypeScript apps,
  and types generated from the API schema.

## Alternatives considered

- **TypeScript everywhere** (for example Next.js or Hono): one language and
  easier code sharing, but a weaker fit for the AI work and my learning goals.
- **Next.js for the web app:** server rendering adds complexity that a
  single-user app behind a separate API does not need.
- **A native iOS app:** would allow a real share extension, but is a large
  separate project. The Shortcut covers capture well enough for now.

## Consequences

- Two languages to maintain, each in its natural home.
- The API schema becomes the contract between backend and frontends, so type
  generation must be part of the workflow.
- Postgres carries a lot of responsibility; if search outgrows it, a dedicated
  search service could be added later.
