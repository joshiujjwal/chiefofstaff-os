# CLAUDE.md — ChiefOfStaff OS

Context file for Claude agents. Read this before touching any code.

---

## Commands

```bash
# Install all dependencies (run from root)
TODO: npm install

# Dev servers (starts both client and server with hot reload)
TODO: npm run dev

# Run all tests
TODO: npm test

# Run server tests only
TODO: npm run test:server

# Run client tests only
TODO: npm run test:client

# Lint + typecheck
TODO: npm run lint
TODO: npm run typecheck

# Database migrations
TODO: npm run db:migrate        # apply pending migrations
TODO: npm run db:migrate:down   # rollback last migration
TODO: npm run db:seed           # seed dev data

# Build for production
TODO: npm run build
```

> Replace all `TODO:` above with real commands once package.json is set up (Phase 0).

---

## Directory Map

```
chiefofstaff-os/
├── client/               # React frontend (Vite + TypeScript)
│   ├── src/
│   │   ├── components/   # Reusable UI components (shadcn + custom)
│   │   ├── pages/        # Route-level page components
│   │   ├── hooks/        # Custom React hooks (data fetching, state)
│   │   ├── lib/          # API client, utilities, constants
│   │   └── types/        # Client-side TypeScript types
│   └── tests/            # Vitest + Testing Library
├── server/               # Node.js + Express backend (TypeScript)
│   ├── src/
│   │   ├── api/          # Express route handlers (thin — delegate to services)
│   │   ├── db/           # Drizzle schema definitions + migrations
│   │   ├── services/     # Business logic (no Express dependency here)
│   │   └── types/        # Shared server types
│   └── tests/
│       ├── unit/         # Service unit tests (mock DB)
│       └── integration/  # Route integration tests (real test DB)
├── docs/
│   ├── spec.md           # Feature specification — read before building a feature
│   └── adr/              # Architecture decisions
├── TODO.md               # Phased task breakdown — primary navigation for agents
├── AGENTS.md             # OpenAI-style agent instructions
└── CLAUDE.md             # This file
```

---

## Architecture Conventions

### Backend
- **Route handlers are thin.** They validate input, call a service, return the result. No business logic in route handlers.
- **Services are pure-ish.** Services receive a DB client as a parameter (dependency injection). This makes unit testing trivial.
- **Drizzle ORM.** Schema lives in `server/src/db/schema.ts`. Migrations generated with `drizzle-kit`. Never write raw SQL in services — use Drizzle's query builder.
- **UUID primary keys everywhere.** Use `crypto.randomUUID()` — no auto-increment integers.
- **Soft deletes.** People and Tasks have a `deleted_at` column. Always filter `WHERE deleted_at IS NULL` by default.
- **Validation.** Use `zod` for request body schemas. Define schemas in `server/src/api/schemas/`. Route handlers call `schema.parse(req.body)` — zod throws on invalid input, caught by error middleware.
- **Error middleware.** Global Express error handler in `server/src/api/middleware/error.ts`. All errors flow through it. Return `{ error, code, details }`.

### Frontend
- **TanStack Query** for all server state. No manual `useEffect` for fetching.
- **shadcn/ui** for UI primitives. Don't re-implement buttons, dialogs, or inputs.
- **Collocate tests.** Unit tests live next to the component in `__tests__/` subdirectory, NOT in the top-level `tests/` folder (that's for integration).
- **No `any`.** TypeScript strict mode is on. If you're about to write `any`, stop and define the type.

---

## Workflow for Agents

1. **Read `TODO.md`** — find the current phase and next unchecked task
2. **Run tests first** — `npm test` must pass before you write a single line of new code
3. **Read `docs/spec.md`** — understand the feature requirements before implementing
4. **Red → Green** — write failing tests, then implement
5. **Check your diff** — no unrelated changes, no deleted tests
6. **Commit** with format: `feat(scope): description` or `test(scope): description`
7. **Update this file or `AGENTS.md`** if you discovered a non-obvious convention

---

## Non-Obvious Gotchas

- The test database is configured via `DATABASE_URL_TEST` env var. Never run tests against the dev database.
- Drizzle migrations are SQL files in `server/src/db/migrations/`. Don't edit them after they've been run — create a new migration instead.
- `last_contacted_at` on `people` is **not** a DB trigger. It's updated by the `PeopleService` whenever an interaction is logged.
- The `weekly_reviews` endpoint upserts by `week_start` — duplicate reviews for the same week should update, not create.
- All list endpoints enforce a max `pageSize` of 100 — do not bypass this in tests.
