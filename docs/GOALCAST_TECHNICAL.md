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
users (id, email, name, team_favorite, city, venue_id, points_total, created_at)
venues (id, name, address, city, contact_email, is_active)
matches (id, home_team, away_team, date, status, home_score, away_score, phase)
predictions (id, user_id, match_id, predicted_home, predicted_away, winner_pick, points_earned)
prize_dynamics (id, venue_id, type: match|weekly|phase|tournament, scope_ref, winner_user_id, announced_at)
trivia_questions (id, match_id, question, options, correct_answer, points)
trivia_answers (id, user_id, question_id, answer, is_correct, response_time_ms)
```

### prize_dynamics notes
- `type` defines the cadence the venue activated
- `scope_ref` is the ID of the match, ISO week number, phase name, or null for tournament
- `winner_user_id` is set when GoalCast calculates the winner after the scope ends
- `announced_at` is when the push notification was sent to the winner
- No prize description stored — venue handles fulfillment offline

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
- [ ] User profile includes city (free text, normalized via Nominatim on the frontend)
- [ ] Venue list and selection
- [ ] Fixture data (all 104 matches)
- [ ] Prediction submission per match
- [ ] Points calculation after match result
- [ ] Leaderboard (global + per city + per venue)
- [ ] Prize dynamics — calculate winner per activated dynamic, send push notification
- [ ] Basic AI analysis per match (win probability)

**Nice to have (add during tournament):**
- [ ] Live trivia via WebSockets
- [ ] Push notifications before each match
- [ ] Venue dashboard (fan count per match, prize dynamic winners)
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

## Venue Prize Configuration

Each venue customizes their own prize and eligibility rules. No platform-imposed rules.

### Configurable fields (Venue Dashboard)
- **Prize description** — free text (e.g. "Dinner for two", "Free round of drinks")
- **Minimum check-ins required** — number set by venue (suggested: 3-5)
- **Prize deadline** — date (default: July 19, World Cup Final)

### Check-in mechanic
- Venue generates a unique 4-digit code per match via the dashboard
- Code is valid from kickoff until kickoff + 30 minutes
- Fan enters code in app → receives +3 bonus points + check-in counted
- Fan must reach venue's minimum check-in threshold to be eligible for prize

### Data tracked per venue
- Total check-ins per match
- Unique fans checked in
- Fans who have reached eligibility threshold
- Pre-match report: fans who have GoalCast and selected this venue

### Why customizable
- Different venues have different margins and prize budgets
- Gives venue ownership over their program
- Makes the pitch easier — "you set the rules"

---

*Last updated: May 2026*
