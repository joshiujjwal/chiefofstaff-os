# ChiefOfStaff OS — Feature Specification

**Version:** 0.1 (pre-implementation)  
**Last updated:** 2025-01  
**Status:** Draft — open questions marked with ❓

---

## Overview

ChiefOfStaff OS is a private, self-hosted personal life operating system. It applies the discipline of a B2B CRM to personal life management: tracking relationships with intentionality, progressing on goals with milestones, and maintaining a unified task inbox across all life areas.

**Problem statement:** Most productivity tools are either work-centric (Linear, Jira) or too generic (Notion, Todoist). None model the richness of personal relationships and long-term life goals together. People end up with relationships living in their head, goals in a notebook, and tasks scattered across apps.

**Target user:** One person — the owner. This is a personal tool, not a team product (v0.1).

---

## Functional Requirements

### Life Areas
- [ ] User can create life areas with name, icon (emoji), and color
- [ ] Default life areas seeded on first run: Health, Finance, Career, Relationships, Family, Personal Growth, Home
- [ ] Each entity (person, goal, task) can be linked to one primary life area
- [ ] Life area dashboard shows aggregate counts and a "health score"

### Relationship CRM
- [ ] Create and edit a person record with: name, nickname, email, phone, relationship type, bio notes, life area
- [ ] Log interactions: type (call / text / coffee / email / note), date, free-text summary
- [ ] View chronological interaction history per person
- [ ] "Last contacted" field auto-updated from most recent interaction
- [ ] Follow-up reminder: configurable "check-in interval" per person (e.g. every 2 weeks); surface overdue in dashboard
- [ ] Search people by name or note keyword
- [ ] Soft delete (recoverable within 30 days)

### Goal Tracking
- [ ] Create goals with: title, description, life area, target date, status
- [ ] Status transitions: `draft → active → paused → completed | dropped`
- [ ] Add milestones to a goal (ordered checklist with optional due dates)
- [ ] Mark milestones complete; goal auto-suggests "completed" when all milestones done
- [ ] Goals list filterable by status and life area
- [ ] Link tasks to a goal

### Task Management
- [ ] Create tasks with: title, notes, due date, status, goal link (optional), person link (optional), life area
- [ ] Status: `todo → in_progress → done | cancelled`
- [ ] Task inbox: flat list sorted by due date, filterable by status / life area / goal
- [ ] Quick-add via keyboard shortcut (⌘K or floating button)
- [ ] Recurring tasks ❓ (v0.2 candidate)
- [ ] Subtasks ❓ (v0.2 candidate)

### Weekly Review
- [ ] Triggered manually (or nudged on Sunday evening)
- [ ] Step 1 — Reflect: free-text prompts ("What went well? What drained you?")
- [ ] Step 2 — Review goals: see each active goal, update milestone progress
- [ ] Step 3 — Plan tasks: review overdue + upcoming tasks, reassign or cancel
- [ ] Step 4 — Capture: free-text notes for the week
- [ ] Persist review as a `weekly_reviews` record with all responses
- [ ] Show previous review summary for continuity

### Search
- [ ] Global search across people names, goal titles, task titles, interaction summaries
- [ ] PostgreSQL full-text search (`tsvector` on relevant columns)
- [ ] Results grouped by entity type

---

## Non-Functional Requirements

- [ ] Single-user (no multi-tenancy in v0.1)
- [ ] All data stored in user-controlled PostgreSQL instance
- [ ] API response time < 200ms at p95 for list endpoints (with index)
- [ ] UI renders main views in < 1.5s on localhost
- [ ] No external analytics or tracking scripts
- [ ] All personal data exportable as JSON at any time
- [ ] Auth required for all API routes (no public endpoints)

---

## Data Model

