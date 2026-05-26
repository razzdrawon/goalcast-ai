# CLAUDE.md — GoalCast AI

This file provides guidance to Claude Code when working with this repository.

---

## Project Overview

GoalCast AI is an AI-powered World Cup 2026 prediction platform. Fans predict match results, compete on local city leaderboards, and play live trivia during watch parties at partnered venues (bars and restaurants).

**Live site:** goalcastai.com
**Demo:** goalcastai.com/demo
**Docs:** See /docs folder for full business and technical plans

---

## Current State

### What exists today
- `landing/index.html` — live landing page at goalcastai.com (connected to Vercel via GitHub)
- `landing/demo.html` — interactive app mockup showing fan experience
- `landing/vercel.json` — Vercel config with cleanUrls enabled
- `docs/GOALCAST_BUSINESS.md` — full business plan, go-to-market, revenue model
- `docs/GOALCAST_TECHNICAL.md` — architecture, stack decisions, MVP scope
- `docs/GOALCAST_SALES.md` — sales scripts, distributor contacts, email templates

### What does NOT exist yet
- No backend (NestJS API not started)
- No database
- No frontend app (React not started)
- No AI service (Python FastAPI not started)
- Forms use Formspree temporarily (mpqnykkd for fans, meedrvvo for venues)

---

## Monorepo Structure

```
goalcast-ai/
├── apps/
│   ├── api/          # NestJS backend (NOT STARTED)
│   ├── ai/           # Python FastAPI AI service (NOT STARTED)
│   ├── web/          # React fan app (NOT STARTED)
│   └── venue/        # React venue dashboard (NOT STARTED)
├── libs/
│   └── shared/       # Shared TypeScript types and DTOs (NOT STARTED)
├── docs/
│   ├── GOALCAST_BUSINESS.md
│   ├── GOALCAST_TECHNICAL.md
│   └── GOALCAST_SALES.md
├── landing/
│   ├── index.html    # goalcastai.com
│   ├── demo.html     # goalcastai.com/demo
│   └── vercel.json
├── CLAUDE.md
└── README.md
```

---

## Tech Stack

| Layer | Technology | Status |
|-------|-----------|--------|
| Landing page | HTML + CSS + JS | ✅ Live |
| Backend API | NestJS + TypeScript | ⏳ Not started |
| AI Service | Python + FastAPI + LangGraph | ⏳ Not started |
| Fan App | React + TypeScript | ⏳ Not started |
| Venue Dashboard | React + TypeScript | ⏳ Not started |
| Database | PostgreSQL | ⏳ Not started |
| Cache | Redis | ⏳ Not started |
| Messaging | Apache Kafka | ⏳ Not started |
| Auth | JWT + RBAC | ⏳ Not started |
| Payments | Stripe | ⏳ Not started |
| Deployment | AWS ECS + Docker | ⏳ Not started |
| Forms (temp) | Formspree | ✅ Active |

---

## Business Context

### Revenue Model
- **Fans (B2C):** Free tier + $9.99 paid tier for full tournament AI analysis
- **Venues (B2B):** Free to list, sponsor end-of-tournament prize (dinner for two ~$80)
- **Future:** Brand sponsorships (Heineken, Modelo), featured venue placement fees

### Target Markets
- Atlanta, GA — 8 World Cup matches including Semifinal (July 15)
- Mexico City — large soccer market, World Cup host country
- Expanding to other host cities as venues are confirmed

### Current Pipeline
- Actively reaching out to bars/restaurants in Atlanta and CDMX
- Reaching out to beer distributors (General Wholesale, Empire Distributors in Atlanta)
- Forms connected to Formspree (50 submission free limit — monitor)

### Key Dates
- Jun 11 — World Cup starts
- Jun 15 — First Atlanta match: Spain vs Cape Verde
- Jul 15 — Atlanta Semifinal
- Jul 19 — World Cup Final

---

## MVP Priority (build in this order)

1. **NestJS API foundation** — auth, user registration, venue registration
2. **Fixture data integration** — football-data.org API for all 104 matches
3. **Predictions system** — submit prediction per match, calculate points after result
4. **Leaderboard** — global + per city + per venue
5. **Stripe integration** — paid tier $9.99/tournament
6. **AI match analysis** — pre-match win probability and narrative via OpenAI
7. **WebSocket gateway** — real-time leaderboard updates
8. **Live trivia** — questions during matches, real-time scoring
9. **Venue dashboard** — fan count reports before each match
10. **Push notifications** — Firebase FCM before each match

---

## Commit Conventions

Always use Conventional Commits format: type: short description in present tense, lowercase

Types:
- feat: new feature or capability
- fix: bug fix
- test: adding or updating tests
- refactor: code change that does not add features or fix bugs
- docs: README or documentation only
- chore: tooling, deps, config (no production code)

Examples:
- feat: add user registration endpoint with JWT auth
- feat: integrate football-data.org fixture API
- fix: handle Formspree submission timeout gracefully
- chore: add docker-compose with postgres and redis

Rules:
- One logical change per commit
- Never commit directly to main — use feature branches
- Branch naming: type/short-description
- Never add Co-Authored-By trailers to commits
- Never add Claude or Anthropic attribution in commit messages

---

## Code Style

- TypeScript strict mode everywhere
- Async-first — prefer async/await
- Pydantic models in Python, DTOs in NestJS — no raw dicts across boundaries
- Explicit error handling — no bare except or empty catch
- All functions typed — no implicit any

---

## Architecture Principles

- Services communicate via Kafka for async operations
- REST for synchronous client-facing APIs
- WebSockets for real-time features (leaderboard, trivia)
- No cross-module imports in NestJS except through defined interfaces
- Python AI service is independent — communicates via Kafka and REST only

---

## External Services & Keys

- **Formspree fans:** mpqnykkd (50 submissions/month free limit)
- **Formspree venues:** meedrvvo (50 submissions/month free limit)
- **football-data.org:** free tier, 10 req/min — need API key in .env
- **OpenAI API:** needed for AI analysis — key in .env
- **Stripe:** needed for paid tier — key in .env
- **Firebase FCM:** needed for push notifications — key in .env

Never commit API keys. Always use .env files and add to .gitignore.

---

## Testing Expectations

- Every new module gets tests alongside the code
- NestJS: Jest + Supertest for API integration tests
- Python: pytest + pytest-asyncio
- No live API calls in tests — mock external services
- Guardrail logic and scoring algorithms must have unit tests

---

## What Claude Should Help With

- Suggest commit messages following Conventional Commits
- Flag if a change is doing too many things (should be split)
- Default to async patterns and typed interfaces
- Remind to write tests alongside implementation
- Keep landing page changes minimal and targeted — it is live in production
- Never modify Formspree IDs without explicit instruction
