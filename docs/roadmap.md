# Personal library: roadmap

Every step of the project, grouped by phase, with what to build, when it is done, the concepts it teaches, and what it depends on. The live, interactive version with progress and the dev log is the Library build map: https://claude.ai/artifact/9HvvNdDJcaGArAFBAXoYyY

Phases 1 to 5 form the MVP, except steps marked after the MVP.

## 1. Foundations (MVP)

Set up a professional repo and design the data that everything else depends on.

### Repo and workspace

**Build:** GitHub repo with the monorepo layout, pnpm workspace, .gitignore, .editorconfig, README, CLAUDE.md and committed VS Code settings.

**Done when:** The skeleton is on GitHub and your first pull request is merged.

**Needs first:** nothing

**Concepts:**

- Monorepo: One repository holding several apps that share code and tooling.
- Git branching and pull requests: Work on short-lived branches and merge through reviewed PRs, even solo.
- Conventional Commits: A commit message format (feat:, fix:, chore:) that makes history readable.
- Editor config as code: Committing editor settings so the project enforces its own standards.

### Code quality gates

**Build:** Ruff for Python, ESLint and Prettier for TypeScript, wired into pre-commit hooks.

**Done when:** Committing badly formatted code gets fixed or blocked automatically.

**Needs first:** Repo and workspace

**Concepts:**

- Linting vs formatting: Linters catch likely bugs; formatters only change how code looks.
- Pre-commit hooks: Scripts that run checks before a commit is allowed to happen.
- Shift-left quality: Catching problems as early as possible, where they are cheapest to fix.

### Local Postgres with Docker

**Build:** A docker-compose.yml that runs Postgres with pgvector locally, with data that survives restarts.

**Done when:** You can start the database with one command and connect to it from SQLTools.

**Needs first:** Repo and workspace

**Concepts:**

- Containers: Isolated, reproducible environments that package software with its dependencies.
- Volumes: Storage outside the container, so data survives when the container is recreated.
- Ports and networking: How a service inside a container is reached from your machine.
- Connection strings: The URL format apps use to find and log into a database.

### Config and secrets

**Build:** Settings loaded with pydantic-settings from environment variables, with .env ignored and .env.example committed.

**Done when:** The app reads every secret from the environment and none are in Git.

**Needs first:** Repo and workspace

**Concepts:**

- Twelve-factor config: Configuration lives in the environment, never hard-coded in the source.
- Secrets hygiene: Keeping keys and passwords out of version control and logs.
- Environment parity: Keeping local, test and production setups as similar as possible.

### Data model design

**Build:** An entity diagram for items, collections, tags and the item-tag join table, with every field and constraint decided.

**Done when:** A diagram in the repo that you can explain table by table.

**Needs first:** Repo and workspace

**Concepts:**

- Entities and relationships: Modeling the things in your domain and how they connect.
- Primary and foreign keys: Unique row identifiers, and references that link rows across tables.
- Many-to-many join tables: A middle table that lets items have many tags and tags have many items.
- Normalization: Storing each fact once to avoid contradictions and duplication.
- Constraints and indexes: Rules the database enforces, and structures that make lookups fast.

### SQLAlchemy models and migrations

**Build:** SQLAlchemy 2 models matching the design, and Alembic migrations that create the schema.

**Done when:** alembic upgrade head builds every table on a fresh database.

**Needs first:** Local Postgres with Docker, Config and secrets, Data model design

**Concepts:**

- ORM vs raw SQL: Mapping tables to Python classes, and knowing when plain SQL is clearer.
- Sessions and transactions: Grouping changes so they all succeed or all roll back together.
- Schema migrations: Version control for your database structure.
- Upgrade and downgrade: Migrations that can move the schema forward and safely back.

## 2. Core API (MVP)

The FastAPI backend that stores your library, accepts saves and fetches metadata.

### FastAPI skeleton

**Build:** An app with routers, a health check, and a database session injected into routes, run with uv.

**Done when:** The interactive docs at /docs load and the health check reaches the database.

**Needs first:** SQLAlchemy models and migrations

**Concepts:**

- HTTP request and response: Methods, headers, bodies and status codes: the language of the web.
- ASGI and async: How Python serves many requests concurrently without blocking.
- Dependency injection: Routes declare what they need, and the framework provides it.
- OpenAPI: A machine-readable description of your API, generated from your code.

