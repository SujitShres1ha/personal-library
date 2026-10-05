# Dev log

A running journal of the project, newest entries at the bottom of each day.
Entry types: **Progress** (what I did), **Learned** (insights), **Problem**
(bugs and blockers, and how they were solved), **Decision** (choices and why).
Add entries with `/log` in Claude Code, or by hand.

## 2026-10-05

- **Decision** (Planning): Building a personal library that gathers saves from X, Reddit, Facebook, LinkedIn, YouTube and daily.dev into one place, with notes, collections, tags and search. Single-user, and a learning project.
- **Decision** (Planning): Storing links plus light metadata rather than full copies of content.
- **Decision** (Planning): Capture via API syncs where available (X, Reddit, YouTube playlist, daily.dev RSS), an iPhone Shortcut for everything on the phone, and a browser extension on desktop.
- **Decision** (Planning): Stack is Python/FastAPI with Postgres and pgvector for the backend, React/TypeScript/Vite for the web app and PWA, WXT for the extension. See ADR 0002.
- **Decision** (Planning): MVP covers foundations, core API, web app, deployment, the Shortcut and a basic extension. Connectors and AI come after.
- **Progress** (Repo and workspace): Starter repository prepared with CLAUDE.md, VS Code config, editorconfig, gitignore, monorepo folders and ADRs 0001 and 0002.
