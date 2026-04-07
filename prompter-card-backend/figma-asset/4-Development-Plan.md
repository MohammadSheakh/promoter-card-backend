# Backend Development Plan — PromoterCard

**Project:** PromoterCard - NFC-Powered Event Promotion Platform  
**Tech Stack:** Node.js + TypeScript + MongoDB + Mongoose + Redis + BullMQ  
**Version:** 1.0 (Single-Tenant, Production-Grade)  
**Created:** April 7, 2026  
**Based On:** PRD v3 + Master System Prompt + Module Creation Guide  

---

## Table of Contents

1. [Development Philosophy](#1-development-philosophy)
2. [Scale Targets](#2-scale-targets)
3. [Tech Stack & Architecture](#3-tech-stack--architecture)
4. [Folder Structure](#4-folder-structure)
5. [Development Phases](#5-development-phases)
6. [Module Implementation Order](#6-module-implementation-order)
7. [Database Strategy](#7-database-strategy)
8. [Caching Strategy](#8-caching-strategy)
9. [Queue Strategy](#9-queue-strategy)
10. [API Development Sequence](#10-api-development-sequence)
11. [Testing Strategy](#11-testing-strategy)
12. [Security Implementation](#12-security-implementation)
13. [Observability & Monitoring](#13-observability--monitoring)
14. [Deployment Architecture](#14-deployment-architecture)
15. [Team Structure](#15-team-structure)
16. [Risk Mitigation](#16-risk-mitigation)
17. [Code Quality Standards](#17-code-quality-standards)
18. [Documentation Standards](#18-documentation-standards)
19. [Sprint Breakdown](#19-sprint-breakdown)
20. [Definition of Done](#20-definition-of-done)

---

## 1. Development Philosophy

### 1.1 Core Principles

**Senior-Level Production Standards:**
- ✅ Every decision justified by performance, maintainability, or security
- ✅ No junior-level code patterns
- ✅ Prefer simpler approaches that meet all constraints
- ✅ Document why complex approaches are necessary
- ✅ Generic controllers and services (DRY principle)
- ✅ SOLID principles enforced
- ✅ Horizontal scaling from day one

### 1.2 Patterns from Documentation

**Reused Patterns (from MODULE_CREATION_GUIDE, REDIS_CACHING_GUIDE, SENIOR_PATTERNS_GUIDE):**
- Generic controller pattern (getAllWithPaginationV2, getAllWithAggregation)
- Generic service pattern
- Modular architecture (`src/modules/<module-name>.module/`)
- File structure: `.constant.ts`, `.interface.ts`, `.model.ts`, `.validation.ts`, `.service.ts`, `.controller.ts`, `.route.ts`, `.test.ts`
- BullMQ for all heavy operations (> 500ms)
- Redis cache-aside pattern with TTL by data type
- Zod validation (100% coverage)
- Rate limiting tiers (auth, strict, user, api)
- Structured JSON logging (Winston)
- Health check endpoint
- Comprehensive documentation per module (`/doc` folder with diagrams)

---

## 2. Scale Targets

### 2.1 Non-Negotiable Targets

```
Concurrent Users       : 50,000+ (promoters + admins + customers)
Total Interactions     : 100,000,000+ (NFC taps + QR scans)
Total Participants     : 10,000,000+
Total Landing Pages    : 500,000+
API Response Time      : < 200ms (reads) | < 500ms (writes)
Heavy Operations       : Immediate 202 Accepted → BullMQ job
Uptime Target          : 99.9%
Peak Game Plays        : 10,000/minute (event spikes)
Peak Signups           : 5,000/minute (event spikes)
```

### 2.2 Performance Budgets

| Operation | Target | Timeout |
|-----------|--------|---------|
| Landing page load | < 100ms | 500ms |
| Dashboard metrics | < 200ms | 1000ms |
| Game interaction | < 150ms | 500ms |
| Sign-up submission | < 300ms | 1000ms |
| Analytics query | < 500ms | 2000ms |
| Report generation | Async (202) | N/A |
| Export generation | Async (202) | N/A |

---

## 3. Tech Stack & Architecture

### 3.1 Core Stack

```yaml
Runtime: Node.js v20.x (LTS)
Language: TypeScript v5.x (strict mode)
Framework: Express.js v4.x
Database: MongoDB v7.x
ODM: Mongoose v8.x
Cache: Redis v7.x
Queue: BullMQ v5.x
Validation: Zod v3.x
Auth: JWT (jsonwebtoken v9.x) + bcrypt v5.x
Security: Helmet.js, CORS whitelist, express-rate-limit
Logging: Winston v3.x (structured JSON)
Testing: Jest v29.x + Supertest v6.x
Documentation: Swagger/OpenAPI 3.0
Process Manager: PM2
Container: Docker + docker-compose
```

### 3.2 Middleware Stack

```yaml
Request Processing:
  - cors (whitelist only)
  - helmet (security headers)
  - compression (gzip/brotli)
  - express.json({ limit: '10mb' })
  - express.urlencoded({ extended: true })

Authentication:
  - authenticate middleware (JWT validation)
  - authorize middleware (role-based access)

Validation:
  - validateRequest middleware (Zod schemas)
  - sanitizeInput middleware (NoSQL injection prevention)

Rate Limiting:
  - rateLimiter middleware (sliding window, Redis-backed)
  - Tiers: auth (5/15min), strict (10/1hr), user (30/1min), api (100/1min)

Observability:
  - requestLogger middleware (correlationId, responseTime)
  - errorHandler middleware (structured error responses)
```

### 3.3 Architecture Pattern

```
┌─────────────────────────────────────────────────────────────┐
│                    CDN / Edge Layer                          │
│           (Cloudflare - Static Assets, Landing Pages)        │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                 Load Balancer / API Gateway                  │
│              (SSL Termination, Rate Limiting)                │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
    ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
    │   Express    │  │   Express    │  │   Express    │
    │   Instance   │  │   Instance   │  │   Instance   │
    │   (Stateless)│  │   (Stateless)│  │   (Stateless)│
    └──────────────┘  └──────────────┘  └──────────────┘
              │               │               │
              └───────────────┼───────────────┘
                              ▼
              ┌───────────────────────────────┐
              │        Redis Cluster           │
              │  (Cache + Session + BullMQ)    │
              └───────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
    ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
    │  MongoDB     │  │  MongoDB     │  │  MongoDB     │
    │  Primary     │  │  Secondary   │  │  Analytics   │
    │  (Writes)    │  │  (Reads)     │  │  Replica     │
    └──────────────┘  └──────────────┘  └──────────────┘
```

---

## 4. Folder Structure

### 4.1 Project Structure

```
prompter-card-backend/
├── src/
│   ├── config/
│   │   ├── database.ts
│   │   ├── redis.ts
│   │   ├── bullmq.ts
│   │   ├── environment.ts
│   │   └── constants.ts
│   │
│   ├── modules/
│   │   ├── auth.module/
│   │   │   ├── auth/
│   │   │   │   ├── auth.constant.ts
│   │   │   │   ├── auth.interface.ts
│   │   │   │   ├── auth.model.ts
│   │   │   │   ├── auth.validation.ts
│   │   │   │   ├── auth.service.ts
│   │   │   │   ├── auth.controller.ts
│   │   │   │   ├── auth.route.ts
│   │   │   │   ├── auth.middleware.ts
│   │   │   │   └── auth.test.ts
│   │   │   └── doc/
│   │   │       ├── README.md
│   │   │       ├── dia/
│   │   │       │   ├── auth-schema.mermaid
│   │   │       │   ├── auth-system-flow.mermaid
│   │   │       │   ├── auth-swimlane.mermaid
│   │   │       │   ├── auth-user-flow.mermaid
│   │   │       │   ├── auth-system-architecture.mermaid
│   │   │       │   ├── auth-state-machine.mermaid
│   │   │       │   ├── auth-sequence.mermaid
│   │   │       │   └── auth-component-architecture.mermaid
│   │   │       └── perf/
│   │   │           └── auth-performance-report.md
│   │   │
│   │   ├── user.module/
│   │   │   ├── user/
│   │   │   │   ├── user.constant.ts
│   │   │   │   ├── user.interface.ts
│   │   │   │   ├── user.model.ts
│   │   │   │   ├── user.validation.ts
│   │   │   │   ├── user.service.ts
│   │   │   │   ├── user.controller.ts
│   │   │   │   ├── user.route.ts
│   │   │   │   └── user.test.ts
│   │   │   └── doc/
│   │   │       └── ...
│   │   │
│   │   ├── landingPage.module/
│   │   │   ├── landingPage/
│   │   │   │   ├── landingPage.constant.ts
│   │   │   │   ├── landingPage.interface.ts
│   │   │   │   ├── landingPage.model.ts
│   │   │   │   ├── landingPage.validation.ts
│   │   │   │   ├── landingPage.service.ts
│   │   │   │   ├── landingPage.controller.ts
│   │   │   │   ├── landingPage.route.ts
│   │   │   │   └── landingPage.test.ts
│   │   │   ├── publicLandingPage/
│   │   │   │   ├── publicLandingPage.constant.ts
│   │   │   │   ├── publicLandingPage.interface.ts
│   │   │   │   ├── publicLandingPage.service.ts
│   │   │   │   ├── publicLandingPage.controller.ts
│   │   │   │   └── publicLandingPage.route.ts
│   │   │   └── doc/
│   │   │       └── ...
│   │   │
│   │   ├── promoter.module/
│   │   │   ├── promoter/
│   │   │   │   ├── promoter.constant.ts
│   │   │   │   ├── promoter.interface.ts
│   │   │   │   ├── promoter.model.ts
│   │   │   │   ├── promoter.validation.ts
│   │   │   │   ├── promoter.service.ts
│   │   │   │   ├── promoter.controller.ts
│   │   │   │   ├── promoter.route.ts
│   │   │   │   └── promoter.test.ts
│   │   │   └── doc/
│   │   │       └── ...
│   │   │
│   │   ├── campaign.module/
│   │   │   ├── campaign/
│   │   │   │   ├── campaign.constant.ts
│   │   │   │   ├── campaign.interface.ts
│   │   │   │   ├── campaign.model.ts
│   │   │   │   ├── campaign.validation.ts
│   │   │   │   ├── campaign.service.ts
│   │   │   │   ├── campaign.controller.ts
│   │   │   │   ├── campaign.route.ts
│   │   │   │   └── campaign.test.ts
│   │   │   ├── event/
│   │   │   │   ├── event.constant.ts
│   │   │   │   ├── event.interface.ts
│   │   │   │   ├── event.model.ts
│   │   │   │   ├── event.validation.ts
│   │   │   │   ├── event.service.ts
│   │   │   │   ├── event.controller.ts
│   │   │   │   ├── event.route.ts
│   │   │   │   └── event.test.ts
│   │   │   └── doc/
│   │   │       └── ...
│   │   │
│   │   ├── participant.module/
│   │   │   ├── participant/
│   │   │   │   ├── participant.constant.ts
│   │   │   │   ├── participant.interface.ts
│   │   │   │   ├── participant.model.ts
│   │   │   │   ├── participant.validation.ts
│   │   │   │   ├── participant.service.ts
│   │   │   │   ├── participant.controller.ts
│   │   │   │   ├── participant.route.ts
│   │   │   │   └── participant.test.ts
│   │   │   └── doc/
│   │   │       └── ...
│   │   │
│   │   ├── card.module/
│   │   │   ├── card/
│   │   │   │   ├── card.constant.ts
│   │   │   │   ├── card.interface.ts
│   │   │   │   ├── card.model.ts
│   │   │   │   ├── card.validation.ts
│   │   │   │   ├── card.service.ts
│   │   │   │   ├── card.controller.ts
│   │   │   │   ├── card.route.ts
│   │   │   │   └── card.test.ts
│   │   │   ├── cardInteraction/
│   │   │   │   ├── cardInteraction.constant.ts
│   │   │   │   ├── cardInteraction.interface.ts
│   │   │   │   ├── cardInteraction.model.ts
│   │   │   │   ├── cardInteraction.service.ts
│   │   │   │   ├── cardInteraction.controller.ts
│   │   │   │   └── cardInteraction.route.ts
│   │   │   └── doc/
│   │   │       └── ...
│   │   │
│   │   ├── game.module/
│   │   │   ├── gamePlay/
│   │   │   │   ├── gamePlay.constant.ts
│   │   │   │   ├── gamePlay.interface.ts
│   │   │   │   ├── gamePlay.model.ts
│   │   │   │   ├── gamePlay.validation.ts
│   │   │   │   ├── gamePlay.service.ts
│   │   │   │   ├── gamePlay.controller.ts
│   │   │   │   ├── gamePlay.route.ts
│   │   │   │   └── gamePlay.test.ts
│   │   │   ├── prize/
│   │   │   │   ├── prize.constant.ts
│   │   │   │   ├── prize.interface.ts
│   │   │   │   ├── prize.model.ts
│   │   │   │   ├── prize.validation.ts
│   │   │   │   ├── prize.service.ts
│   │   │   │   ├── prize.controller.ts
│   │   │   │   ├── prize.route.ts
│   │   │   │   └── prize.test.ts
│   │   │   ├── game.service.ts (shared game logic)
│   │   │   └── doc/
│   │   │       └── ...
│   │   │
│   │   ├── review.module/
│   │   │   ├── review/
│   │   │   │   ├── review.constant.ts
│   │   │   │   ├── review.interface.ts
│   │   │   │   ├── review.model.ts
│   │   │   │   ├── review.service.ts
│   │   │   │   ├── review.controller.ts
│   │   │   │   ├── review.route.ts
│   │   │   │   └── review.test.ts
│   │   │   └── doc/
│   │   │       └── ...
│   │   │
│   │   ├── social.module/
│   │   │   ├── socialLink/
│   │   │   │   ├── socialLink.constant.ts
│   │   │   │   ├── socialLink.interface.ts
│   │   │   │   ├── socialLink.model.ts
│   │   │   │   ├── socialLink.service.ts
│   │   │   │   ├── socialLink.controller.ts
│   │   │   │   ├── socialLink.route.ts
│   │   │   │   └── socialLink.test.ts
│   │   │   ├── socialLinkClick/
│   │   │   │   ├── socialLinkClick.constant.ts
│   │   │   │   ├── socialLinkClick.interface.ts
│   │   │   │   ├── socialLinkClick.model.ts
│   │   │   │   ├── socialLinkClick.service.ts
│   │   │   │   ├── socialLinkClick.controller.ts
│   │   │   │   └── socialLinkClick.route.ts
│   │   │   └── doc/
│   │   │       └── ...
│   │   │
│   │   ├── analytics.module/
│   │   │   ├── analytics/
│   │   │   │   ├── analytics.constant.ts
│   │   │   │   ├── analytics.interface.ts
│   │   │   │   ├── analytics.model.ts
│   │   │   │   ├── analytics.service.ts
│   │   │   │   ├── analytics.controller.ts
│   │   │   │   ├── analytics.route.ts
│   │   │   │   └── analytics.test.ts
│   │   │   └── doc/
│   │   │       └── ...
│   │   │
│   │   └── report.module/
│   │       ├── report/
│   │       │   ├── report.constant.ts
│   │       │   ├── report.interface.ts
│   │       │   ├── report.model.ts
│   │       │   ├── report.validation.ts
│   │       │   ├── report.service.ts
│   │       │   ├── report.controller.ts
│   │       │   ├── report.route.ts
│   │       │   └── report.test.ts
│   │       └── doc/
│   │           └── ...
│   │
│   ├── middleware/
│   │   ├── authenticate.middleware.ts
│   │   ├── authorize.middleware.ts
│   │   ├── validateRequest.middleware.ts
│   │   ├── sanitizeInput.middleware.ts
│   │   ├── rateLimiter.middleware.ts
│   │   ├── requestLogger.middleware.ts
│   │   ├── errorHandler.middleware.ts
│   │   └── fileUpload.middleware.ts
│   │
│   ├── services/
│   │   ├── generic.service.ts
│   │   ├── pagination.service.ts
│   │   ├── cache.service.ts
│   │   ├── queue.service.ts
│   │   ├── email.service.ts
│   │   └── file.service.ts
│   │
│   ├── controllers/
│   │   └── generic.controller.ts
│   │
│   ├── queues/
│   │   ├── queues.constants.ts
│   │   ├── analytics.queue.ts
│   │   ├── email.queue.ts
│   │   ├── report.queue.ts
│   │   └── game.queue.ts
│   │
│   ├── utils/
│   │   ├── apiResponse.ts
│   │   ├── errorHandler.ts
│   │   ├── logger.ts
│   │   ├── qrGenerator.ts
│   │   ├── deviceFingerprint.ts
│   │   └── probability.ts
│   │
│   ├── types/
│   │   ├── express.d.ts
│   │   ├── models.d.ts
│   │   └── globals.d.ts
│   │
│   ├── routes/
│   │   └── index.ts
│   │
│   ├── app.ts
│   └── server.ts
│
├── tests/
├── migrations/
├── scripts/
├── __Documentation/
├── figma-asset/
├── .env.example
├── .dockerignore
├── Dockerfile
├── docker-compose.yml
├── package.json
├── tsconfig.json
└── README.md
```

---

## 5. Development Phases

### Phase 0: Foundation Setup (Week 1)

**Objective:** Establish project infrastructure and core utilities

**Deliverables:**
- [ ] Project scaffolding (TypeScript, Express, folder structure)
- [ ] MongoDB connection with connection pooling
- [ ] Redis connection with error handling
- [ ] BullMQ queue configuration
- [ ] Generic controller implementation
- [ ] Generic service implementation
- [ ] Pagination service implementation
- [ ] Cache service implementation
- [ ] Queue service implementation
- [ ] Middleware stack (authenticate, authorize, validate, sanitize, rateLimit, logger, errorHandler)
- [ ] Environment configuration
- [ ] Docker setup (docker-compose with MongoDB, Redis)
- [ ] Health check endpoint
- [ ] Request logging with correlationId
- [ ] Error tracking setup
- [ ] ESLint + Prettier configuration
- [ ] Git hooks (Husky + lint-staged)

**Risk Level:** Low  
**Dependencies:** None  

---

### Phase 1: Authentication & User Management (Week 1-2)

**Objective:** Secure authentication system with role-based access

**Modules:**
- `auth.module`
- `user.module`

**Deliverables:**
- [ ] User model (with roles: admin, campaign_manager, promoter)
- [ ] Session model (stored in MongoDB + Redis)
- [ ] Auth service (register, login, logout, refresh, password reset)
- [ ] JWT implementation (access token 15min, refresh token 7 days)
- [ ] Refresh token rotation
- [ ] Refresh token reuse detection
- [ ] Rate limiting for auth endpoints (5 req/min)
- [ ] Password hashing (bcrypt, cost factor 12)
- [ ] Email verification flow
- [ ] Password reset flow
- [ ] User CRUD endpoints
- [ ] Role-based authorization middleware
- [ ] Profile management endpoints

**BullMQ Jobs:**
- [ ] Email sending jobs (verification, password reset)

**Redis Caching:**
- [ ] User profile cache (TTL: 15 minutes)
- [ ] Session storage (TTL: match refresh token expiry)

**Database Indexes:**
- [ ] User: email (unique), role, status
- [ ] Session: user, tokenHash, expiresAt (TTL)

**Documentation:**
- [ ] Auth module diagrams (architecture, sequence, state machine)
- [ ] Auth performance report
- [ ] User module diagrams

**Risk Level:** Low  
**Dependencies:** Phase 0  

---

### Phase 2: Landing Page Management (Week 2-3)

**Objective:** Landing page CRUD with block-based customization

**Modules:**
- `landingPage.module`
- `landingPage.module/sub-modules/publicLandingPage.module`

**Deliverables:**
- [ ] LandingPage model (blocks as flexible schema)
- [ ] LandingPage service (CRUD, duplicate, publish/unpublish)
- [ ] LandingPage validation schemas
- [ ] LandingPage admin endpoints (CRUD, duplicate, publish)
- [ ] Public landing page endpoint (slug-based lookup)
- [ ] Landing page content validation
- [ ] Promoter assignment to landing pages
- [ ] Page slug uniqueness validation
- [ ] Block structure validation

**BullMQ Jobs:**
- [ ] Landing page analytics aggregation

**Redis Caching:**
- [ ] Public landing page cache (TTL: 5 minutes, invalidate on publish)
- [ ] Admin landing page list cache (TTL: 2 minutes)

**Database Indexes:**
- [ ] LandingPage: slug (unique), status, publishedAt

**Documentation:**
- [ ] LandingPage module diagrams
- [ ] LandingPage performance report
- [ ] Public landing page flow diagrams

**Risk Level:** Medium  
**Dependencies:** Phase 1  

---

### Phase 3: Promoter Management (Week 3-4)

**Objective:** Promoter CRUD and performance tracking

**Modules:**
- `promoter.module`

**Deliverables:**
- [ ] Promoter model (name, nickname, stats counters)
- [ ] Promoter service (CRUD, stats calculation)
- [ ] Promoter validation schemas
- [ ] Promoter CRUD endpoints
- [ ] Promoter leaderboard endpoint (aggregation pipeline)
- [ ] Promoter statistics endpoint
- [ ] Promoter selection tracking (PromoterSelection model)
- [ ] Promoter-card relationship management
- [ ] Nickname-based review matching

**BullMQ Jobs:**
- [ ] Promoter stats recalculation
- [ ] Leaderboard generation

**Redis Caching:**
- [ ] Promoter profile cache (TTL: 15 minutes)
- [ ] Leaderboard cache using Redis sorted sets (TTL: 5 minutes)

**Database Indexes:**
- [ ] Promoter: totalInteractions, status, text index (name, nickname)
- [ ] PromoterSelection: promoter, landingPage + promoter

**Documentation:**
- [ ] Promoter module diagrams
- [ ] Promoter performance report
- [ ] Leaderboard algorithm documentation

**Risk Level:** Low  
**Dependencies:** Phase 2  

---

### Phase 4: Campaign & Event Management (Week 4-5)

**Objective:** Campaign and event lifecycle management

**Modules:**
- `campaign.module`
- `campaign.module/sub-modules/event.module`

**Deliverables:**
- [ ] Campaign model (date ranges, status, counters)
- [ ] Event model (campaign reference, eventDate)
- [ ] Campaign service (CRUD, status transitions)
- [ ] Event service (CRUD, event management)
- [ ] Campaign CRUD endpoints
- [ ] Event CRUD endpoints (nested under campaign)
- [ ] Campaign statistics endpoint
- [ ] Upcoming events endpoint
- [ ] Campaign progress tracking
- [ ] Status transition validation (scheduled → active → completed)

**BullMQ Jobs:**
- [ ] Campaign status automation (scheduled → active based on date)
- [ ] Campaign analytics aggregation

**Redis Caching:**
- [ ] Campaign list cache (TTL: 2 minutes)
- [ ] Upcoming events cache (TTL: 5 minutes)

**Database Indexes:**
- [ ] Campaign: status + startDate + endDate, landingPage
- [ ] Event: campaign, eventDate

**Documentation:**
- [ ] Campaign module diagrams
- [ ] Event module diagrams
- [ ] Campaign state machine diagram

**Risk Level:** Low  
**Dependencies:** Phase 3  

---

### Phase 5: Card Management (Week 5-6)

**Objective:** NFC card and QR code management

**Modules:**
- `card.module`
- `card.module/sub-modules/cardInteraction.module`

**Deliverables:**
- [ ] Card model (nfc, qr_code types, assignment)
- [ ] CardInteraction model (tap tracking)
- [ ] Card service (CRUD, assignment, NFC resolution)
- [ ] Card CRUD endpoints
- [ ] Card assignment endpoints
- [ ] NFC UUID resolution endpoint (critical for NFC tap flow)
- [ ] QR code generation endpoint
- [ ] Card interaction tracking
- [ ] Card statistics endpoint
- [ ] NFC UUID uniqueness validation

**BullMQ Jobs:**
- [ ] Card analytics aggregation
- [ ] QR code generation (async for bulk orders)

**Redis Caching:**
- [ ] NFC UUID → Card resolution cache (TTL: 1 hour, critical path)
- [ ] Card details cache (TTL: 15 minutes)

**Database Indexes:**
- [ ] Card: nfcUuid (unique, sparse), promoter + status
- [ ] CardInteraction: card + createdAt, createdAt

**Documentation:**
- [ ] Card module diagrams
- [ ] NFC tap flow sequence diagram
- [ ] Card performance report

**Risk Level:** Medium (NFC integration complexity)  
**Dependencies:** Phase 4  

---

### Phase 6: Participant Management (Week 6-7)

**Objective:** Participant data capture and management

**Modules:**
- `participant.module`

**Deliverables:**
- [ ] Participant model (contact info, status, prize tracking)
- [ ] Participant service (CRUD, export)
- [ ] Participant validation schemas
- [ ] Participant CRUD endpoints
- [ ] Participant list endpoint with pagination
- [ ] Participant export endpoint (CSV/Excel)
- [ ] Participant search (name, email)
- [ ] Participant status management
- [ ] Consent tracking (GDPR compliance)
- [ ] Duplicate email detection

**BullMQ Jobs:**
- [ ] Participant export generation
- [ ] Participant analytics aggregation

**Redis Caching:**
- [ ] Participant count cache (TTL: 2 minutes)
- [ ] Participant list cache (TTL: 1 minute)

**Database Indexes:**
- [ ] Participant: landingPage + createdAt, campaign + createdAt, promoter + createdAt, email

**Documentation:**
- [ ] Participant module diagrams
- [ ] GDPR compliance documentation
- [ ] Participant performance report

**Risk Level:** Low  
**Dependencies:** Phase 5  

---

### Phase 7: Game Engine (Week 7-8)

**Objective:** Game interaction handling with fraud prevention

**Modules:**
- `game.module`
- `game.module/sub-modules/gamePlay.module`
- `game.module/sub-modules/prize.module`

**Deliverables:**
- [ ] GamePlay model (game type, result, redemption tracking)
- [ ] Prize model (probability, redemption limits)
- [ ] Game service (dice roll, spin wheel logic)
- [ ] Game play endpoint (public landing page)
- [ ] Prize configuration endpoints
- [ ] Prize redemption endpoint
- [ ] Weighted probability algorithm
- [ ] Rate limiting (1 play per 5 minutes per device)
- [ ] Device fingerprinting
- [ ] Fraud detection (duplicate prevention, pattern detection)
- [ ] Game statistics endpoint
- [ ] Prize distribution endpoint

**BullMQ Jobs:**
- [ ] Game analytics aggregation
- [ ] Fraud detection analysis

**Redis Caching:**
- [ ] Game play rate limit tracking (Sliding window, TTL: 5 minutes)
- [ ] Prize configuration cache (TTL: 15 minutes)
- [ ] Game statistics cache (TTL: 2 minutes)

**Database Indexes:**
- [ ] GamePlay: deviceFingerprint + createdAt, ipAddress + createdAt, landingPage + gameType + createdAt
- [ ] Prize: landingPage + active + displayOrder

**Documentation:**
- [ ] Game module diagrams
- [ ] Game probability algorithm documentation
- [ ] Fraud detection strategy documentation
- [ ] Game performance report

**Risk Level:** High (fraud prevention complexity)  
**Dependencies:** Phase 6  

---

### Phase 8: Review & Social Tracking (Week 8-9)

**Objective:** Review click tracking and social link analytics

**Modules:**
- `review.module`
- `social.module`
- `social.module/sub-modules/socialLinkClick.module`

**Deliverables:**
- [ ] ReviewClick model (review click tracking)
- [ ] SocialLink model (platform, URL)
- [ ] SocialLinkClick model (click tracking)
- [ ] Review tracking service
- [ ] Social link service
- [ ] Review click endpoint (public landing page)
- [ ] Social link click endpoint (public landing page)
- [ ] Review engagement endpoint (admin)
- [ ] Review funnel endpoint (admin)
- [ ] Social link analytics endpoint
- [ ] Social link configuration endpoints
- [ ] Top performing links endpoint

**BullMQ Jobs:**
- [ ] Review analytics aggregation
- [ ] Social analytics aggregation

**Redis Caching:**
- [ ] Review statistics cache (TTL: 2 minutes)
- [ ] Social link list cache (TTL: 15 minutes)
- [ ] Social analytics cache (TTL: 5 minutes)

**Database Indexes:**
- [ ] ReviewClick: landingPage + createdAt, promoter + createdAt
- [ ] SocialLink: landingPage + displayOrder
- [ ] SocialLinkClick: socialLink + createdAt, createdAt

**Documentation:**
- [ ] Review module diagrams
- [ ] Social module diagrams
- [ ] Review tracking limitations documentation

**Risk Level:** Low  
**Dependencies:** Phase 7  

---

### Phase 9: Analytics Dashboard (Week 9-10)

**Objective:** Comprehensive analytics and reporting

**Modules:**
- `analytics.module`

**Deliverables:**
- [ ] AnalyticsDaily model (pre-aggregated daily metrics)
- [ ] Analytics service (dashboard overview, trends, breakdowns)
- [ ] Dashboard overview endpoint (all metrics with trends)
- [ ] Interaction analytics endpoint
- [ ] Peak activity hours endpoint
- [ ] Geographic distribution endpoint
- [ ] Card performance endpoint
- [ ] Promoter leaderboard endpoint
- [ ] Trend data endpoint
- [ ] Daily analytics aggregation job (scheduled)
- [ ] Real-time statistics endpoint

**BullMQ Jobs:**
- [ ] Daily analytics aggregation (scheduled, runs at midnight)
- [ ] Real-time analytics aggregation (frequent, every 5 minutes)

**Redis Caching:**
- [ ] Dashboard metrics cache (TTL: 1 minute)
- [ ] Analytics breakdown cache (TTL: 2 minutes)
- [ ] Trend data cache (TTL: 5 minutes)

**Database Indexes:**
- [ ] AnalyticsDaily: date (unique)

**Documentation:**
- [ ] Analytics module diagrams
- [ ] Aggregation pipeline documentation
- [ ] Analytics performance report

**Risk Level:** Medium (complex aggregation pipelines)  
**Dependencies:** Phase 8  

---

### Phase 10: Report Generation (Week 10-11)

**Objective:** Report generation and data export

**Modules:**
- `report.module`

**Deliverables:**
- [ ] Report model (report tracking)
- [ ] Report service (generation, status tracking)
- [ ] Report generation endpoints
- [ ] Report download endpoint
- [ ] Report list endpoint
- [ ] Quick export endpoints (CSV, Excel, JSON, PDF)
- [ ] Report types:
  - Monthly Performance Report
  - Audience Data Export
  - Engagement Report
  - Game Performance Report
- [ ] File storage integration (S3/cloud storage)
- [ ] Report generation progress tracking

**BullMQ Jobs:**
- [ ] Report generation jobs (PDF, Excel)
- [ ] Data export jobs (CSV, JSON)

**Redis Caching:**
- [ ] Report list cache (TTL: 1 minute)

**Database Indexes:**
- [ ] Report: status + createdAt, completedAt

**Documentation:**
- [ ] Report module diagrams
- [ ] Export format specifications

**Risk Level:** Medium (file generation complexity)  
**Dependencies:** Phase 9  

---

### Phase 11: Integration & Polish (Week 11-12)

**Objective:** Third-party integrations and production readiness

**Deliverables:**
- [ ] Email service integration (SendGrid/AWS SES)
- [ ] QR code generation integration
- [ ] NFC card provider integration (if applicable)
- [ ] Google Business API integration (review tracking)
- [ ] File storage integration (S3/cloud storage)
- [ ] API documentation (Swagger/OpenAPI)
- [ ] Postman collection generation
- [ ] Performance testing and optimization
- [ ] Security audit and hardening
- [ ] Load testing
- [ ] Error handling refinement
- [ ] Logging refinement
- [ ] Health check refinement

**BullMQ Jobs:**
- [ ] Email sending optimization
- [ ] File processing optimization

**Redis Caching:**
- [ ] Integration-specific caching
- [ ] Rate limit refinement

**Documentation:**
- [ ] API documentation
- [ ] Postman collection
- [ ] Deployment guide
- [ ] Operations manual

**Risk Level:** Low  
**Dependencies:** Phase 10  

---

### Phase 12: Testing & QA (Week 12-13)

**Objective:** Comprehensive testing and quality assurance

**Deliverables:**
- [ ] Unit tests (all services, utilities)
- [ ] Integration tests (all endpoints)
- [ ] E2E tests (critical user flows)
- [ ] Load testing reports
- [ ] Security testing reports
- [ ] Performance benchmarks
- [ ] Bug fixes
- [ ] Code review and refactoring
- [ ] Documentation review and completion

**Testing Coverage Targets:**
- Unit tests: > 80%
- Integration tests: 100% of endpoints
- E2E tests: All critical flows

**Risk Level:** Low  
**Dependencies:** Phase 11  

---

### Phase 13: Deployment & Launch (Week 13-14)

**Objective:** Production deployment and launch

**Deliverables:**
- [ ] Production environment setup
- [ ] MongoDB cluster setup
- [ ] Redis cluster setup
- [ ] CI/CD pipeline configuration
- [ ] Monitoring setup (Datadog/New Relic)
- [ ] Error tracking setup (Sentry)
- [ ] Backup configuration
- [ ] Disaster recovery testing
- [ ] Launch preparation
- [ ] Go-live checklist
- [ ] Post-launch monitoring

**Risk Level:** Medium  
**Dependencies:** Phase 12  

---

## 6. Module Implementation Order

### Priority Matrix

| Priority | Module | Rationale |
|----------|--------|-----------|
| **P0** | Foundation (Phase 0) | Core infrastructure, required by all modules |
| **P0** | Auth (Phase 1) | Security foundation, required by all authenticated endpoints |
| **P0** | User (Phase 1) | User management, required by auth |
| **P0** | LandingPage (Phase 2) | Core entity, referenced by most modules |
| **P0** | Promoter (Phase 3) | Core entity, required for campaigns and cards |
| **P0** | Campaign (Phase 4) | Core entity, required for events and participants |
| **P0** | Card (Phase 5) | Core entity, primary interaction mechanism |
| **P0** | Participant (Phase 6) | Core entity, captures user data |
| **P0** | Game (Phase 7) | Core engagement feature |
| **P1** | Review (Phase 8) | Important but not blocking |
| **P1** | Social (Phase 8) | Important but not blocking |
| **P1** | Analytics (Phase 9) | Requires data from other modules |
| **P1** | Report (Phase 10) | Requires data from other modules |
| **P2** | Integration (Phase 11) | Third-party integrations |
| **P2** | Testing (Phase 12) | QA phase |
| **P2** | Deployment (Phase 13) | Launch phase |

---

## 7. Database Strategy

### 7.1 MongoDB Configuration

```typescript
// config/database.ts
import mongoose from 'mongoose';

export async function connectDatabase() {
  const mongoUri = process.env.MONGODB_URI || 'mongodb://localhost:27017/promotercard';
  
  mongoose.set('strictQuery', true);
  mongoose.set('debug', process.env.NODE_ENV === 'development');
  
  try {
    await mongoose.connect(mongoUri, {
      // Connection pool configuration
      maxPoolSize: parseInt(process.env.MONGO_MAX_POOL_SIZE || '100'),
      minPoolSize: parseInt(process.env.MONGO_MIN_POOL_SIZE || '10'),
      serverSelectionTimeoutMS: 5000,
      socketTimeoutMS: 45000,
      
      // Reliability
      retryWrites: true,
      retryReads: true,
      
      // Read preference (for analytics queries)
      readPreference: process.env.NODE_ENV === 'production' ? 'secondaryPreferred' : 'primary',
    });
    
    console.log('✅ MongoDB connected successfully');
    
    // Connection event handlers
    mongoose.connection.on('error', (err) => {
      logger.error('MongoDB connection error:', err);
    });
    
    mongoose.connection.on('disconnected', () => {
      logger.warn('MongoDB disconnected');
    });
    
  } catch (error) {
    logger.error('❌ MongoDB connection error:', error);
    process.exit(1);
  }
}
```

### 7.2 Indexing Strategy

**Critical Indexes (Create First):**
```typescript
// Run during application startup or migration
export async function createIndexes() {
  // Highest priority: NFC resolution (most critical query)
  await Card.createIndexes();
  
  // Public-facing performance
  await LandingPage.createIndexes();
  
  // High query volume
  await Participant.createIndexes();
  await GamePlay.createIndexes();
  
  // Analytics indexes
  await AnalyticsDaily.createIndexes();
  
  // All other indexes
  await Promise.all([
    User.createIndexes(),
    Promoter.createIndexes(),
    Campaign.createIndexes(),
    Event.createIndexes(),
    CardInteraction.createIndexes(),
    Prize.createIndexes(),
    ReviewClick.createIndexes(),
    SocialLink.createIndexes(),
    SocialLinkClick.createIndexes(),
    PromoterSelection.createIndexes(),
    Report.createIndexes(),
    Session.createIndexes(),
  ]);
}
```

### 7.3 Query Optimization Rules

**Mandatory Practices:**
- ✅ Use `.lean()` on all read-only queries
- ✅ Use `.select()` to limit returned fields
- ✅ Use pagination on all list endpoints
- ✅ Use aggregation pipelines for joined data
- ✅ Never return unpaginated lists
- ✅ Avoid `$lookup` chains deeper than 2 levels
- ✅ Use `.hint()` for critical queries
- ✅ Use `.explain('executionStats')` to verify index usage

**Example:**
```typescript
// ✅ Good: Lean query with projection
const promoters = await Promoter.find({ status: 'active' })
  .select('name nickname totalInteractions avatarUrl')
  .sort({ totalInteractions: -1 })
  .limit(20)
  .lean();

// ❌ Bad: Full document return without lean
const promoters = await Promoter.find({ status: 'active' });
```

---

## 8. Caching Strategy

### 8.1 Redis Configuration

```typescript
// config/redis.ts
import { createClient } from 'redis';
import { logger } from '../utils/logger';

export const redisClient = createClient({
  url: process.env.REDIS_URL || 'redis://localhost:6379',
  socket: {
    reconnectStrategy: (retries) => Math.min(retries * 50, 2000),
  },
});

redisClient.on('error', (err) => {
  logger.error('Redis Error:', err);
});

export async function connectRedis() {
  await redisClient.connect();
  logger.info('✅ Redis connected');
}
```

### 8.2 Cache Service Implementation

```typescript
// services/cache.service.ts
import { redisClient } from '../config/redis';

export class CacheService {
  /**
   * Get data from cache
   */
  async get<T>(key: string): Promise<T | null> {
    const data = await redisClient.get(key);
    return data ? JSON.parse(data) : null;
  }

  /**
   * Set data in cache with TTL
   */
  async set(key: string, value: any, ttlSeconds: number): Promise<void> {
    await redisClient.setEx(key, ttlSeconds, JSON.stringify(value));
  }

  /**
   * Delete data from cache
   */
  async delete(key: string): Promise<void> {
    await redisClient.del(key);
  }

  /**
   * Delete multiple keys matching pattern
   */
  async deletePattern(pattern: string): Promise<void> {
    const keys = await redisClient.keys(pattern);
    if (keys.length > 0) {
      await redisClient.del(keys);
    }
  }

  /**
   * Cache-aside pattern
   */
  async getOrSet<T>(
    key: string,
    fetchFn: () => Promise<T>,
    ttlSeconds: number
  ): Promise<T> {
    // Try cache first
    const cached = await this.get<T>(key);
    if (cached) {
      return cached;
    }

    // Cache miss - fetch from DB
    const data = await fetchFn();

    // Store in cache
    await this.set(key, data, ttlSeconds);

    return data;
  }
}

export const cacheService = new CacheService();
```

### 8.3 Cache Key Naming Convention

```
Format: <module>:<identifier>:<datatype>

Examples:
  landingpage:public:azure-beach          # Public landing page
  landingpage:list:1                      # Landing page list (page 1)
  promoter:profile:promo001               # Promoter profile
  promoter:leaderboard                    # Promoter leaderboard
  card:detail:card123                     # Card details
  card:nfc:uuid12345                      # NFC UUID resolution
  campaign:list:active                    # Campaign list (active)
  event:upcoming                          # Upcoming events
  participant:count                       # Participant count
  game:stats:7d                           # Game statistics (7 days)
  prize:config:page123                    # Prize configuration
  review:stats:7d                         # Review statistics (7 days)
  sociallink:list:page123                 # Social link list
  sociallink:analytics:7d                 # Social analytics (7 days)
  analytics:overview:7d                   # Analytics overview (7 days)
  report:list:pending                     # Report list (pending)
  session:user123:device456               # Session data
```

### 8.4 TTL by Data Type

| Data Type | TTL | Rationale |
|-----------|-----|-----------|
| User profile | 15 minutes | Moderate freshness |
| Landing page (public) | 5 minutes | Published, stable |
| Landing page list | 2 minutes | Frequent edits |
| Promoter profile | 15 minutes | Moderate freshness |
| Leaderboard | 5 minutes | Near real-time |
| Card details | 15 minutes | Moderate freshness |
| NFC UUID resolution | 1 hour | Static mapping |
| Campaign list | 2 minutes | Moderate updates |
| Upcoming events | 5 minutes | Near real-time |
| Participant count | 2 minutes | Frequent updates |
| Game statistics | 2 minutes | Near real-time |
| Prize config | 15 minutes | Rarely changes |
| Review statistics | 2 minutes | Frequent updates |
| Social link list | 15 minutes | Rarely changes |
| Social analytics | 5 minutes | Near real-time |
| Dashboard metrics | 1 minute | High-frequency updates |
| Report list | 1 minute | Frequent updates |
| Session | 7 days | Match refresh token |

### 8.5 Cache Invalidation Strategy

```typescript
// Example: Landing page cache invalidation
async function updateLandingPage(id: string, data: UpdateLandingPageDto) {
  // Update in database
  const updatedPage = await LandingPage.findByIdAndUpdate(id, data, { new: true });

  // Invalidate cache
  await cacheService.delete(`landingpage:detail:${id}`);
  await cacheService.delete(`landingpage:public:${updatedPage.slug}`);
  await cacheService.deletePattern(`landingpage:list:*`);

  return updatedPage;
}
```

---

## 9. Queue Strategy

### 9.1 Queue Configuration

```typescript
// queues/queues.constants.ts
export const QUEUE_NAMES = {
  CRITICAL: 'critical-queue',
  STANDARD: 'standard-queue',
  LOW: 'low-queue',
} as const;

// queues/queue-config.ts
export const QUEUE_CONFIG = {
  attempts: 3,
  backoff: { type: 'exponential' as const, delay: 2000 },
  removeOnComplete: { count: 100 },
  removeOnFail: { count: 500 },
};
```

### 9.2 Queue Priority Tiers

| Queue | Priority | Use Cases |
|-------|----------|-----------|
| `critical-queue` | Highest | Auth emails, password reset, payment events |
| `standard-queue` | Medium | Reports, exports, bulk updates, analytics |
| `low-queue` | Lowest | Cleanup jobs, audit logs, non-urgent notifications |

### 9.3 Queue Service Implementation

```typescript
// services/queue.service.ts
import { Queue } from 'bullmq';
import { QUEUE_NAMES, QUEUE_CONFIG } from '../queues/queues.constants';
import { redisClient } from '../config/redis';

export class QueueService {
  private queues: Map<string, Queue>;

  constructor() {
    this.queues = new Map();
    this.initializeQueues();
  }

  private initializeQueues() {
    Object.values(QUEUE_NAMES).forEach((queueName) => {
      const queue = new Queue(queueName, {
        connection: redisClient,
        defaultJobOptions: QUEUE_CONFIG,
      });
      this.queues.set(queueName, queue);
    });
  }

  async addJob<T>(
    queueName: string,
    jobName: string,
    data: T,
    options?: any
  ): Promise<string> {
    const queue = this.queues.get(queueName);
    if (!queue) {
      throw new Error(`Queue ${queueName} not found`);
    }

    const job = await queue.add(jobName, data, {
      ...QUEUE_CONFIG,
      jobId: `${jobName}-${Date.now()}-${Math.random().toString(36).substr(2, 9)}`,
      ...options,
    });

    return job.id!;
  }

  async getQueueStatus() {
    const status: Record<string, any> = {};
    for (const [name, queue] of this.queues) {
      const [waiting, active, completed, failed] = await Promise.all([
        queue.getWaitingCount(),
        queue.getActiveCount(),
        queue.getCompletedCount(),
        queue.getFailedCount(),
      ]);
      status[name] = { waiting, active, completed, failed };
    }
    return status;
  }
}

export const queueService = new QueueService();
```

### 9.4 When to Use BullMQ

**Mandatory Use Cases:**
- ✅ Any operation taking > 500ms
- ✅ Report generation (PDF, Excel)
- ✅ Data export (CSV, JSON)
- ✅ Email sending
- ✅ File processing
- ✅ Analytics aggregation
- ✅ Bulk operations (> 100 records)

**Example:**
```typescript
// ✅ Good: Async report generation
@Post('generate')
async generateReport(req: Request, res: Response) {
  const jobId = await queueService.addJob(
    QUEUE_NAMES.STANDARD,
    'generate-report',
    {
      reportType: req.body.reportType,
      format: req.body.format,
      parameters: req.body.parameters,
    }
  );

  return apiResponse.accepted(res, {
    jobId,
    message: 'Report generation started. Check status with GET /reports/:id',
  });
}

// ❌ Bad: Synchronous heavy operation
@Post('generate')
async generateReport(req: Request, res: Response) {
  const report = await generateReportSync(req.body); // Blocks thread!
  return apiResponse.success(res, report);
}
```

---

## 10. API Development Sequence

### 10.1 Endpoint Development Order

**For Each Module:**
1. Model definition (schema, indexes)
2. Validation schemas (Zod)
3. Service implementation (CRUD + business logic)
4. Controller implementation (route handlers)
5. Route definitions (with middleware)
6. Documentation (README, diagrams, performance report)
7. Tests (unit, integration)

### 10.2 Generic Controller Usage Pattern

```typescript
// Example: Using generic controller
import { GenericController } from '../../controllers/generic.controller';
import { landingPageService } from './landingPage.service';

const genericController = new GenericController();

/*-─────────────────────────────────
|  Role: Admin | Module: Landing Page
|  Figma: landing-pages/landing-page-builder-01.png
|  Action: Get all landing pages with pagination
|  Auth: Required
|  Rate Limit: 100 req/min per userId
└──────────────────────────────────*/
router.get(
  '/',
  authenticate,
  authorize('admin', 'campaign_manager'),
  rateLimiter('user'),
  genericController.getAllWithPaginationV2(landingPageService.getAll)
);
```

### 10.3 Generic Service Pattern

```typescript
// Example: Service extending generic service
import { GenericService } from '../../services/generic.service';
import { LandingPage, ILandingPage } from './landingPage.model';

class LandingPageService extends GenericService<ILandingPage> {
  constructor() {
    super(LandingPage);
  }

  async getAll(query: any, options: any) {
    // Custom query logic
    const filter = { ...super.buildFilter(query) };
    return this.model.find(filter)
      .select('title slug status publishedAt')
      .sort({ createdAt: -1 })
      .lean();
  }

  // Business-specific methods
  async publish(id: string): Promise<ILandingPage> {
    return this.model.findByIdAndUpdate(
      id,
      { status: 'published', publishedAt: new Date() },
      { new: true }
    ).lean();
  }
}
```

---

## 11. Testing Strategy

### 11.1 Testing Pyramid

```
        /\
       /E2E\          ← 10% coverage (critical flows only)
      /------\
     /Integ  \        ← 30% coverage (all endpoints)
    /--------\
   /  Unit    \       ← 60% coverage (services, utilities)
  /------------\
```

### 11.2 Unit Tests

**Coverage Target:** > 80%

**What to Test:**
- Service methods (business logic)
- Utility functions
- Validation schemas
- Helper functions

**What NOT to Test:**
- Controllers (covered by integration tests)
- External dependencies (mock them)
- Mongoose methods (tested by Mongoose)

**Example:**
```typescript
// tests/unit/services/game.service.test.ts
describe('GameService', () => {
  let gameService: GameService;

  beforeEach(() => {
    gameService = new GameService();
  });

  describe('rollDice', () => {
    it('should return a result between 1 and 6', () => {
      const result = gameService.rollDice();
      expect(result).toBeGreaterThanOrEqual(1);
      expect(result).toBeLessThanOrEqual(6);
    });

    it('should respect probability weights', () => {
      const outcomes: number[] = [];
      for (let i = 0; i < 1000; i++) {
        outcomes.push(gameService.rollDice());
      }
      // Statistical test for distribution
    });
  });
});
```

### 11.3 Integration Tests

**Coverage Target:** 100% of endpoints

**What to Test:**
- Request/response cycle
- Authentication and authorization
- Validation
- Error handling
- Database operations

**Example:**
```typescript
// tests/integration/landingPage.test.ts
describe('LandingPage API', () => {
  let authToken: string;

  beforeAll(async () => {
    // Setup test user
    const loginResponse = await request(app)
      .post('/api/v1/auth/login')
      .send({ email: 'test@example.com', password: 'password' });
    authToken = loginResponse.body.data.accessToken;
  });

  describe('GET /api/v1/landing-pages', () => {
    it('should return paginated landing pages', async () => {
      const response = await request(app)
        .get('/api/v1/landing-pages')
        .query({ page: 1, limit: 10 })
        .set('Authorization', `Bearer ${authToken}`);

      expect(response.status).toBe(200);
      expect(response.body.success).toBe(true);
      expect(response.body.data).toHaveLength(10);
      expect(response.body.pagination).toBeDefined();
    });

    it('should require authentication', async () => {
      const response = await request(app).get('/api/v1/landing-pages');
      expect(response.status).toBe(401);
    });
  });
});
```

### 11.4 E2E Tests

**Coverage Target:** All critical user flows

**What to Test:**
- Complete user journeys
- Multi-step workflows
- Cross-module interactions

**Example:**
```typescript
// tests/e2e/nfc-tap-flow.test.ts
describe('NFC Tap Flow (E2E)', () => {
  it('should complete full NFC tap to signup flow', async () => {
    // 1. Admin creates landing page
    const landingPage = await createLandingPage();

    // 2. Admin creates NFC card linked to landing page
    const card = await createNFCCard(landingPage.slug);

    // 3. Customer taps NFC card (simulated)
    const pageResponse = await request(app)
      .get(`/api/v1/public/pages/${landingPage.slug}`);
    expect(pageResponse.status).toBe(200);

    // 4. Customer selects promoter
    await request(app)
      .post(`/api/v1/public/pages/${landingPage.slug}/select-promoter`)
      .send({ promoterId: promoter.id });

    // 5. Customer plays game
    const gameResponse = await request(app)
      .post(`/api/v1/public/pages/${landingPage.slug}/game`)
      .send({ deviceFingerprint: 'test-device' });
    expect(gameResponse.status).toBe(200);
    expect(gameResponse.body.data.result).toBeDefined();

    // 6. Customer submits signup
    const signupResponse = await request(app)
      .post(`/api/v1/public/pages/${landingPage.slug}/submit`)
      .send({
        name: 'John Doe',
        email: 'john@example.com',
        consentGiven: true,
      });
    expect(signupResponse.status).toBe(200);

    // 7. Verify data in database
    const participant = await Participant.findOne({ email: 'john@example.com' });
    expect(participant).toBeDefined();
    expect(participant.promoter.toString()).toBe(promoter.id);
  });
});
```

---

## 12. Security Implementation

### 12.1 Authentication Security

**JWT Implementation:**
```typescript
// middleware/authenticate.middleware.ts
import jwt from 'jsonwebtoken';
import { User } from '../modules/user.module/user.model';

export const authenticate = async (req: Request, res: Response, next: NextFunction) => {
  try {
    const token = req.headers.authorization?.replace('Bearer ', '');
    if (!token) {
      throw new AuthenticationError('No token provided');
    }

    const decoded = jwt.verify(token, process.env.JWT_ACCESS_SECRET!) as {
      userId: string;
    };

    const user = await User.findById(decoded.userId).select('-passwordHash').lean();
    if (!user || user.status !== 'active') {
      throw new AuthenticationError('Invalid or inactive user');
    }

    req.user = user;
    next();
  } catch (error) {
    next(new AuthenticationError('Invalid token'));
  }
};
```

**Refresh Token Rotation:**
```typescript
// services/auth.service.ts
async refreshToken(refreshToken: string) {
  // Find session in Redis
  const session = await redisClient.get(`session:token:${refreshToken}`);
  if (!session) {
    throw new AuthenticationError('Invalid refresh token');
  }

  // Check for reuse (token already used)
  if (session.used) {
    // Reuse detected - invalidate entire session
    await this.invalidateSession(session.userId);
    throw new AuthenticationError('Token reuse detected');
  }

  // Generate new tokens
  const newAccessToken = this.generateAccessToken(session.userId);
  const newRefreshToken = generateRandomToken();

  // Update session
  await redisClient.setEx(
    `session:token:${newRefreshToken}`,
    7 * 24 * 60 * 60,
    JSON.stringify({ ...session, used: false })
  );

  // Mark old token as used
  await redisClient.setEx(
    `session:token:${refreshToken}`,
    60,
    JSON.stringify({ ...session, used: true })
  );

  return { accessToken: newAccessToken, refreshToken: newRefreshToken };
}
```

### 12.2 Input Validation

**Zod Validation Schema Example:**
```typescript
// modules/participant.module/participant.validation.ts
import { z } from 'zod';

export const createParticipantSchema = z.object({
  name: z.string().trim().min(1).max(100),
  email: z.string().email().toLowerCase(),
  phone: z.string().regex(/^\+?[1-9]\d{1,14}$/).optional().nullable(),
  promoter: z.string().regex(/^[0-9a-fA-F]{24}$/).optional().nullable(),
  consentGiven: z.boolean(),
});

// Usage in route
router.post(
  '/submit',
  validateRequest(createParticipantSchema),
  participantController.submitForm
);
```

### 12.3 Rate Limiting

```typescript
// middleware/rateLimiter.middleware.ts
import rateLimit from 'express-rate-limit';
import RedisStore from 'rate-limit-redis';
import { redisClient } from '../config/redis';

export const rateLimiters = {
  public: rateLimit({
    windowMs: 60 * 1000,
    max: 100,
    standardHeaders: true,
    store: new RedisStore({
      sendCommand: (...args: string[]) => redisClient.sendCommand(args),
    }),
    keyGenerator: (req) => req.ip,
  }),
  auth: rateLimit({
    windowMs: 60 * 1000,
    max: 5,
    standardHeaders: true,
    store: new RedisStore({
      sendCommand: (...args: string[]) => redisClient.sendCommand(args),
    }),
    keyGenerator: (req) => req.ip,
  }),
  user: rateLimit({
    windowMs: 60 * 1000,
    max: 100,
    standardHeaders: true,
    store: new RedisStore({
      sendCommand: (...args: string[]) => redisClient.sendCommand(args),
    }),
    keyGenerator: (req) => req.user?.id || req.ip,
  }),
};
```

### 12.4 Security Headers

```typescript
// app.ts
import helmet from 'helmet';

app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      scriptSrc: ["'self'"],
      imgSrc: ["'self'", 'data:', 'https:'],
    },
  },
  crossOriginEmbedderPolicy: false,
}));
```

---

## 13. Observability & Monitoring

### 13.1 Request Logging

```typescript
// middleware/requestLogger.middleware.ts
import { randomUUID } from 'crypto';
import { logger } from '../utils/logger';

export const requestLogger = (req: Request, res: Response, next: NextFunction) => {
  const correlationId = req.headers['x-correlation-id'] as string || randomUUID();
  const startTime = Date.now();

  res.setHeader('X-Correlation-ID', correlationId);

  res.on('finish', () => {
    const responseTime = Date.now() - startTime;
    logger.info({
      correlationId,
      method: req.method,
      route: req.route?.path || req.url,
      statusCode: res.statusCode,
      responseTimeMs: responseTime,
      userId: req.user?.id,
      ip: req.ip,
    }, 'Request completed');
  });

  next();
};
```

### 13.2 Health Check Endpoint

```typescript
// routes/health.route.ts
import { Router } from 'express';
import mongoose from 'mongoose';
import { redisClient } from '../config/redis';
import { queueService } from '../services/queue.service';

const router = Router();

router.get('/health', async (req, res) => {
  const status: any = {
    status: 'healthy',
    db: 'disconnected',
    redis: 'disconnected',
    queues: {},
  };

  // Check MongoDB
  if (mongoose.connection.readyState === 1) {
    status.db = 'connected';
  } else {
    status.status = 'degraded';
  }

  // Check Redis
  try {
    await redisClient.ping();
    status.redis = 'connected';
  } catch {
    status.status = 'degraded';
  }

  // Check Queues
  try {
    status.queues = await queueService.getQueueStatus();
  } catch {
    status.status = 'degraded';
  }

  // If critical services down
  if (status.db === 'disconnected' || status.redis === 'disconnected') {
    status.status = 'down';
    return res.status(503).json(status);
  }

  res.json(status);
});

export default router;
```

### 13.3 Error Tracking

```typescript
// middleware/errorHandler.middleware.ts
import { logger } from '../utils/logger';

export const errorHandler = (err: Error, req: Request, res: Response, next: NextFunction) => {
  // Log error with full context
  logger.error({
    correlationId: req.headers['x-correlation-id'],
    method: req.method,
    route: req.route?.path || req.url,
    userId: req.user?.id,
    error: {
      message: err.message,
      stack: err.stack,
    },
  }, 'Unhandled error');

  // Send error response
  const statusCode = err.statusCode || 500;
  res.status(statusCode).json({
    success: false,
    error: {
      code: err.code || 'INTERNAL_ERROR',
      message: statusCode === 500 ? 'Internal server error' : err.message,
    },
  });
};
```

---

## 14. Deployment Architecture

### 14.1 Infrastructure Setup

```yaml
Production Environment:
  - Load Balancer: AWS ALB / NGINX
  - App Servers: 3+ EC2 instances (auto-scaling)
  - MongoDB: M10+ cluster (3-node replica set)
  - Redis: ElastiCache cluster (2 nodes)
  - File Storage: S3
  - CDN: Cloudflare
  - Monitoring: Datadog / New Relic
  - Error Tracking: Sentry
  - CI/CD: GitHub Actions
```

### 14.2 Docker Configuration

```dockerfile
# Dockerfile
FROM node:20-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY --from=builder /app/dist ./dist

EXPOSE 3000

CMD ["node", "dist/server.js"]
```

```yaml
# docker-compose.yml (development)
version: '3.8'

services:
  app:
    build: .
    ports:
      - '3000:3000'
    environment:
      - NODE_ENV=development
      - MONGODB_URI=mongodb://mongo:27017/promotercard
      - REDIS_URL=redis://redis:6379
    depends_on:
      - mongo
      - redis

  mongo:
    image: mongo:7
    ports:
      - '27017:27017'
    volumes:
      - mongo-data:/data/db

  redis:
    image: redis:7-alpine
    ports:
      - '6379:6379'

volumes:
  mongo-data:
```

### 14.3 CI/CD Pipeline

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run lint
      - run: npm test

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: docker build -t promotercard-backend .
      - run: docker tag promotercard-backend registry.example.com/promotercard-backend:latest
      - run: docker push registry.example.com/promotercard-backend:latest

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: |
          ssh deploy@example.com "
            docker pull registry.example.com/promotercard-backend:latest
            docker stop promotercard-backend || true
            docker run -d --name promotercard-backend \
              --restart always \
              -p 3000:3000 \
              -e NODE_ENV=production \
              -e MONGODB_URI=$MONGODB_URI \
              -e REDIS_URL=$REDIS_URL \
              registry.example.com/promotercard-backend:latest
          "
```

---

## 15. Team Structure

### 15.1 Recommended Team

| Role | Count | Responsibilities |
|------|-------|------------------|
| Senior Backend Developer | 2 | Core development, architecture, code review |
| Backend Developer | 2 | Module implementation, testing |
| DevOps Engineer | 1 | Infrastructure, CI/CD, monitoring |
| QA Engineer | 1 | Testing, quality assurance |
| Technical Lead | 1 | Architecture decisions, sprint planning |

### 15.2 Sprint Structure

- **Sprint Duration:** 2 weeks
- **Sprint Planning:** Day 1 of each sprint
- **Daily Standup:** 15 minutes
- **Sprint Review:** Last day of sprint
- **Sprint Retrospective:** After review

---

## 16. Risk Mitigation

### 16.1 Risk Register

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| MongoDB performance degradation | High | Medium | Index optimization, query monitoring, read replicas |
| Redis cluster failure | High | Low | Redis Sentinel, automatic failover |
| BullMQ queue backlog | Medium | Medium | Queue monitoring, auto-scaling workers |
| NFC UUID collision | High | Low | Unique constraint, validation |
| Game fraud/abuse | High | High | Rate limiting, device fingerprinting, pattern detection |
| Data breach | Critical | Low | Encryption, access controls, security audit |
| API rate limit abuse | Medium | Medium | Sliding window, IP + user-based limits |
| Third-party API failure | Medium | Medium | Fallback mechanisms, circuit breakers |

### 16.2 Contingency Plans

**Database Issues:**
- Enable MongoDB Atlas alerts for slow queries
- Regular index review (weekly)
- Query optimization on demand

**Queue Issues:**
- Monitor queue depth (alert if > 1000 jobs)
- Auto-scale workers based on queue depth
- Dead letter queue for failed jobs

**Security Issues:**
- Regular security audits (quarterly)
- Dependency updates (monthly)
- Penetration testing (annually)

---

## 17. Code Quality Standards

### 17.1 Linting Rules

```json
{
  "extends": ["eslint:recommended", "plugin:@typescript-eslint/recommended"],
  "rules": {
    "no-console": "error",
    "@typescript-eslint/no-explicit-any": "error",
    "@typescript-eslint/no-unused-vars": "error",
    "@typescript-eslint/explicit-function-return-type": "warn",
    "prefer-const": "error"
  }
}
```

### 17.2 Git Hooks

```json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "pre-push": "npm test"
    }
  },
  "lint-staged": {
    "src/**/*.ts": ["eslint --fix", "prettier --write"]
  }
}
```

### 17.3 Code Review Checklist

Before approving PR:
- [ ] Follows folder structure
- [ ] Uses generic controller/service where applicable
- [ ] All endpoints have validation
- [ ] All list endpoints have pagination
- [ ] Rate limiting applied
- [ ] Error handling implemented
- [ ] Logging added
- [ ] Documentation updated
- [ ] Tests written
- [ ] No console.log
- [ ] No any types
- [ ] Indexes defined for new queries

---

## 18. Documentation Standards

### 18.1 Module Documentation Requirements

Every module MUST have `/doc` folder containing:

**README.md:**
- Module purpose (2-3 lines)
- List of responsibilities
- API examples (request + response)
- System flow description
- Links to diagram files

**Diagrams (`/doc/dia/`):**
- `<module>-schema.mermaid` - Database schema
- `<module>-system-flow.mermaid` - System flow
- `<module>-swimlane.mermaid` - Swimlane diagram
- `<module>-user-flow.mermaid` - User flow
- `<module>-system-architecture.mermaid` - Architecture
- `<module>-state-machine.mermaid` - State transitions
- `<module>-sequence.mermaid` - Sequence diagram
- `<module>-component-architecture.mermaid` - Components

**Performance Report (`/doc/perf/`):**
- Time complexity (Big O notation)
- Space complexity
- Memory efficiency notes
- Redis cache strategy
- MongoDB index strategy
- Horizontal scaling considerations

### 18.2 File Naming Conventions

| File Type | Format |
|-----------|--------|
| Agenda files | `agenda-DD-MM-YY-XXX-V1.md` |
| Mermaid diagrams | `<module>-<diagram-type>.mermaid` |
| Performance reports | `<module>-performance-report.md` |
| Postman collections | `<project>-postman-collection.json` |
| Implementation logs | `<feature>-IMPLEMENTATION-COMPLETE.md` |

---

## 19. Sprint Breakdown

### Sprint 1: Foundation (Week 1)

**Goals:**
- Project scaffolding
- Core utilities (generic controller, service, pagination, cache, queue)
- Middleware stack
- Docker setup
- Health check

**Deliverables:**
- Complete Phase 0

**Story Points:** 13

---

### Sprint 2: Authentication (Week 1-2)

**Goals:**
- Auth module
- User module
- JWT implementation
- Email integration

**Deliverables:**
- Complete Phase 1

**Story Points:** 13

---

### Sprint 3: Landing Pages (Week 2-3)

**Goals:**
- Landing page module
- Public landing page endpoint

**Deliverables:**
- Complete Phase 2

**Story Points:** 13

---

### Sprint 4: Promoters & Campaigns (Week 3-5)

**Goals:**
- Promoter module
- Campaign module
- Event module

**Deliverables:**
- Complete Phase 3-4

**Story Points:** 13

---

### Sprint 5: Cards & Participants (Week 5-7)

**Goals:**
- Card module
- Card interaction tracking
- Participant module

**Deliverables:**
- Complete Phase 5-6

**Story Points:** 13

---

### Sprint 6: Game Engine (Week 7-8)

**Goals:**
- Game module
- Prize module
- Fraud prevention

**Deliverables:**
- Complete Phase 7

**Story Points:** 13

---

### Sprint 7: Reviews & Social (Week 8-9)

**Goals:**
- Review module
- Social module
- Click tracking

**Deliverables:**
- Complete Phase 8

**Story Points:** 8

---

### Sprint 8: Analytics & Reports (Week 9-11)

**Goals:**
- Analytics module
- Report module
- Export functionality

**Deliverables:**
- Complete Phase 9-10

**Story Points:** 13

---

### Sprint 9: Integration & Testing (Week 11-13)

**Goals:**
- Third-party integrations
- Unit tests
- Integration tests
- E2E tests
- Performance optimization

**Deliverables:**
- Complete Phase 11-12

**Story Points:** 13

---

### Sprint 10: Deployment & Launch (Week 13-14)

**Goals:**
- Production setup
- CI/CD pipeline
- Monitoring
- Launch

**Deliverables:**
- Complete Phase 13

**Story Points:** 8

---

**Total Story Points:** 118  
**Total Duration:** 14 weeks (~3.5 months)

---

## 20. Definition of Done

### 20.1 Module Completion Checklist

A module is considered "Done" when ALL of the following are complete:

**Code:**
- [ ] Model defined with proper indexes
- [ ] Validation schemas (Zod) implemented
- [ ] Service implementation complete
- [ ] Controller implementation complete
- [ ] Routes defined with middleware
- [ ] Generic controller/service used where applicable
- [ ] Error handling implemented
- [ ] Logging added
- [ ] Rate limiting applied
- [ ] No console.log statements
- [ ] No any types
- [ ] All functions have explicit return types

**Caching:**
- [ ] Cache keys defined with correct naming
- [ ] TTL values set per data type
- [ ] Cache invalidation on write operations
- [ ] Redis sorted sets for counts/leaderboards

**Queues:**
- [ ] Heavy operations moved to BullMQ
- [ ] Queue configuration (attempts, backoff, removeOnComplete, removeOnFail)
- [ ] Job failure logging implemented
- [ ] Queue names as constants

**Security:**
- [ ] Input validation on 100% of endpoints
- [ ] Sensitive fields excluded from responses
- [ ] Rate limiting applied
- [ ] Authentication/authorization implemented

**Database:**
- [ ] All query fields have indexes
- [ ] .lean() used on read-only queries
- [ ] No $lookup chain deeper than 2 levels
- [ ] TTL indexes for expiring data

**Documentation:**
- [ ] README.md in /doc folder
- [ ] All Mermaid diagrams created
- [ ] Performance report completed
- [ ] API examples documented

**Testing:**
- [ ] Unit tests written (> 80% coverage)
- [ ] Integration tests written (100% endpoints)
- [ ] E2E tests for critical flows
- [ ] All tests passing

**Observability:**
- [ ] Request logging includes correlationId + responseTime
- [ ] Error tracking captures full context
- [ ] Health check covers module dependencies

**Process:**
- [ ] Code reviewed by senior developer
- [ ] PR approved and merged
- [ ] Documentation updated
- [ ] Module tracker updated

---

## Appendix A: Environment Variables

```env
# Application
NODE_ENV=production
PORT=3000
API_VERSION=v1

# MongoDB
MONGODB_URI=mongodb://localhost:27017/promotercard
MONGO_MAX_POOL_SIZE=100
MONGO_MIN_POOL_SIZE=10

# Redis
REDIS_URL=redis://localhost:6379

# JWT
JWT_ACCESS_SECRET=your-access-secret
JWT_REFRESH_SECRET=your-refresh-secret

# Email
SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_USER=noreply@example.com
SMTP_PASS=your-password

# File Storage
AWS_S3_BUCKET=promotercard-uploads
AWS_ACCESS_KEY_ID=your-key
AWS_SECRET_ACCESS_KEY=your-secret

# Rate Limiting
RATE_LIMIT_PUBLIC=100
RATE_LIMIT_AUTH=5
RATE_LIMIT_USER=100
RATE_LIMIT_ADMIN=200

# Monitoring
SENTRY_DSN=your-sentry-dsn
DATADOG_API_KEY=your-datadog-key
```

---

## Appendix B: API Response Format

```typescript
// Success Response
{
  "success": true,
  "data": { /* response data */ },
  "pagination": { /* if applicable */ },
  "meta": {
    "timestamp": "2026-04-07T10:30:00Z",
    "requestId": "req_abc123"
  }
}

// Error Response
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      { "field": "email", "message": "Invalid email format" }
    ]
  },
  "meta": {
    "timestamp": "2026-04-07T10:30:00Z",
    "requestId": "req_abc123"
  }
}

// Accepted Response (for async operations)
{
  "success": true,
  "data": {
    "jobId": "job_abc123",
    "message": "Operation started. Check status with GET /resource/:id"
  },
  "meta": {
    "timestamp": "2026-04-07T10:30:00Z",
    "requestId": "req_abc123"
  }
}
```

---

## Appendix C: Key Performance Indicators

### Technical KPIs

| KPI | Target | Measurement |
|-----|--------|-------------|
| API Response Time (p95) | < 200ms | APM monitoring |
| API Response Time (p99) | < 500ms | APM monitoring |
| Cache Hit Rate | > 80% | Redis stats |
| Error Rate | < 0.1% | Error tracking |
| Queue Depth | < 1000 jobs | BullMQ monitoring |
| Job Failure Rate | < 5% | BullMQ monitoring |
| Database Query Time (p95) | < 50ms | MongoDB profiler |
| Connection Pool Utilization | < 80% | MongoDB metrics |

### Business KPIs

| KPI | Target | Measurement |
|-----|--------|-------------|
| Landing Page Load Time | < 2 seconds | Performance monitoring |
| Game Completion Rate | > 80% | Analytics |
| Sign-Up Conversion Rate | > 30% | Analytics |
| Review Click Rate | > 25% | Analytics |
| Promoter Adoption Rate | > 90% | Analytics |

---

**End of Backend Development Plan**

**Document Version:** 1.0  
**Last Updated:** April 7, 2026  
**Next Review:** Upon completion of Sprint 1