### Schemas and validation

**Build:** Pydantic models describing exactly what each endpoint accepts and returns.

**Done when:** Invalid input returns a clear 422 error and responses never leak internal fields.

**Needs first:** FastAPI skeleton

**Concepts:**

- DTOs vs database models: Keeping the shape you expose separate from how you store data.
- Validation at the boundary: Checking every input where it enters the system.
- Serialization: Converting objects to JSON and back, consistently.

### Items CRUD

**Build:** Routes to list, view, update and delete items, with pagination and filters by collection, tag and source.

**Done when:** You can manage items entirely from the /docs page.

**Needs first:** Schemas and validation

**Concepts:**

- REST resources and verbs: Nouns for URLs, HTTP methods for actions.
- Status codes: 200, 201, 204, 404, 409 and when each is the honest answer.
- Pagination: Returning large lists in pages, and offset vs cursor approaches.
- Query parameters and filtering: Letting clients narrow results without new endpoints.

### Authentication

**Build:** Password login that sets a secure session cookie for the web app, and a bearer token for the Shortcut and extension.

**Done when:** Every route rejects requests without valid credentials.

**Needs first:** FastAPI skeleton, Config and secrets

**Concepts:**

- Authentication vs authorization: Who you are, versus what you are allowed to do.
- Password hashing: Storing a slow one-way hash (argon2 or bcrypt), never the password.
- Cookies and JWTs: Two common ways to keep a user logged in, and their trade-offs.
- Bearer tokens: A secret sent in a header to authenticate non-browser clients.
- CORS: Browser rules about which sites may call your API.

### Capture endpoint

**Build:** POST /capture accepting a URL, an optional note and a source, normalizing the URL and skipping duplicates.

**Done when:** Saving the same link twice, with or without tracking parameters, keeps one item.

**Needs first:** Items CRUD, Authentication

**Concepts:**

- Idempotency: Repeating a request has the same effect as doing it once.
- URL canonicalization: Stripping tracking parameters and variations so equal links match.
- Upserts: Insert-or-update in a single safe database operation.
- One API, many clients: Designing an endpoint that the web app, Shortcut and extension all share.

### Metadata fetcher

**Build:** A background task using httpx to fill in title, description, thumbnail and site name from Open Graph tags and oEmbed.

**Done when:** New items show a proper title and thumbnail within seconds.

**Needs first:** Capture endpoint

**Concepts:**

- Background tasks: Doing slow work after responding, so saving feels instant.
- Timeouts and retries: Never waiting forever on another website.
- Open Graph and oEmbed: The standards sites use to describe their pages and media.
- Failure states: Recording what went wrong so an item is never silently broken.

### Backend tests

**Build:** A pytest suite with a test database and fixtures covering capture, duplicates, auth and filters.

**Done when:** pytest passes locally and covers the paths you would be scared to break.

**Needs first:** Capture endpoint

**Concepts:**

- Unit vs integration tests: Testing pieces in isolation, versus testing them working together.
- Fixtures: Reusable setup such as a clean database or a logged-in client.
- Test isolation: Every test starts from a known state and leaves nothing behind.
- Arrange, act, assert: A simple structure that keeps tests readable.

### Keyword search

**Build:** Postgres full-text search over titles, descriptions, notes and tags, with a GIN index and ranking.

**Done when:** Searching a word from a note finds the item instantly.

**Needs first:** Items CRUD

**Concepts:**

- Full-text search: Matching words and their variations rather than exact strings.
- Stemming: Treating "indexing" and "indexes" as the same root word.
- GIN indexes: The index type that makes text search fast.
- Relevance ranking: Ordering results by how well they match.

## 3. Web app (MVP)

The React app where you browse, organize, annotate and search, on desktop and iPhone.

### Web app scaffold

**Build:** Vite, React and TypeScript with Tailwind and React Router, inside the pnpm workspace.

**Done when:** The app runs with hot reload and passes lint and type checks.

**Needs first:** Code quality gates

**Concepts:**

- SPA vs server rendering: Rendering in the browser, versus on the server, and when each fits.
- Components and props: Building interfaces from small reusable pieces.
- TypeScript types: Catching mistakes before the code runs.
- Build tooling: What Vite does: bundling, transforming and serving your code.

### Typed API client

**Build:** Types generated from the FastAPI schema with openapi-typescript, and data fetching with TanStack Query.

