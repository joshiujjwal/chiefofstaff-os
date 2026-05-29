# ChiefOfStaff OS — Task Breakdown

## How to Use This File

**Every task follows this loop:**
1. Read the task description fully
2. Run existing tests: `npm test` — all must pass before you start
3. Write failing tests first (red phase)
4. Implement until tests pass (green phase)
5. Review your diff manually — no accidental deletes
6. Commit with a descriptive message
7. Update `CLAUDE.md` / `AGENTS.md` with anything surprising you learned

**Evidence gates:** Each phase requires passing tests + human sign-off before Phase N+1 begins.

---

## Phase 0: Foundation ⬜

- [ ] Init monorepo structure (root `package.json` with workspaces: `client/`, `server/`)
- [ ] Configure TypeScript (`tsconfig.json`) for both client and server
- [ ] Set up ESLint + Prettier with shared config (`@typescript-eslint`, strict mode)
- [ ] Install and configure Vitest for server unit tests
- [ ] Write first smoke test: `GET /health` returns `{ status: "ok" }` (red → green)
- [ ] Set up Drizzle ORM + PostgreSQL connection (env-configured, test DB separate)
- [ ] Create `.env.example` with all required vars documented
- [ ] GitHub Actions CI: lint + test on every push to `main` and PRs
- [ ] Docker Compose for local dev: PostgreSQL + server + client
- [ ] **GATE: All smoke tests green, CI passing, human review of config files**

---

## Phase 1: Data Model & Core API ⬜

### 1a. Database Schema
- [ ] `life_areas` table: `id, name, icon, color, description, created_at`
- [ ] `people` table: `id, name, email, phone, relationship_type, life_area_id, notes, last_contacted_at, created_at, updated_at`
- [ ] `interactions` table: `id, person_id, type (call/text/meeting/note), summary, occurred_at, created_at`
- [ ] `goals` table: `id, title, description, life_area_id, status (active/paused/completed/dropped), target_date, created_at, updated_at`
- [ ] `milestones` table: `id, goal_id, title, completed_at, due_date`
- [ ] `tasks` table: `id, title, notes, status (todo/in_progress/done/cancelled), due_date, goal_id (nullable), person_id (nullable), life_area_id (nullable), created_at, updated_at`
- [ ] `tags` table + `taggable` polymorphic join table
- [ ] Write and run initial migration
- [ ] Seed script with sample data (3 life areas, 5 people, 3 goals, 10 tasks)
- [ ] **GATE: `npm run db:migrate` + `npm run db:seed` succeed cleanly**

### 1b. People API
- [ ] `GET /api/people` — list with pagination + filter by life_area
- [ ] `POST /api/people` — create with validation (name required)
- [ ] `GET /api/people/:id` — detail with recent interactions
- [ ] `PATCH /api/people/:id` — partial update
- [ ] `DELETE /api/people/:id` — soft delete (set `deleted_at`)
- [ ] `POST /api/people/:id/interactions` — log an interaction
- [ ] `GET /api/people/:id/interactions` — interaction history
- [ ] Unit tests for PeopleService (mock DB)
- [ ] Integration tests for all People routes (real test DB)
- [ ] **GATE: All People tests green**

### 1c. Goals & Tasks API
- [ ] `GET /api/goals` — list with status filter
- [ ] `POST /api/goals` — create
- [ ] `PATCH /api/goals/:id` — update (including status transitions)
- [ ] `POST /api/goals/:id/milestones` — add milestone
- [ ] `PATCH /api/milestones/:id` — complete or update
- [ ] `GET /api/tasks` — list with filters (status, due_date, goal_id, person_id)
- [ ] `POST /api/tasks` — create
- [ ] `PATCH /api/tasks/:id` — update / complete
- [ ] `DELETE /api/tasks/:id` — soft delete
- [ ] Unit + integration tests for Goals and Tasks
- [ ] **GATE: All Goals/Tasks tests green**

### 1d. Life Areas API
- [ ] `GET /api/life-areas` — list all with summary stats (people count, active goals count, open tasks count)
- [ ] `POST /api/life-areas` — create
- [ ] `PATCH /api/life-areas/:id` — update
- [ ] Unit + integration tests
- [ ] **GATE: All LifeAreas tests green**

---

## Phase 2: React Frontend ⬜

### 2a. App Shell
- [ ] Vite + React 18 + TypeScript setup in `client/`
- [ ] Tailwind CSS + shadcn/ui installation and configuration
- [ ] React Router v6 with layout routes
- [ ] Global nav sidebar: Life Areas, People, Goals, Tasks, Weekly Review
- [ ] API client (typed fetch wrapper or TanStack Query setup)
- [ ] Auth gate (redirect to login if no session)

### 2b. People Views
- [ ] People list page with search + filter by life area
- [ ] Person detail page: bio, interaction timeline, linked tasks/goals
- [ ] Add/edit person form with validation
- [ ] Log interaction modal (type + summary)
- [ ] "Follow up soon" indicator (last contacted > threshold)

### 2c. Goals & Tasks Views
- [ ] Goals list with status tabs (Active / Paused / Completed)
- [ ] Goal detail page: description, milestone checklist, linked tasks
- [ ] Task inbox: grouped by due date, filterable
- [ ] Quick-add task from anywhere (command palette or floating button)
- [ ] Drag-to-reorder tasks within a goal

### 2d. Life Area Dashboard
- [ ] Dashboard cards per life area (people, goals, open tasks)
- [ ] Clickable drill-down to filtered views
- [ ] Life area health score (formula TBD in spec.md)

### 2e. Weekly Review Flow
- [ ] Multi-step review wizard: Reflect → Review goals → Plan tasks → Capture notes
- [ ] Persist review session as a `weekly_reviews` record
- [ ] Show last week's review for comparison

- [ ] **GATE: All frontend component smoke tests pass (Vitest + Testing Library), manual walkthrough recorded**

---

## Phase 3: Polish & Harden ⬜

- [ ] Full-text search across people, goals, tasks (PostgreSQL `tsvector`)
- [ ] Reminder / follow-up notifications (email or in-app)
- [ ] Data export: JSON + CSV dump of all personal data
- [ ] Rate limiting on API (express-rate-limit)
- [ ] Input sanitization audit (all user strings pass through validator.js)
- [ ] Accessibility audit (axe-core, keyboard nav for all modals)
- [ ] Error boundary in React with graceful fallback
- [ ] API error response standardization (`{ error, code, details }`)
- [ ] Performance: pagination enforced on all list endpoints (max 100)
- [ ] **GATE: Zero critical/high axe violations, all edge-case tests green**

---

## Phase 4: Ship ⬜

- [ ] Write `Dockerfile` for server (multi-stage, Alpine base)
- [ ] Write `Dockerfile` for client (Nginx serving static build)
- [ ] `docker-compose.prod.yml` with health checks
- [ ] Deploy to fly.io or Railway (document steps in `docs/deploy.md`)
- [ ] Set up production database with automated backups
- [ ] Environment variable audit (no secrets in image)
- [ ] Smoke test production URL after deploy
- [ ] Tag `v0.1.0` release
- [ ] **GATE: Production URL responding, human sign-off**

---

## Parking Lot 🅿️

- Mobile app (React Native or PWA)
- AI assistant: "Who should I reconnect with this week?"
- Natural language task entry ("Call mom tomorrow")
- Zapier / n8n integrations (Google Contacts, Calendar sync)
- Multi-user / family sharing
- Obsidian plugin for linked notes

---

## Lessons Learned 📝

_Fill this in as you build. Format: date — what you learned — why it matters._

- 
