# AGENTS.md — ChiefOfStaff OS

Agent instructions following the OpenAI AGENTS.md standard.

---

## Setup

```bash
# 1. Install dependencies
npm install

# 2. Copy environment config and fill in values
cp .env.example .env

# Required env vars:
#   DATABASE_URL       — PostgreSQL connection string (dev)
#   DATABASE_URL_TEST  — PostgreSQL connection string (test DB, separate!)
#   AUTH_SECRET        — JWT signing secret (min 32 chars)
#   PORT               — Server port (default 3001)

# 3. Run database migrations
npm run db:migrate

# 4. Seed development data
npm run db:seed

# 5. Start dev servers (client + server concurrently)
npm run dev
```

---

## Running Tests

```bash
# All tests
npm test

# Server unit tests only (fast, no DB needed)
npm run test:server:unit

# Server integration tests (requires test DB running)
npm run test:server:integration

# Client component tests
npm run test:client

# Watch mode
npm run test:watch
```

**TDD rule:** Write failing tests FIRST. Implementation comes after. Never skip the red phase.

---

## Code Style

### TypeScript (all files)
- Strict mode: `"strict": true` in all `tsconfig.json`
- No `any` — define proper types or use `unknown` with narrowing
- Prefer `interface` for object shapes, `type` for unions/aliases
- Use `const` by default; `let` only when reassignment is required
- Named exports only — no default exports (improves refactoring)

### Backend (Node.js / Express)
- Route handlers: validate → call service → return result. No logic beyond that.
- Services receive `db: DrizzleClient` as first parameter — enables easy mocking
- Zod schemas for all request bodies — defined in `server/src/api/schemas/`
- Use `async/await` — no `.then()` chains
- Log with a structured logger (pino) — no `console.log` in production code

### Frontend (React)
- Functional components only — no class components
- Custom hooks for any logic beyond simple state (`use` prefix)
- TanStack Query for all server state — no manual fetch in `useEffect`
- shadcn/ui components as base — extend, don't replace
- Tailwind for all styling — no inline styles, no CSS modules
- Forms use `react-hook-form` + `zod` resolver

### Naming
- Files: `kebab-case.ts` for utilities/services, `PascalCase.tsx` for React components
- Database columns: `snake_case`
- TypeScript types/interfaces: `PascalCase`
- Constants: `SCREAMING_SNAKE_CASE`

---

## PR Instructions

Every PR must include:

1. **What changed** — one-sentence summary
2. **Why** — link to TODO.md task or describe the problem
3. **Evidence** — at minimum one of:
   - Test output showing new tests passing (`npm test` screenshot or paste)
   - Screenshot for UI changes
   - `curl` output for new API endpoints
4. **How to test** — steps a reviewer can follow

**PR rules:**
- One logical change per PR
- Never merge with failing tests
- Never remove a test without a comment explaining why
- Keep PRs under 400 lines changed where possible

---

## Architecture Quick Reference

| Concern              | Where it lives                            |
|----------------------|-------------------------------------------|
| DB schema            | `server/src/db/schema.ts`                 |
| Migrations           | `server/src/db/migrations/`               |
| Request validation   | `server/src/api/schemas/*.schema.ts`      |
| Business logic       | `server/src/services/*.service.ts`        |
| Route handlers       | `server/src/api/routes/*.routes.ts`       |
| Shared types         | `server/src/types/` (server-side)         |
| React pages          | `client/src/pages/`                       |
| Reusable components  | `client/src/components/`                  |
| API client           | `client/src/lib/api.ts`                   |
| Data fetching hooks  | `client/src/hooks/use*.ts`                |

---

## What NOT to Do

- Don't write business logic in route handlers
- Don't use `any` in TypeScript
- Don't run tests against the dev database (use `DATABASE_URL_TEST`)
- Don't edit migration files that have already been applied — create a new one
- Don't add `console.log` to production code — use the structured logger
- Don't refactor unrelated code in a feature PR
- Don't bypass the soft-delete filter — never hard-delete people or tasks