**Done when:** Changing a backend field causes a type error in the web app.

**Needs first:** Web app scaffold, Schemas and validation

**Concepts:**

- Contract-first APIs: The API description is the agreement both sides build against.
- Generated types: Letting tools write the types so they never drift.
- Server state vs client state: Data owned by the server, versus UI state owned by the page.
- Caching and invalidation: Keeping fetched data fresh without refetching everything.

### Login and protected routes

**Build:** A login page, session handling with cookies, and routes that redirect when you are logged out.

**Done when:** You can log in, refresh the page and stay logged in.

**Needs first:** Typed API client, Authentication

**Concepts:**

- Auth flows in single-page apps: How a browser app proves who you are on each request.
- Credentials in requests: Sending cookies to your API across origins safely.
- Route guards: Blocking pages until a condition, like being logged in, is met.

### Library view

**Build:** The main list of items with thumbnails, titles, source icons and infinite scrolling.

**Done when:** Your saves appear in a list that loads more as you scroll.

**Needs first:** Login and protected routes, Items CRUD

**Concepts:**

- Rendering lists and keys: How React tracks items in a list efficiently.
- Loading, empty and error states: Designing every state a screen can be in.
- Infinite queries: Loading pages of data on demand as the user scrolls.

### Item detail and notes

**Build:** A view for one item where you write your note and change its collection and tags.

**Done when:** Notes save as you type and survive a refresh.

**Needs first:** Library view

**Concepts:**

- Controlled forms: Form inputs whose values live in React state.
- Optimistic updates: Updating the screen before the server confirms, and rolling back on failure.
- Debouncing: Waiting for a pause in typing before saving.

### Collections, tags and search

**Build:** Browse by collection, filter by tag and source, and search, with filters kept in the URL.

**Done when:** You can find any saved item in a few seconds, and share a filtered view by URL.

**Needs first:** Item detail and notes, Keyword search

**Concepts:**

- URL as state: Storing filters in the address so views are bookmarkable.
- Combining filters: Composing several conditions cleanly on both client and server.
- Autocomplete: Suggesting existing tags as you type.
- Accessibility basics: Labels, focus states and keyboard navigation.

### Responsive app and PWA

**Build:** A mobile-first layout, a web app manifest and icons so it installs to your iPhone home screen.

**Done when:** It opens full screen from your home screen and is comfortable to use one-handed.

**Needs first:** Library view

**Concepts:**

- Mobile-first CSS: Designing for small screens first, then enhancing for larger ones.
- Viewport and safe areas: Handling notches and system bars on phones.
- Web app manifest: The file that makes a site installable like an app.
- PWA limits on iOS: What installed web apps can and cannot do on iPhone.

## 4. Ship it (MVP)

Get it online with CI, so you use it daily while you build the rest.

### Backend Dockerfile

**Build:** A multi-stage Dockerfile that builds a small, production-ready image of the API.

**Done when:** The image runs locally exactly as it will in production.

**Needs first:** FastAPI skeleton

**Concepts:**

- Images and layers: How images are built and cached step by step.
- Multi-stage builds: Building in one stage and shipping only what is needed.
- Running as non-root: A basic container security habit.

### Deploy API and database

**Build:** The API on a host such as Railway, Render or Fly.io, with managed Postgres and migrations run on deploy.

**Done when:** The live /health endpoint responds over HTTPS.

**Needs first:** Backend Dockerfile, Capture endpoint

**Concepts:**

- Platform as a service: Letting a provider run servers so you focus on the app.
- Managed databases: Hosted Postgres with backups and upgrades handled for you.
- Migrations on deploy: Updating the schema safely as part of each release.
- Health checks: How hosts know your app is alive and ready.

### Deploy web app and domain

**Build:** The web app on Cloudflare Pages or Vercel, on your own domain with HTTPS.

**Done when:** You can log in on your phone at your own address.

**Needs first:** Deploy API and database, Login and protected routes

**Concepts:**

- Static hosting and CDNs: Serving built files from servers close to the user.
- DNS records: How a domain name points to your services.
- TLS certificates: What makes HTTPS secure, and how it is issued automatically.
- Same-site vs cross-site setups: How your domain layout affects cookies and CORS.

### CI with GitHub Actions

**Build:** A workflow that lints, type-checks, tests and builds on every pull request.

**Done when:** A pull request with a failing test cannot be merged.

