# ChiefOfStaff OS

> 🧠 Personal life operating system — tracks relationships, goals, tasks, and life areas like a CRM but for your personal life.

![Status](https://img.shields.io/badge/status-🚧%20Early%20Development-orange)
![Stack](https://img.shields.io/badge/stack-TypeScript%20%7C%20React%20%7C%20Node.js%20%7C%20PostgreSQL-blue)

---

## What It Does

ChiefOfStaff OS gives you a unified command center for your personal life:

- **Relationship CRM** — Track people, interactions, follow-up reminders, relationship strength
- **Goal Tracking** — Define goals with milestones, link them to life areas, track progress
- **Task Management** — Tasks linked to goals, people, or life areas with context
- **Life Areas** — Configurable domains (Health, Finance, Career, Family, etc.) with dashboards
- **Weekly Review** — Structured reflection and planning flow
- **Timeline / Activity Feed** — Full history of what you logged across all areas

---

## Tech Stack

| Layer       | Technology                          |
|-------------|-------------------------------------|
| Frontend    | React 18 + TypeScript + Vite        |
| UI Library  | shadcn/ui + Tailwind CSS            |
| Backend     | Node.js + Express + TypeScript      |
| Database    | PostgreSQL + Drizzle ORM            |
| Auth        | Clerk (or self-hosted JWT)          |
| Testing     | Vitest (unit) + Supertest (API)     |
| Deployment  | Docker + fly.io / Railway           |

---

## Getting Started

```bash
# Clone
git clone https://github.com/joshiujjwal/chiefofstaff-os.git
cd chiefofstaff-os

# Install dependencies
TODO: npm install (monorepo setup TBD)

# Copy environment config
cp .env.example .env
# Fill in: DATABASE_URL, AUTH_SECRET, PORT

# Run database migrations
TODO: npm run db:migrate

# Start development servers
TODO: npm run dev

# Run tests
TODO: npm test
```

---

## Project Structure

```
chiefofstaff-os/
├── src/
│   ├── api/          # Express route handlers
│   ├── db/           # Drizzle schema + migrations
│   ├── services/     # Business logic (no Express dependency)
│   └── types/        # Shared TypeScript types/interfaces
├── tests/
│   ├── unit/         # Service-layer unit tests (Vitest)
│   └── integration/  # API integration tests (Supertest)
├── docs/
│   ├── spec.md       # Feature specification
│   └── adr/          # Architecture Decision Records
├── .github/
│   ├── copilot-instructions.md
│   ├── instructions/
│   └── skills/
├── README.md
├── TODO.md
├── CLAUDE.md
└── AGENTS.md
```

---

## Contributing

1. Read `TODO.md` to find the current phase and next task
2. Run existing tests first — never break what's green
3. Write failing tests before implementation (red → green)
4. Keep PRs small and focused on one task
5. Include evidence in PR description: test output, screenshot, or log snippet
6. Update `CLAUDE.md` or `AGENTS.md` if you learn a new convention

> **No unreviewed code ships.** Every PR needs a human diff review before merge.
