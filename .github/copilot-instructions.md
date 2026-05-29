# GitHub Copilot Instructions — ChiefOfStaff OS

## Project Context

ChiefOfStaff OS is a personal life operating system built with:
- **Backend:** Node.js + Express + TypeScript + Drizzle ORM + PostgreSQL
- **Frontend:** React 18 + TypeScript + Vite + Tailwind CSS + shadcn/ui
- **Testing:** Vitest (unit) + Supertest (API integration) + Testing Library (components)
- **Auth:** JWT-based (single user, self-hosted)

This is a single-user private app — no multi-tenancy, no public endpoints.

---

## Coding Conventions

### TypeScript
- Strict mode always on — no `any`, no `@ts-ignore` without an explanation comment
- Named exports only — no `export default`
- Use `interface` for object shapes; `type` for unions, aliases, mapped types
- Prefer `unknown` + type narrowing over `any`

### Backend
- Route handlers call `zod` schema `.parse()` on `req.body` — never trust raw input
- All services accept `db: DrizzleClient` as a parameter — this is how we test them
- UUIDs from `crypto.randomUUID()` — no integer PKs
- Soft deletes: `deleted_at` column — never hard-delete people or tasks
- Structured logging with `pino` — no `console.log` in server code

### Frontend
- TanStack Query for all data fetching — no `useEffect` fetch calls
- shadcn/ui for all primitive components (Button, Dialog, Input, etc.)
- Tailwind for all styling — no inline styles
- `react-hook-form` + `zod` for all forms
- Component files: `PascalCase.tsx`; utility/hook files: `kebab-case.ts` or `useCamelCase.ts`

### Database
- Schema defined in `server/src/db/schema.ts` using Drizzle's schema builder
- Migrations in `server/src/db/migrations/` — never edit applied migrations
- All tables have `created_at TIMESTAMPTZ` defaulting to `now()`
- Use UUIDs for all primary keys

---

## Testing Conventions

- **Write tests before implementation.** Red → Green is mandatory.
- Unit tests mock the DB client — services should be testable without a live DB
- Integration tests use a real test database (`DATABASE_URL_TEST` env var)
- Test file naming: `*.test.ts` (server) or `*.test.tsx` (React components)
- Test descriptions: `describe('ServiceName', () => { it('should do X when Y', ...) })`
- Every new API endpoint needs: happy path + validation error + auth error tests

---

## Boundaries

- **Don't refactor code unless the task explicitly asks for it**
- **Don't remove or skip tests** — if a test is wrong, fix it or ask
- **Don't add dependencies** without checking if an existing dep covers the use case
- **Don't add `console.log`** to production code paths
- **Don't bypass auth middleware** on any new routes
- **Don't use `any`** — define the type properly
- **Don't write raw SQL** — use Drizzle's query builder
- **Don't hard-code secrets** — all config from environment variables