**Needs first:** Backend tests, Code quality gates

**Concepts:**

- Continuous integration: Automatically verifying every change before it lands.
- Service containers: Running Postgres inside CI for integration tests.
- Branch protection: Requiring checks to pass before merging to main.

### Logs, errors and backups

**Build:** Structured logging, error tracking with Sentry, and a tested database backup.

**Done when:** You are alerted to errors, and you have restored a backup at least once.

**Needs first:** Deploy API and database

**Concepts:**

- Structured logging: Logs as searchable data, not just text.
- Error tracking: Collecting crashes with context so you can fix them.
- Backup and restore: A backup only counts once you have restored it.

## 5. Capture (MVP)

Ways to send things in: the iPhone share menu and a browser extension.

### iPhone Shortcut

**Build:** A share-menu Shortcut that extracts the URL, asks for an optional note and posts it to /capture.

**Done when:** You can save from X, Reddit, LinkedIn, Facebook and YouTube in a couple of taps.

**Needs first:** Deploy API and database

**Concepts:**

- Calling APIs from clients: Building a request with a method, headers and a JSON body.
- Auth headers: Sending your token safely with each request.
- Parsing shared content: Pulling a clean URL out of messy shared text.
- Failure feedback: Telling yourself clearly when a save did not work.

### Extension: save popup

**Build:** A WXT extension with a toolbar popup that saves the current tab with a note.

**Done when:** One click and a note saves any page from Chrome or Firefox.

**Needs first:** Deploy API and database

**Concepts:**

- Manifest V3: The current format and rules for browser extensions.
- Extension contexts: Popup, background worker and content scripts, and how they talk.
- Permissions: Asking only for what the extension truly needs.
- Extension storage: Keeping your token in the extension securely.

### Extension: right-click and YouTube (after the MVP)

**Build:** A right-click save option, and a save button injected on YouTube video pages.

**Done when:** You can save a link without opening it, and save videos with a note in place.

**Needs first:** Extension: save popup

**Concepts:**

- Context menus: Adding actions to the browser right-click menu.
- Content scripts: Code that runs inside other websites.
- DOM injection: Adding your own UI to a page you do not control.

### Extension: detect saves (after the MVP)

**Build:** Notices when you click save on LinkedIn or Facebook and offers to add it too. A stretch goal.

**Done when:** Saving on those sites prompts you to add the item to your library.

**Needs first:** Extension: right-click and YouTube

**Concepts:**

- MutationObserver: Watching a page for changes as it updates.
- Resilient selectors: Finding elements in a way that survives redesigns.
- Platform terms: Knowing where automation crosses a site’s rules.

## 6. Connectors (after the MVP)

Automatic syncing from the apps you save in. After the MVP.

### Connector interface

**Build:** A shared adapter shape every source implements, storing raw payloads and a log of each sync run.

**Done when:** Adding a new source means writing one adapter and nothing else.

**Needs first:** Capture endpoint

**Concepts:**

- Adapter pattern: Translating many different sources into one common shape.
- Raw vs normalized data: Keeping the original payload so you can reprocess it later.
- Protocols in Python: Defining interfaces that classes agree to implement.

### Scheduler

**Build:** Periodic jobs with APScheduler that run each connector safely.

**Done when:** Connectors run on a schedule, and running one twice never duplicates items.

**Needs first:** Connector interface, Deploy API and database

**Concepts:**

- Scheduled jobs: Running work on a timetable, like cron.
- Idempotent jobs: Jobs that are safe to repeat after a failure.
- Retries and backoff: Trying again, waiting longer each time.

### daily.dev RSS

**Build:** Reads your public bookmarks feed and imports new entries.

**Done when:** Bookmarking on daily.dev shows up in your library on the next sync.

**Needs first:** Scheduler

**Concepts:**

- RSS and Atom: Standard feed formats for publishing lists of content.
- Incremental sync: Fetching only what changed since the last run.

### Reddit saved posts

**Build:** Signs in with OAuth and syncs your saved posts and comments.

**Done when:** Saving on Reddit, on any device, appears in your library automatically.

**Needs first:** Scheduler

**Concepts:**

- OAuth authorization code flow: Letting a user grant access without sharing their password.
- Refresh tokens: Staying connected after short-lived access tokens expire.
- Rate limits: Respecting how often an API lets you call it.

### YouTube playlist