```
life_areas
  id            UUID PK
  name          TEXT NOT NULL
  icon          TEXT (emoji)
  color         TEXT (hex)
  description   TEXT
  created_at    TIMESTAMPTZ

people
  id              UUID PK
  name            TEXT NOT NULL
  nickname        TEXT
  email           TEXT
  phone           TEXT
  relationship_type TEXT  -- friend | family | mentor | colleague | acquaintance
  life_area_id    UUID FK life_areas
  notes           TEXT
  check_in_days   INT DEFAULT NULL  -- follow-up interval
  last_contacted_at TIMESTAMPTZ
  deleted_at      TIMESTAMPTZ
  created_at      TIMESTAMPTZ
  updated_at      TIMESTAMPTZ

interactions
  id          UUID PK
  person_id   UUID FK people
  type        TEXT  -- call | text | coffee | email | note
  summary     TEXT
  occurred_at TIMESTAMPTZ
  created_at  TIMESTAMPTZ

goals
  id            UUID PK
  title         TEXT NOT NULL
  description   TEXT
  life_area_id  UUID FK life_areas
  status        TEXT  -- draft | active | paused | completed | dropped
  target_date   DATE
  created_at    TIMESTAMPTZ
  updated_at    TIMESTAMPTZ

milestones
  id            UUID PK
  goal_id       UUID FK goals
  title         TEXT NOT NULL
  sort_order    INT
  due_date      DATE
  completed_at  TIMESTAMPTZ
  created_at    TIMESTAMPTZ

tasks
  id            UUID PK
  title         TEXT NOT NULL
  notes         TEXT
  status        TEXT  -- todo | in_progress | done | cancelled
  due_date      DATE
  goal_id       UUID FK goals NULLABLE
  person_id     UUID FK people NULLABLE
  life_area_id  UUID FK life_areas NULLABLE
  deleted_at    TIMESTAMPTZ
  created_at    TIMESTAMPTZ
  updated_at    TIMESTAMPTZ

weekly_reviews
  id            UUID PK
  week_start    DATE NOT NULL
  reflect_text  TEXT
  plan_text     TEXT
  notes_text    TEXT
  created_at    TIMESTAMPTZ
  updated_at    TIMESTAMPTZ
```

---

## API Interface Design

All endpoints prefixed with `/api/v1`. JSON in/out. Auth via Bearer token header.

### Conventions
- List endpoints return `{ data: [...], total, page, pageSize }`
- Error responses: `{ error: string, code: string, details?: object }`
- Soft-deleted records excluded by default; include via `?includeDeleted=true`

### Key Endpoints

```
GET    /health                              # No auth required

GET    /api/v1/life-areas
POST   /api/v1/life-areas
PATCH  /api/v1/life-areas/:id

GET    /api/v1/people?page&pageSize&lifeAreaId&q
POST   /api/v1/people
GET    /api/v1/people/:id
PATCH  /api/v1/people/:id
DELETE /api/v1/people/:id
GET    /api/v1/people/:id/interactions
POST   /api/v1/people/:id/interactions

GET    /api/v1/goals?status&lifeAreaId
POST   /api/v1/goals
PATCH  /api/v1/goals/:id
POST   /api/v1/goals/:id/milestones
PATCH  /api/v1/milestones/:id

GET    /api/v1/tasks?status&lifeAreaId&goalId&personId&dueBy
POST   /api/v1/tasks
PATCH  /api/v1/tasks/:id
DELETE /api/v1/tasks/:id

GET    /api/v1/weekly-reviews
POST   /api/v1/weekly-reviews
GET    /api/v1/weekly-reviews/:id
PATCH  /api/v1/weekly-reviews/:id

GET    /api/v1/search?q
```

---

## Test Plan

### Unit Tests (Vitest, mock DB)
- PeopleService: create, update, soft-delete, get with interactions
- GoalsService: status transition validation (e.g. can't go completed → active)
- TasksService: create with optional links, filter logic
- LifeAreaService: aggregate stats computation

### Integration Tests (Supertest, test PostgreSQL)
- All CRUD routes return correct status codes and shapes
- Validation: missing required fields → 400 with descriptive message
- Auth: unauthenticated requests → 401
- Pagination: large seed → verify page/pageSize respected
- Soft delete: deleted record excluded from list, recoverable

### Edge Cases
- Person with no interactions — `last_contacted_at` is null, no crash
- Goal with no milestones — mark complete manually
- Task with no due date — sorts to end of inbox
- Weekly review created twice for same week — second upserts, no duplicate
- Full-text search with special characters (apostrophes, emoji)

---

## Open Questions

❓ Auth strategy: Clerk (hosted, easy) vs self-hosted JWT (full privacy)? Default to JWT for v0.1 since this is single-user and privacy-focused.

❓ Life area "health score" formula — simple weighted avg of (goal progress % + tasks completed this week + interactions logged)?

❓ Recurring tasks — defer to v0.2 or include from day one?

❓ Mobile — PWA with responsive design sufficient for v0.1, or native app needed?

❓ File attachments on interactions/tasks (photos, voice memos)?
