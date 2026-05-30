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

---

## 🚀 Improvement Proposals

### First-Principles Analysis

- **Personal productivity tools fail from stale data, not missing features.** The #1 reason personal CRMs die is that logging friction exceeds perceived value; every design decision should be evaluated by "how many taps/clicks to record this?"
- **The feature surface is very wide for v1.** Relationships + Goals + Tasks + Life Areas + Weekly Review is five distinct product surfaces; most successful personal tools start with one and expand — breadth at launch risks building a mediocre version of each.
- **Relationship tracking requires a different data model than task tracking.** Treating them as the same "linked entity" oversimplifies the temporal, emotional, and contextual nature of relationships.
- **Auth complexity is deferred but critical.** Clerk is listed as "or self-hosted JWT" — this decision has major implications for data privacy, GDPR compliance, and multi-device sync that should be resolved before the schema is finalized.

### Key Risks & Assumptions

- **Data entry friction will kill adoption.** Every interaction must be loggable in under 10 seconds on mobile or users will abandon the habit within two weeks.
- **Getting started is entirely `TODO`** — setup commands are placeholders, which means there is no way to run the project yet; this blocks all development velocity.
- **Privacy sensitivity:** life-area data (health, finance, relationships) is among the most sensitive personal data; a breach or accidental sync would be catastrophic for trust.
- **The weekly review flow is hard to design well** — most tools have one but users skip it; without habit-forming mechanics (streak, calendar reminder, time-box estimate), it becomes abandonware.

### Concrete Improvement Ideas

1. **Unblock the monorepo setup immediately** — replace all `TODO: npm install` placeholders with a working `package.json` workspace config; nothing else can be built until the dev environment runs. (Highest unblocking impact.)
2. **Add a quick-capture global input** — a keyboard shortcut or floating button that logs a note, task, or interaction to a universal inbox in ≤2 taps, to be triaged later; this single feature determines whether the tool gets daily use.
3. **Implement relationship health score algorithm** — calculate a score based on days-since-last-contact and interaction frequency; surface "fading relationships" in a weekly digest to create actionable nudges.
4. **Ship mobile-first layouts from day one** — since most personal logging happens on mobile (on the go, after a meeting), design the UI for 375px wide before considering desktop.
5. **Add calendar integration (read-only first)** — pull events from Google/Apple Calendar to auto-suggest people to log interactions with, reducing manual entry dramatically.