**Build:** Syncs the videos in your Library playlist through the YouTube Data API.

**Done when:** Adding a video to the playlist adds it to your library.

**Needs first:** Scheduler

**Concepts:**

- OAuth scopes: Requesting the narrowest access you need.
- API quotas: Budgeting calls against a daily allowance.
- Page tokens: Following continuation tokens through long lists.

### X bookmarks

**Build:** Syncs your bookmarks through the X API, with a spending limit set on your developer account.

**Done when:** Bookmarks sync automatically and the monthly cost stays in cents.

**Needs first:** Scheduler

**Concepts:**

- OAuth 2 with PKCE: A safer OAuth flow for apps that cannot keep a secret.
- Pay-per-use APIs: Designing syncs that minimize billable calls.
- Spending limits: Capping costs so a bug never runs up a bill.

### Export importers

**Build:** Upload LinkedIn and Facebook data exports and import the saved items they contain.

**Done when:** A fresh export imports cleanly without creating duplicates.

**Needs first:** Connector interface

**Concepts:**

- File uploads: Accepting and processing files through an API.
- Parsing CSV and JSON: Reading structured data defensively.
- Data cleaning: Handling missing fields, odd formats and duplicates.

## 7. Search & AI (after the MVP)

Search by meaning, and let AI suggest where things belong. After the MVP.

### AI suggestions

**Build:** Claude suggests a collection and tags for each new item, and you accept or change them.

**Done when:** Most new items arrive with sensible suggestions you rarely need to fix.

**Needs first:** Metadata fetcher, Collections, tags and search

**Concepts:**

- Prompt design: Giving a model clear instructions, context and examples.
- Structured outputs: Getting reliable JSON back from a model.
- Human in the loop: AI proposes, you decide.
- Cost control: Caching, batching and choosing the right model size.

### Embeddings with pgvector

**Build:** Each item gets an embedding stored in Postgres with pgvector.

**Done when:** Every item has a vector, and new items get one automatically.

**Needs first:** Metadata fetcher

**Concepts:**

- Embeddings: Turning text into numbers that capture meaning.
- Cosine similarity: Measuring how close two meanings are.
- Vector indexes: HNSW and similar structures for fast similarity search.

### Semantic search

**Build:** Search by meaning, blended with keyword results into one ranked list.

**Done when:** Describing an item vaguely still finds it.

**Needs first:** Embeddings with pgvector, Keyword search

**Concepts:**

- Hybrid retrieval: Combining keyword and meaning-based search.
- Rank fusion: Merging two ranked lists into one fair ordering.
- Evaluating search: Checking quality with real queries you know the answers to.

### Related items

**Build:** Each item shows the saves most similar to it.

**Done when:** Opening an item surfaces related saves you had forgotten.

**Needs first:** Embeddings with pgvector

**Concepts:**

- Nearest neighbors: Finding the closest vectors to a given one.
- Similarity thresholds: Deciding when "similar" is similar enough.

## 8. Grow further (after the MVP)

Extras once the library is part of your routine.

### Inbox status

**Build:** New items arrive marked new, and you can keep or archive them.

**Done when:** You can hide unreviewed items with one filter.

**Needs first:** Collections, tags and search

**Concepts:**

- State machines: Modeling the allowed states of an item and moves between them.
- Bulk actions: Changing many items at once efficiently.

### Knowledge graph view

**Build:** A visual map linking items through collections, tags and similarity.

**Done when:** You can explore your library visually and spot connections.

**Needs first:** Related items, Collections, tags and search

**Concepts:**

- Graph data: Representing items as nodes and relationships as edges.
- Force-directed layouts: Letting physics arrange a graph readably.
- Rendering performance: Keeping large visuals smooth.

### Resurfacing digest

**Build:** A weekly message bringing back forgotten saves alongside recent ones.

**Done when:** The digest regularly reminds you of things worth revisiting.

**Needs first:** Scheduler, Related items

**Concepts:**

- Spaced repetition: Resurfacing things at growing intervals.
- Sending email or messages: Delivering notifications reliably.

### Saving stats

**Build:** Charts of what you save over time, by source and topic.

**Done when:** You can see how your interests change month to month.

**Needs first:** Collections, tags and search

**Concepts:**

- Aggregation queries: GROUP BY and window functions for summaries.
- Data visualization: Choosing the chart that answers the question.
