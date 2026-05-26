# GoalCast AI — Technical Plan
> Portfolio project + real product · World Cup 2026

---

## Why This Project Exists (dual purpose)

1. **Real product** — generate revenue during World Cup 2026
2. **Portfolio** — demonstrate full stack + AI skills for job applications

Stack gaps this project covers vs existing portfolio:
- NestJS in production (vs side projects only)
- WebSockets for real-time features
- GraphQL API layer
- JWT + RBAC auth with multiple roles
- Microservices communicating via Kafka
- AWS ECS deployment with multiple services

---

## Architecture

### Services

**1. Core API (NestJS + TypeScript)**
- Auth — JWT + RBAC (fan, venue_manager, admin)
- Matches — fixture data, predictions, scoring
- Venues — registration, partnership management
- Leaderboard — rankings, points calculation
- WebSocket gateway — real-time updates during matches

**2. AI Advisor (Python + FastAPI)**
- Pre-match analysis — team form, historical data, win probability
- LLM layer — narrative analysis via OpenAI/Anthropic
- Evaluation framework — track prediction accuracy over time

**3. Trivia Service (NestJS)**
- Question bank per match
- Real-time question delivery via WebSockets
- Score calculation and leaderboard updates

### Data Flow

```
Fan App
  ↓
NestJS Core API (REST + WebSocket)
  ↓
PostgreSQL (predictions, users, venues, scores)
Redis (leaderboard cache, session, real-time state)
  ↓
Kafka (match events → AI Advisor)
  ↓
Python AI Service (analysis, LLM narrative)
```

### External Data Sources
- Football-data.org API (free tier) — fixture data, team stats, live scores
- OpenAI API — narrative match analysis
- Firebase Cloud Messaging — push notifications before each match

---

## Database Schema (core entities)

```
users (id, email, name, team_favorite, venue_id, points_total, created_at)
venues (id, name, address, contact_email, is_active, prize_description)
matches (id, home_team, away_team, date, status, home_score, away_score)
predictions (id, user_id, match_id, predicted_home, predicted_away, winner_pick, points_earned)
trivia_questions (id, match_id, question, options, correct_answer, points)
trivia_answers (id, user_id, question_id, answer, is_correct, response_time_ms)
```

---

## Scoring System

| Action | Points |
|--------|--------|
| Correct winner prediction | +3 |
| Exact score prediction | +5 |
| Trivia correct answer | +2-3 (varies) |
| Fast trivia answer (top 10%) | +1 bonus |

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend API | NestJS + TypeScript |
| AI Service | Python + FastAPI |
| Database | PostgreSQL |
| Cache | Redis |
| Real-time | WebSockets (NestJS gateway) |
| Messaging | Apache Kafka |
| Auth | JWT + Passport.js |
| LLM | OpenAI API / Anthropic |
| Deployment | AWS ECS (Docker containers) |
| CI/CD | GitHub Actions |
| Frontend (fan) | React + TypeScript |
| Frontend (venue) | React + TypeScript (simple dashboard) |

---

## MVP Scope — What to build before Jun 15

**Must have:**
- [ ] User registration + login (Google OAuth for low friction)
- [ ] Venue list and selection
- [ ] Fixture data (all 104 matches)
- [ ] Prediction submission per match
- [ ] Points calculation after match result
- [ ] Leaderboard (global + per venue)
- [ ] Basic AI analysis per match (win probability)

**Nice to have (add during tournament):**
- [ ] Live trivia via WebSockets
- [ ] Push notifications before each match
- [ ] Venue dashboard (fan count per match)
- [ ] Paid tier with Stripe

**Post-tournament:**
- [ ] Full AI eval framework
- [ ] Detailed venue analytics
- [ ] Year-round sports coverage

---

## CLAUDE.md Conventions (for Claude Code)

- Conventional Commits format
- Never add Co-Authored-By trailers
- TypeScript strict mode
- Async-first patterns
- DTOs for all data contracts
- One logical change per commit
- Branch naming: type/short-description

---

## Repo Structure (planned)

```
goalcast-ai/
├── apps/
│   ├── api/          # NestJS core API
│   ├── ai/           # Python FastAPI AI service
│   └── web/          # React fan app
├── libs/
│   └── shared/       # Shared types and DTOs
├── docker-compose.yml
├── CLAUDE.md
└── README.md
```

---

## External APIs

| API | Purpose | Cost |
|-----|---------|------|
| football-data.org | Fixture data, results, team stats | Free (10 req/min) |
| OpenAI API | Match narrative analysis | ~$0.01-0.05 per match |
| Firebase FCM | Push notifications | Free |
| Stripe | Paid tier payments | 2.9% + $0.30 per transaction |

---

## Deployment Plan

**Development:** Docker Compose locally
**Staging:** Single EC2 instance (free tier)
**Production:** AWS ECS with 3 services, RDS PostgreSQL, ElastiCache Redis

Estimated AWS cost during tournament: ~$50-80/month

---

*Last updated: May 2026*
