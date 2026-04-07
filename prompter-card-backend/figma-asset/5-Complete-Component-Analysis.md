# PromoterCard - Complete Component Analysis

**Project:** PromoterCard - NFC-Powered Event Promotion Platform  
**Version:** 1.0  
**Last Updated:** April 7, 2026  
**Status:** Complete System Analysis  
**Analysis Type:** Senior-Level Production-Grade  

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [System Overview](#2-system-overview)
3. [User Flow Analysis](#3-user-flow-analysis)
4. [Component Breakdown by Module](#4-component-breakdown-by-module)
5. [API Endpoint Analysis](#5-api-endpoint-analysis)
6. [Data Flow Analysis](#6-data-flow-analysis)
7. [Integration Points](#7-integration-points)
8. [Missing Components & Recommendations](#8-missing-components--recommendations)
9. [Technical Debt & Risks](#9-technical-debt--risks)
10. [Implementation Priority Matrix](#10-implementation-priority-matrix)

---

## 1. Executive Summary

### 1.1 What This Document Covers

This is a **complete, production-grade component analysis** of the PromoterCard platform. It covers:

- ✅ **All user flows** (Admin, Promoter, Customer)
- ✅ **All backend components** (APIs, services, models, queues)
- ✅ **All frontend components** (existing designs + missing designs)
- ✅ **Integration points** (Google, NFC, Email, CDN)
- ✅ **Data flow** (how data moves through the system)
- ✅ **Missing components** (what needs to be built)
- ✅ **Technical risks** (what could go wrong)

### 1.2 System Architecture at a Glance

```
┌─────────────────────────────────────────────────────────────────────┐
│                         PROMOTERCARD PLATFORM                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐          │
│  │   ADMIN      │    │   PROMOTER   │    │   CUSTOMER   │          │
│  │  DASHBOARD   │    │  DASHBOARD   │    │  LANDING PG  │          │
│  │  (Web App)   │    │  (Web/Mobile)│    │  (Web Page)  │          │
│  └──────┬───────┘    └──────┬───────┘    └──────┬───────┘          │
│         │                   │                   │                   │
│         └───────────────────┼───────────────────┘                   │
│                             │                                       │
│                    ┌────────▼────────┐                              │
│                    │   BACKEND API   │                              │
│                    │  (Node.js/TS)   │                              │
│                    └────────┬────────                              │
│                             │                                       │
│         ┌───────────────────┼───────────────────┐                   │
│         │                   │                   │                   │
│    ┌────▼────┐       ┌─────▼─────┐      ┌──────▼──────┐           │
│    │ MongoDB │       │   Redis   │      │   BullMQ    │           │
│    │  (DB)   │       │  (Cache)  │      │  (Queues)   │           │
│    └─────────┘       └───────────┘      └─────────────┘           │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                  EXTERNAL INTEGRATIONS                       │   │
│  │  Google Business API  │  Email Service  │  NFC Supplier     │   │
│  │  Cloud Storage (S3)   │  CDN (CDN)      │  Payment Gateway  │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. System Overview

### 2.1 Platform Purpose

**PromoterCard** is a web platform that replaces traditional flyer-based event promotion with interactive NFC-enabled cards linked to customizable landing pages. The system enables:

1. **Businesses** to manage promoters, campaigns, and analytics
2. **Promoters** to engage customers with NFC cards and track their performance
3. **Customers** to interact with games, sign up for events, and leave reviews

### 2.2 Core Workflow

```
1. Admin creates a landing page with event details, games, and prizes
2. Admin creates NFC cards and assigns them to promoters
3. Promoter distributes NFC cards to potential customers at events
4. Customer taps NFC card → lands on event page
5. Customer plays game (dice/spin) → wins prize
6. Customer signs up for guest list → provides email/phone
7. Customer leaves Google review → gets additional prize
8. System tracks all interactions and generates analytics
9. Admin views dashboard to measure campaign effectiveness
```

### 2.3 Technology Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| **Frontend (Admin)** | React/Next.js (existing designs) | Admin dashboard UI |
| **Frontend (Promoter)** | TBD (needs design) | Promoter dashboard UI |
| **Frontend (Customer)** | TBD (needs design) | Landing pages UI |
| **Backend** | Node.js + TypeScript + Express.js | API server |
| **Database** | MongoDB v7.x | Primary data store |
| **Cache** | Redis v7.x | Session, cache, queues |
| **Queue** | BullMQ v5.x | Async operations |
| **Auth** | JWT + bcrypt | Authentication |
| **Validation** | Zod v3.x | Input validation |
| **Logging** | Winston v3.x | Structured logging |
| **Storage** | S3/Cloud Storage | File uploads |
| **CDN** | Cloudflare | Static assets, landing pages |

---

## 3. User Flow Analysis

### 3.1 Admin User Flow

**Role:** Business owner or manager  
**Access:** Full system access  

```
┌─────────────────────────────────────────────────────────────────────┐
│                        ADMIN USER FLOW                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  START                                                                │
│    │                                                                  │
│    ▼                                                                  │
│  ┌─────────────────┐                                                 │
│  │  Login/Register │                                                 │
│  │  (Auth Module)  │                                                 │
│  └────────┬────────                                                 │
│           │                                                          │
│           ▼                                                          │
│  ┌─────────────────┐                                                 │
│  │ Admin Dashboard │                                                 │
│  │  - Overview     │                                                 │
│  │  - Metrics      │                                                 │
│  │  - Leaderboard  │                                                 │
│  └────────┬────────┘                                                 │
│           │                                                          │
│     ┌─────┴─────────────────────────────────────────────┐           │
│     │                                                   │           │
│     ▼                                                   ▼           │
│  ┌──────────────┐                              ┌──────────────┐    │
│  │ Manage       │                              │ View         │    │
│  │ Promoters    │                              │ Analytics    │    │
│  │              │                              │              │    │
│  │ - Create     │                              │ - Overview   │    │
│  │ - Edit       │                              │ - Trends     │    │
│  │ - Delete     │                              │ - Reports    │    │
│  │ - Assign     │                              │ - Exports    │    │
│  │   Cards      │                              │              │    │
│  └──────────────┘                              └──────────────┘    │
│     │                                                   │           │
│     ▼                                                   ▼           │
│  ┌──────────────┐                              ┌──────────────┐    │
│  │ Create       │                              │ Generate     │    │
│  │ Campaigns    │                              │ Reports      │    │
│  │              │                              │              │    │
│  │ - Events     │                              │ - Monthly    │    │
│  │ - Landing    │                              │ - Audience   │    │
│  │   Pages      │                              │ - Engagement │    │
│  │ - Assign     │                              │ - Game Perf  │    │
│  │   Promoters  │                              │              │    │
│  └──────────────┘                              └──────────────┘    │
│                                                                       │
│  END                                                                  │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

**Admin Flow Breakdown:**

1. **Authentication Flow:**
   - Register (email, password, business details)
   - Login (JWT access token + refresh token)
   - Password reset
   - Email verification

2. **Dashboard Flow:**
   - View performance overview (interactions, participants, game plays, signups, reviews)
   - View promoter leaderboard
   - View trends and insights

3. **Promoter Management Flow:**
   - Create promoter (name, nickname, email, phone)
   - Edit promoter details
   - Delete/deactivate promoter
   - Assign NFC cards to promoter
   - View promoter performance stats

4. **Campaign Management Flow:**
   - Create campaign (name, dates, venue)
   - Create events within campaign
   - Assign landing page to campaign
   - Assign promoters to campaign
   - Track campaign progress
   - View campaign analytics

5. **Landing Page Management Flow:**
   - Create landing page (slug, title)
   - Configure page blocks (hero, event details, description, game, social links, contact info)
   - Assign promoters to landing page
   - Publish/unpublish landing page
   - Duplicate landing page
   - Preview landing page

6. **Card Management Flow:**
   - Create NFC card (name, type, assign to promoter)
   - Generate QR code
   - Assign card to promoter
   - View card interactions
   - Track card performance

7. **Analytics Flow:**
   - View interaction analytics (total, unique, return visitors)
   - View peak activity hours
   - View geographic distribution
   - View card performance
   - View promoter leaderboard
   - View trends over time

8. **Reporting Flow:**
   - Generate monthly performance report
   - Generate audience data export
   - Generate engagement report
   - Generate game performance report
   - Download reports (PDF, CSV, Excel, JSON)

---

### 3.2 Promoter User Flow

**Role:** Field staff who distribute NFC cards  
**Access:** Limited to personal data and assigned cards  

```
┌─────────────────────────────────────────────────────────────────────┐
│                       PROMOTER USER FLOW                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  START                                                                │
│    │                                                                  │
│    ▼                                                                  │
│  ┌─────────────────┐                                                 │
│  │  Login          │                                                 │
│  │  (Auth Module)  │                                                 │
│  └────────┬────────┘                                                 │
│           │                                                          │
│           ▼                                                          │
│  ┌─────────────────┐                                                 │
│  │Promoter Dashboard│                                                │
│  │  - My Stats     │                                                 │
│  │  - My Cards     │                                                 │
│  │  - My Events    │                                                 │
│  └────────┬────────                                                 │
│           │                                                          │
│     ┌─────┴─────────────────────────────────────────────┐           │
│     │                                                   │           │
│     ▼                                                   ▼           │
│  ┌──────────────┐                              ┌──────────────┐    │
│  │ View         │                              │ Manage       │    │
│  │ Performance  │                              │ Cards        │    │
│  │              │                              │              │    │
│  │ - My Rank    │                              │ - View       │    │
│  │ - Interactions│                             │   Assigned   │    │
│  │ - Signups    │                              │   Cards      │    │
│  │ - Reviews    │                              │ - View       │    │
│  │ - Trends     │                              │   Interactions│    │
│  └──────────────┘                              └──────────────┘    │
│     │                                                   │           │
│     ▼                                                   ▼           │
│  ┌──────────────┐                              ┌──────────────┐    │
│  │ View         │                              │ Validate     │    │
│  │ Events       │                              │ Prizes       │    │
│  │              │                              │              │    │
│  │ - Upcoming   │                              │ - Scan/Verify│    │
│  │ - Past       │                              │ - Mark       │    │
│  │ - Details    │                              │   Redeemed   │    │
│  │ - My Role    │                              │              │    │
│  └──────────────┘                              └──────────────┘    │
│                                                                       │
│  END                                                                  │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

**Promoter Flow Breakdown:**

1. **Authentication Flow:**
   - Login (email, password - credentials provided by admin)
   - View personal dashboard
   - Update profile (name, nickname, phone)
   - Change password

2. **Dashboard Flow:**
   - View personal stats (interactions, signups, reviews)
   - View ranking among team
   - View recent activity feed
   - View upcoming events

3. **Card Management Flow:**
   - View assigned NFC cards
   - View card interaction count
   - View last tap time
   - Download QR code (if applicable)

4. **Event Tracking Flow:**
   - View assigned campaigns/events
   - View event details (date, time, venue)
   - View real-time interaction count during events
   - View post-event performance summary

5. **Prize Redemption Flow:**
   - Validate prize claims from customers
   - Scan/verify prize codes
   - Mark prizes as redeemed
   - View redemption history

6. **Profile Management Flow:**
   - Update personal information
   - Update nickname (for review matching)
   - View activity history
   - View performance history

---

### 3.3 Customer User Flow

**Role:** End user who interacts with NFC cards  
**Access:** Public landing pages only (no authentication required)  

```
┌─────────────────────────────────────────────────────────────────────┐
│                       CUSTOMER USER FLOW                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  START                                                                │
│    │                                                                  │
│    ▼                                                                  │
│  ┌─────────────────┐                                                 │
│  │  Tap NFC Card   │                                                 │
│  │  or Scan QR     │                                                 │
│  └────────┬────────┘                                                 │
│           │                                                          │
│           ▼                                                          │
│  ┌─────────────────┐                                                 │
│  │  Landing Page   │                                                 │
│  │  Load           │                                                 │
│  └────────┬────────┘                                                 │
│           │                                                          │
│     ┌──────────────────────────────────────────────────┐           │
│     │                                                   │           │
│     ▼                                                   ▼           │
│  ┌──────────────┐                              ┌──────────────┐    │
│  │ Select       │                              │ Play Game    │    │
│  │ Promoter     │                              │              │    │
│  │              │                              │ - Dice Roll  │    │
│  │ (Who referred│                              │ - Spin Wheel │    │
│  │  you?)       │                              │              │    │
│  └──────────────┘                              └──────┬───────┘    │
│     │                                                │              │
│     ▼                                                ▼              │
│  ┌──────────────┐                              ┌──────────────┐    │
│  │ Submit       │                              │ View Prize   │    │
│  │ Sign-Up Form │                              │ Won          │    │
│  │              │                              │              │    │
│  │ - Name       │                              │ - Prize Name │    │
│  │ - Email      │                              │ - Redemption │    │
│  │ - Phone      │                              │   Instructions│    │
│  │ - Consent    │                              │              │    │
│  └──────────────┘                              └──────┬───────┘    │
│     │                                                │              │
│     ▼                                                ▼              │
│  ┌──────────────┐                              ┌──────────────┐    │
│  │ Leave        │                              │ Redeem Prize │    │
│  │ Google       │                              │              │    │
│  │ Review       │                              │ - Show prize │    │
│  │              │                              │   to staff   │    │
│  │ - Click      │                              │ - Staff      │    │
│  │   Review URL │                              │   validates  │    │
│  │ - Go to      │                              │              │    │
│  │   Google     │                              │              │    │
│  └──────────────┘                              └──────────────┘    │
│                                                                       │
│  END                                                                  │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

**Customer Flow Breakdown:**

1. **Landing Page Access Flow:**
   - Tap NFC card (triggers URL redirect to landing page)
   - Scan QR code (triggers URL redirect to landing page)
   - Direct link access (shared URL)
   - Landing page loads with event details, game, and social links

2. **Promoter Selection Flow:**
   - Customer sees "Who referred you?" prompt
   - Customer selects promoter from dropdown
   - System tracks promoter selection for attribution

3. **Game Interaction Flow:**
   - Customer sees game section (dice roll or spin wheel)
   - Customer clicks "Play" button
   - Game animation plays
   - Prize result is displayed
   - Prize is stored in system for redemption

4. **Sign-Up Flow:**
   - Customer sees sign-up form
   - Customer enters name, email, phone
   - Customer checks consent checkbox (GDPR compliance)
   - Form submits to backend
   - Confirmation message displayed

5. **Review Flow:**
   - Customer sees review prompt after game/sign-up
   - Customer clicks "Leave a Review" button
   - System tracks review click
   - Customer redirected to Google review page
   - Customer leaves review on Google (external)

6. **Prize Redemption Flow:**
   - Customer shows prize won to staff
   - Staff validates prize (via promoter dashboard or manual check)
   - Staff marks prize as redeemed
   - Customer receives prize

---

## 4. Component Breakdown by Module

### 4.1 Auth Module

**Purpose:** User authentication and session management  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `auth.model.ts` | Model | ✅ Planned | Session model for token tracking |
| `auth.service.ts` | Service | ✅ Planned | Login, logout, refresh, password reset logic |
| `auth.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `auth.route.ts` | Route | ✅ Planned | API route definitions |
| `auth.validation.ts` | Validation | ✅ Planned | Zod schemas for auth endpoints |
| `auth.middleware.ts` | Middleware | ✅ Planned | Custom auth middleware (if needed) |
| `auth.constant.ts` | Constants | ✅ Planned | Auth-related constants and enums |
| `auth.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `auth.test.ts` | Test | ✅ Planned | Unit and integration tests |

**API Endpoints:**

```
POST   /api/v1/auth/register          - Register admin user
POST   /api/v1/auth/login             - Login and receive JWT
POST   /api/v1/auth/logout            - Invalidate session
POST   /api/v1/auth/refresh           - Refresh JWT token
POST   /api/v1/auth/forgot-password   - Request password reset
POST   /api/v1/auth/reset-password    - Reset password with token
GET    /api/v1/auth/me                - Get current user profile
PUT    /api/v1/auth/profile           - Update current user profile
PUT    /api/v1/auth/password          - Change password
```

**Data Flow:**

```
1. User submits login credentials
2. Backend validates email/password
3. Backend generates JWT access token (15 min expiry)
4. Backend generates refresh token (7 days)
5. Backend stores refresh token in Redis
6. Backend returns access token + refresh token to client
7. Client stores tokens
8. Client uses access token for API requests
9. When access token expires, client uses refresh token to get new access token
10. On logout, backend invalidates refresh token in Redis
```

**Redis Keys:**

```
session:token:{refreshToken}     - Refresh token session (TTL: 7 days)
blacklist:{accessToken}          - Blacklisted access token (TTL: 15 min)
otp:verification:{email}         - Email verification OTP (TTL: 10 min)
otp:reset-password:{email}       - Password reset OTP (TTL: 10 min)
```

**BullMQ Jobs:**

```
critical-queue:
  - send-verification-email
  - send-password-reset-email
  - send-welcome-email
```

---

### 4.2 User Module

**Purpose:** User management (admin users and promoters)  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `user.model.ts` | Model | ✅ Planned | User model with roles |
| `user.service.ts` | Service | ✅ Planned | User CRUD operations |
| `user.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `user.route.ts` | Route | ✅ Planned | API route definitions |
| `user.validation.ts` | Validation | ✅ Planned | Zod schemas for user endpoints |
| `user.constant.ts` | Constants | ✅ Planned | User-related constants and enums |
| `user.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `user.test.ts` | Test | ✅ Planned | Unit and integration tests |

**API Endpoints:**

```
GET    /api/v1/users                   - List users with pagination
POST   /api/v1/users                   - Create user (admin)
GET    /api/v1/users/:id               - Get user details
PUT    /api/v1/users/:id               - Update user
DELETE /api/v1/users/:id               - Deactivate user
PUT    /api/v1/users/:id/role          - Update user role
```

**Data Flow:**

```
1. Admin creates user (email, name, role)
2. Backend validates input
3. Backend generates password hash
4. Backend creates user in MongoDB
5. Backend sends welcome email (via BullMQ)
6. User receives email with temporary password
7. User logs in and changes password
```

**Redis Keys:**

```
user:profile:{userId}     - User profile cache (TTL: 15 min)
```

---

### 4.3 Landing Page Module

**Purpose:** Landing page CRUD and public page serving  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `landingPage.model.ts` | Model | ✅ Planned | LandingPage model |
| `landingPage.service.ts` | Service | ✅ Planned | LandingPage CRUD operations |
| `landingPage.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `landingPage.route.ts` | Route | ✅ Planned | API route definitions |
| `landingPage.validation.ts` | Validation | ✅ Planned | Zod schemas |
| `landingPage.constant.ts` | Constants | ✅ Planned | LandingPage constants |
| `landingPage.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `landingPage.test.ts` | Test | ✅ Planned | Tests |
| `publicLandingPage.service.ts` | Service | ✅ Planned | Public page serving logic |
| `publicLandingPage.controller.ts` | Controller | ✅ Planned | Public page handlers |
| `publicLandingPage.route.ts` | Route | ✅ Planned | Public page routes |

**API Endpoints:**

```
# Admin Endpoints
GET    /api/v1/landing-pages                  - List landing pages
POST   /api/v1/landing-pages                  - Create landing page
GET    /api/v1/landing-pages/:id              - Get landing page details
PUT    /api/v1/landing-pages/:id              - Update landing page
DELETE /api/v1/landing-pages/:id              - Delete landing page
POST   /api/v1/landing-pages/:id/duplicate    - Duplicate landing page
POST   /api/v1/landing-pages/:id/publish      - Publish landing page
POST   /api/v1/landing-pages/:id/unpublish    - Unpublish landing page

# Public Endpoints (no auth required)
GET    /api/v1/public/pages/:slug             - Get landing page content
POST   /api/v1/public/pages/:slug/submit      - Submit sign-up form
POST   /api/v1/public/pages/:slug/game        - Play game
POST   /api/v1/public/pages/:slug/review      - Track review click
POST   /api/v1/public/pages/:slug/social/:id  - Track social link click
POST   /api/v1/public/pages/:slug/select-promoter - Select promoter
```

**Data Flow:**

```
Admin Flow:
1. Admin creates landing page (slug, title)
2. Admin configures page blocks (hero, event details, game, social links, etc.)
3. Admin assigns promoters to landing page
4. Admin publishes landing page
5. Landing page is now accessible via public URL

Customer Flow:
1. Customer taps NFC card or scans QR code
2. Backend resolves NFC UUID to landing page (via Redis cache)
3. Backend returns landing page content
4. Frontend renders landing page with event details, game, etc.
5. Customer interacts with page (plays game, signs up, etc.)
6. Backend processes interactions and stores in MongoDB
```

**Redis Keys:**

```
landingpage:public:{slug}        - Public landing page cache (TTL: 5 min)
landingpage:detail:{id}          - Landing page detail cache (TTL: 2 min)
landingpage:list:{page}          - Landing page list cache (TTL: 2 min)
card:nfc:{nfcUuid}               - NFC UUID resolution cache (TTL: 1 hour)
```

**BullMQ Jobs:**

```
standard-queue:
  - aggregate-landing-page-analytics
```

---

### 4.4 Promoter Module

**Purpose:** Promoter management and performance tracking  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `promoter.model.ts` | Model | ✅ Planned | Promoter model |
| `promoter.service.ts` | Service | ✅ Planned | Promoter CRUD operations |
| `promoter.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `promoter.route.ts` | Route | ✅ Planned | API route definitions |
| `promoter.validation.ts` | Validation | ✅ Planned | Zod schemas |
| `promoter.constant.ts` | Constants | ✅ Planned | Promoter constants |
| `promoter.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `promoter.test.ts` | Test | ✅ Planned | Tests |
| `promoterSelection.model.ts` | Model | ✅ Planned | Promoter selection tracking |
| `promoterSelection.service.ts` | Service | ✅ Planned | Selection tracking logic |

**API Endpoints:**

```
GET    /api/v1/promoters                      - List promoters
POST   /api/v1/promoters                      - Create promoter
GET    /api/v1/promoters/:id                  - Get promoter details
PUT    /api/v1/promoters/:id                  - Update promoter
DELETE /api/v1/promoters/:id                  - Delete promoter
GET    /api/v1/promoters/:id/stats            - Get promoter statistics
GET    /api/v1/promoters/leaderboard          - Get promoter leaderboard
POST   /api/v1/promoters/:id/assign-card      - Assign card to promoter
POST   /api/v1/promoters/:id/unassign-card    - Unassign card
```

**Data Flow:**

```
1. Admin creates promoter (name, nickname, email, phone)
2. Backend stores promoter in MongoDB
3. Admin assigns NFC cards to promoter
4. Promoter distributes cards to customers
5. Customer taps card and selects promoter
6. Backend tracks promoter selection
7. Backend aggregates promoter stats (interactions, signups, reviews)
8. Promoter views stats in dashboard
```

**Redis Keys:**

```
promoter:profile:{promoterId}    - Promoter profile cache (TTL: 15 min)
promoter:leaderboard              - Promoter leaderboard (TTL: 5 min, sorted set)
promoter:stats:{promoterId}:{period} - Promoter stats cache (TTL: 2 min)
```

**BullMQ Jobs:**

```
low-queue:
  - recalculate-promoter-stats
  - generate-leaderboard
```

---

### 4.5 Campaign & Event Module

**Purpose:** Campaign and event lifecycle management  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `campaign.model.ts` | Model | ✅ Planned | Campaign model |
| `campaign.service.ts` | Service | ✅ Planned | Campaign CRUD operations |
| `campaign.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `campaign.route.ts` | Route | ✅ Planned | API route definitions |
| `campaign.validation.ts` | Validation | ✅ Planned | Zod schemas |
| `campaign.constant.ts` | Constants | ✅ Planned | Campaign constants |
| `campaign.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `campaign.test.ts` | Test | ✅ Planned | Tests |
| `event.model.ts` | Model | ✅ Planned | Event model |
| `event.service.ts` | Service | ✅ Planned | Event CRUD operations |
| `event.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `event.route.ts` | Route | ✅ Planned | API route definitions |
| `event.validation.ts` | Validation | ✅ Planned | Zod schemas |
| `event.constant.ts` | Constants | ✅ Planned | Event constants |
| `event.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `event.test.ts` | Test | ✅ Planned | Tests |

**API Endpoints:**

```
GET    /api/v1/campaigns                      - List campaigns
POST   /api/v1/campaigns                      - Create campaign
GET    /api/v1/campaigns/:id                  - Get campaign details
PUT    /api/v1/campaigns/:id                  - Update campaign
DELETE /api/v1/campaigns/:id                  - Delete campaign
GET    /api/v1/campaigns/:id/events           - List campaign events
POST   /api/v1/campaigns/:id/events           - Create event
PUT    /api/v1/campaigns/:id/events/:eventId  - Update event
DELETE /api/v1/campaigns/:id/events/:eventId  - Delete event
GET    /api/v1/campaigns/:id/stats            - Get campaign statistics
GET    /api/v1/events/upcoming                - Get upcoming events
```

**Data Flow:**

```
1. Admin creates campaign (name, dates, venue, landing page)
2. Admin creates events within campaign
3. Admin assigns promoters to campaign
4. Campaign status transitions: scheduled → active → completed
5. Backend tracks campaign interactions and signups
6. Admin views campaign analytics
```

**Redis Keys:**

```
campaign:list:{status}           - Campaign list cache (TTL: 2 min)
campaign:detail:{id}             - Campaign detail cache (TTL: 5 min)
event:upcoming                   - Upcoming events cache (TTL: 5 min)
```

**BullMQ Jobs:**

```
low-queue:
  - update-campaign-status (scheduled → active based on date)
  - aggregate-campaign-analytics
```

---

### 4.6 Card Module

**Purpose:** NFC card and QR code management  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `card.model.ts` | Model | ✅ Planned | Card model |
| `card.service.ts` | Service | ✅ Planned | Card CRUD operations |
| `card.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `card.route.ts` | Route | ✅ Planned | API route definitions |
| `card.validation.ts` | Validation | ✅ Planned | Zod schemas |
| `card.constant.ts` | Constants | ✅ Planned | Card constants |
| `card.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `card.test.ts` | Test | ✅ Planned | Tests |
| `cardInteraction.model.ts` | Model | ✅ Planned | Card interaction tracking |
| `cardInteraction.service.ts` | Service | ✅ Planned | Interaction tracking logic |
| `cardInteraction.controller.ts` | Controller | ✅ Planned | Interaction handlers |
| `cardInteraction.route.ts` | Route | ✅ Planned | Interaction routes |

**API Endpoints:**

```
GET    /api/v1/cards                          - List cards
POST   /api/v1/cards                          - Create card
GET    /api/v1/cards/:id                      - Get card details
PUT    /api/v1/cards/:id                      - Update card
DELETE /api/v1/cards/:id                      - Deactivate card
POST   /api/v1/cards/:id/assign               - Assign card to promoter
POST   /api/v1/cards/:id/unassign             - Unassign card
GET    /api/v1/cards/:id/interactions         - Get card interactions
POST   /api/v1/cards/resolve/:nfcUuid         - Resolve NFC UUID to page
POST   /api/v1/cards/:id/generate-qr          - Generate QR code
```

**Data Flow:**

```
1. Admin creates card (name, type: NFC or QR)
2. For NFC cards: Admin assigns NFC UUID (from supplier)
3. For QR cards: Backend generates QR code
4. Admin assigns card to promoter
5. Customer taps NFC card or scans QR code
6. Backend resolves NFC UUID/QR to landing page
7. Backend tracks card interaction
8. Admin views card analytics
```

**Redis Keys:**

```
card:detail:{cardId}             - Card detail cache (TTL: 15 min)
card:nfc:{nfcUuid}               - NFC UUID resolution cache (TTL: 1 hour)
card:interactions:{cardId}:{period} - Card interaction stats (TTL: 2 min)
```

**BullMQ Jobs:**

```
standard-queue:
  - generate-qr-code (async for bulk orders)
low-queue:
  - aggregate-card-analytics
```

---

### 4.7 Participant Module

**Purpose:** Participant data capture and management  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `participant.model.ts` | Model | ✅ Planned | Participant model |
| `participant.service.ts` | Service | ✅ Planned | Participant CRUD operations |
| `participant.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `participant.route.ts` | Route | ✅ Planned | API route definitions |
| `participant.validation.ts` | Validation | ✅ Planned | Zod schemas |
| `participant.constant.ts` | Constants | ✅ Planned | Participant constants |
| `participant.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `participant.test.ts` | Test | ✅ Planned | Tests |

**API Endpoints:**

```
GET    /api/v1/participants                   - List participants
GET    /api/v1/participants/:id               - Get participant details
PUT    /api/v1/participants/:id               - Update participant
DELETE /api/v1/participants/:id               - Delete participant
GET    /api/v1/participants/export            - Export participants (CSV)
GET    /api/v1/participants/stats             - Get participant statistics
```

**Data Flow:**

```
1. Customer submits sign-up form on landing page
2. Backend validates input (email format, consent given)
3. Backend checks for duplicate email
4. Backend creates participant in MongoDB
5. Backend sends welcome email (via BullMQ)
6. Admin views participants in dashboard
7. Admin exports participants to CSV
```

**Redis Keys:**

```
participant:count                - Participant count cache (TTL: 2 min)
participant:list:{page}          - Participant list cache (TTL: 1 min)
participant:export:{exportId}    - Export status cache (TTL: 1 hour)
```

**BullMQ Jobs:**

```
standard-queue:
  - send-welcome-email
  - generate-participant-export
low-queue:
  - aggregate-participant-analytics
```

---

### 4.8 Game Module

**Purpose:** Game interaction handling with fraud prevention  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `gamePlay.model.ts` | Model | ✅ Planned | GamePlay model |
| `gamePlay.service.ts` | Service | ✅ Planned | Game play logic |
| `gamePlay.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `gamePlay.route.ts` | Route | ✅ Planned | API route definitions |
| `gamePlay.validation.ts` | Validation | ✅ Planned | Zod schemas |
| `gamePlay.constant.ts` | Constants | ✅ Planned | Game constants |
| `gamePlay.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `gamePlay.test.ts` | Test | ✅ Planned | Tests |
| `prize.model.ts` | Model | ✅ Planned | Prize model |
| `prize.service.ts` | Service | ✅ Planned | Prize CRUD operations |
| `prize.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `prize.route.ts` | Route | ✅ Planned | API route definitions |
| `prize.validation.ts` | Validation | ✅ Planned | Zod schemas |
| `prize.constant.ts` | Constants | ✅ Planned | Prize constants |
| `prize.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `prize.test.ts` | Test | ✅ Planned | Tests |
| `game.service.ts` | Service | ✅ Planned | Shared game logic (probability, etc.) |

**API Endpoints:**

```
# Game Play Endpoints
POST   /api/v1/games/play                       - Play game (dice/spin)
POST   /api/v1/games/:playId/redeem             - Redeem prize
GET    /api/v1/games/stats                      - Get game statistics
GET    /api/v1/games/plays                      - List game plays
GET    /api/v1/games/prizes/distribution        - Get prize distribution

# Prize Endpoints
GET    /api/v1/prizes                           - List prizes
POST   /api/v1/prizes                           - Create prize
PUT    /api/v1/prizes/:id                       - Update prize
DELETE /api/v1/prizes/:id                       - Delete prize
```

**Data Flow:**

```
1. Customer clicks "Play" button on landing page
2. Backend receives game play request
3. Backend validates rate limit (1 play per 5 min per device)
4. Backend checks device fingerprint for duplicates
5. Backend runs game logic (weighted probability)
6. Backend determines prize result
7. Backend stores game play in MongoDB
8. Backend returns result to customer
9. Customer sees prize won
10. Customer can redeem prize (staff validates)
```

**Game Logic:**

```
Dice Roll:
  - 6-sided dice
  - Each face maps to a prize
  - Weighted probability (configurable)
  - Result determined server-side

Spin Wheel:
  - 4-8 segments
  - Each segment maps to a prize
  - Weighted probability (configurable)
  - Result determined server-side

Anti-Fraud:
  - Rate limiting: 1 play per 5 minutes per device
  - Device fingerprinting
  - IP address tracking
  - Session validation
  - Pattern detection (unusual activity)
```

**Redis Keys:**

```
game:rate-limit:{deviceFingerprint} - Rate limit counter (TTL: 5 min)
game:prize-config:{landingPageId}   - Prize configuration cache (TTL: 15 min)
game:stats:{period}                 - Game statistics cache (TTL: 2 min)
```

**BullMQ Jobs:**

```
low-queue:
  - aggregate-game-analytics
  - detect-fraud-patterns
```

---

### 4.9 Review Module

**Purpose:** Review click tracking and attribution  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `review.model.ts` | Model | ✅ Planned | ReviewClick model |
| `review.service.ts` | Service | ✅ Planned | Review tracking logic |
| `review.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `review.route.ts` | Route | ✅ Planned | API route definitions |
| `review.constant.ts` | Constants | ✅ Planned | Review constants |
| `review.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `review.test.ts` | Test | ✅ Planned | Tests |

**API Endpoints:**

```
GET    /api/v1/reviews/stats                  - Get review statistics
GET    /api/v1/reviews/clicks                 - List review clicks
GET    /api/v1/reviews/funnel                 - Get review funnel data
GET    /api/v1/reviews/recent                 - Get recent review clicks
```

**Data Flow:**

```
1. Customer clicks "Leave a Review" button on landing page
2. Backend tracks review click
3. Backend redirects customer to Google review URL
4. Backend attributes click to promoter (if selected)
5. Admin views review engagement stats
6. Admin views review funnel (participants → clicks → reviews)
```

**Redis Keys:**

```
review:stats:{period}            - Review statistics cache (TTL: 2 min)
review:funnel                    - Review funnel cache (TTL: 5 min)
```

**BullMQ Jobs:**

```
low-queue:
  - aggregate-review-analytics
```

---

### 4.10 Social Module

**Purpose:** Social link tracking and analytics  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `socialLink.model.ts` | Model | ✅ Planned | SocialLink model |
| `socialLink.service.ts` | Service | ✅ Planned | Social link CRUD |
| `socialLink.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `socialLink.route.ts` | Route | ✅ Planned | API route definitions |
| `socialLink.constant.ts` | Constants | ✅ Planned | Social link constants |
| `socialLink.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `socialLink.test.ts` | Test | ✅ Planned | Tests |
| `socialLinkClick.model.ts` | Model | ✅ Planned | SocialLinkClick model |
| `socialLinkClick.service.ts` | Service | ✅ Planned | Click tracking logic |
| `socialLinkClick.controller.ts` | Controller | ✅ Planned | Click handlers |
| `socialLinkClick.route.ts` | Route | ✅ Planned | Click routes |

**API Endpoints:**

```
GET    /api/v1/social-links                   - List social links
POST   /api/v1/social-links                   - Create social link
PUT    /api/v1/social-links/:id               - Update social link
DELETE /api/v1/social-links/:id               - Delete social link
GET    /api/v1/social-links/stats             - Get social link statistics
GET    /api/v1/social-links/top               - Get top performing links
```

**Data Flow:**

```
1. Admin configures social links on landing page
2. Customer clicks social link on landing page
3. Backend tracks click
4. Backend redirects customer to social URL
5. Admin views social link analytics
```

**Redis Keys:**

```
sociallink:list:{landingPageId}  - Social link list cache (TTL: 15 min)
sociallink:analytics:{period}    - Social analytics cache (TTL: 5 min)
```

**BullMQ Jobs:**

```
low-queue:
  - aggregate-social-analytics
```

---

### 4.11 Analytics Module

**Purpose:** Comprehensive analytics and reporting  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `analytics.model.ts` | Model | ✅ Planned | AnalyticsDaily model |
| `analytics.service.ts` | Service | ✅ Planned | Analytics aggregation |
| `analytics.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `analytics.route.ts` | Route | ✅ Planned | API route definitions |
| `analytics.constant.ts` | Constants | ✅ Planned | Analytics constants |
| `analytics.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `analytics.test.ts` | Test | ✅ Planned | Tests |

**API Endpoints:**

```
GET    /api/v1/analytics/overview             - Get overview metrics
GET    /api/v1/analytics/interactions         - Get interaction analytics
GET    /api/v1/analytics/peak-hours           - Get peak activity hours
GET    /api/v1/analytics/geographic           - Get geographic distribution
GET    /api/v1/analytics/cards                - Get card performance
GET    /api/v1/analytics/promoters            - Get promoter leaderboard
GET    /api/v1/analytics/trends               - Get trend data
```

**Data Flow:**

```
1. Backend aggregates analytics from all modules
2. Backend stores daily aggregates in AnalyticsDaily collection
3. Admin requests analytics data
4. Backend returns aggregated data (with caching)
5. Admin views charts and graphs
```

**Redis Keys:**

```
analytics:overview:{period}      - Overview metrics cache (TTL: 1 min)
analytics:breakdown:{type}:{period} - Breakdown cache (TTL: 2 min)
analytics:trends:{type}          - Trend data cache (TTL: 5 min)
```

**BullMQ Jobs:**

```
low-queue:
  - aggregate-daily-analytics (scheduled, runs at midnight)
  - aggregate-realtime-analytics (frequent, every 5 minutes)
```

---

### 4.12 Report Module

**Purpose:** Report generation and data export  

**Components:**

| Component | Type | Status | Description |
|-----------|------|--------|-------------|
| `report.model.ts` | Model | ✅ Planned | Report model |
| `report.service.ts` | Service | ✅ Planned | Report generation |
| `report.controller.ts` | Controller | ✅ Planned | HTTP request handlers |
| `report.route.ts` | Route | ✅ Planned | API route definitions |
| `report.validation.ts` | Validation | ✅ Planned | Zod schemas |
| `report.constant.ts` | Constants | ✅ Planned | Report constants |
| `report.interface.ts` | Interface | ✅ Planned | TypeScript interfaces |
| `report.test.ts` | Test | ✅ Planned | Tests |

**API Endpoints:**

```
GET    /api/v1/reports                        - List reports
POST   /api/v1/reports/generate               - Generate report (async)
GET    /api/v1/reports/:id                    - Get report status
GET    /api/v1/reports/:id/download           - Download report
DELETE /api/v1/reports/:id                    - Delete report
POST   /api/v1/reports/export/quick           - Quick data export
```

**Data Flow:**

```
1. Admin requests report generation
2. Backend creates report job (BullMQ)
3. Backend returns job ID to admin
4. BullMQ worker generates report
5. Backend stores report in S3
6. Admin checks report status
7. Admin downloads report
```

**Redis Keys:**

```
report:list:{status}             - Report list cache (TTL: 1 min)
report:status:{reportId}         - Report status cache (TTL: 1 hour)
```

**BullMQ Jobs:**

```
standard-queue:
  - generate-monthly-performance-report
  - generate-audience-data-export
  - generate-engagement-report
  - generate-game-performance-report
  - export-csv
  - export-excel
  - export-pdf
```

---

## 5. API Endpoint Analysis

### 5.1 Endpoint Summary

| Module | Total Endpoints | Auth Required | Rate Limit |
|--------|-----------------|---------------|------------|
| Auth | 9 | 2 (login, register) | auth (5/min) |
| User | 6 | Yes | user (100/min) |
| LandingPage | 11 | 8 (admin) | user (100/min) |
| Public LandingPage | 6 | No | public (30/min) |
| Promoter | 8 | Yes | user (100/min) |
| Campaign | 10 | Yes | user (100/min) |
| Card | 9 | Yes | user (100/min) |
| Participant | 6 | Yes | user (100/min) |
| Game | 5 | 2 (public) | public (30/min) |
| Prize | 5 | Yes | user (100/min) |
| Review | 4 | Yes | user (100/min) |
| Social | 6 | Yes | user (100/min) |
| Analytics | 7 | Yes | user (100/min) |
| Report | 5 | Yes | user (100/min) |
| **Total** | **97** | | |

### 5.2 Public vs Authenticated Endpoints

| Type | Count | Examples |
|------|-------|----------|
| **Public (no auth)** | 8 | Login, register, public landing pages, game play |
| **Authenticated** | 89 | All admin and promoter endpoints |

### 5.3 Rate Limiting Tiers

| Tier | Limit | Window | Endpoints |
|------|-------|--------|-----------|
| **auth** | 5 | 1 minute | Login, register, password reset |
| **public** | 30 | 1 minute | Public landing pages, game play |
| **user** | 100 | 1 minute | All authenticated endpoints |
| **admin** | 200 | 1 minute | Admin-only endpoints (if needed) |

---

## 6. Data Flow Analysis

### 6.1 NFC Tap to Landing Page Flow

```
┌──────────┐     ┌──────────┐     ┌──────────     ┌──────────┐     ┌──────────┐
│ Customer │     │   NFC    │     │ Backend  │     │  Redis   │     │ MongoDB  │
│  Device  │     │   Card   │     │   API    │     │  Cache   │     │          │
└────┬─────     └────┬─────┘     └────┬─────     └────┬─────┘     └────┬─────
     │                │                │                │                │
     │ 1. Tap NFC     │                │                │                │
     │───────────────>│                │                │                │
     │                │                │                │                │
     │                │ 2. Redirect    │                │                │
     │                │    to URL      │                │                │
     │                │───────────────>│                │                │
     │                │                │                │                │
     │                │                │ 3. Check cache │                │
     │                │                │───────────────>│                │
     │                │                │                │                │
     │                │                │ 4. Cache hit?  │                │
     │                │                │<───────────────│                │
     │                │                │                │                │
     │                │                │ 5. If miss,    │                │
     │                │                │    query DB    │───────────────>│
     │                │                │                │                │
     │                │                │ 6. Return page │                │
     │                │                │<───────────────│                │
     │                │                │                │                │
     │                │                │ 7. Cache page  │                │
     │                │                │───────────────>│                │
     │                │                │                │                │
     │                │                │ 8. Return page │                │
     │ 9. Display     │                │                │                │
     │<───────────────│                │                │                │
     │                │                │                │                │
```

### 6.2 Game Play Flow

```
┌──────────┐     ┌──────────     ┌──────────┐     ┌──────────┐     ┌──────────┐
│ Customer │     │ Backend  │     │  Redis   │     │ MongoDB  │     │ BullMQ   │
│  Device  │     │   API    │     │ (Rate)   │     │          │     │ (Queue)  │
└────┬─────┘     └────┬─────     └────┬─────┘     └────┬─────     └────┬─────┘
     │                │                │                │                │
     │ 1. Click Play  │                │                │                │
     │───────────────>│                │                │                │
     │                │                │                │                │
     │                │ 2. Check rate  │                │                │
     │                │    limit       │───────────────>│                │
     │                │                │                │                │
     │                │ 3. Rate OK?    │                │                │
     │                │<───────────────│                │                │
     │                │                │                │                │
     │                │ 4. Run game    │                │                │
     │                │    logic       │                │                │
     │                │                │                │                │
     │                │ 5. Store play  │                │                │
     │                │───────────────>│                │                │
     │                │                │                │                │
     │                │ 6. Queue       │                │                │
     │                │    analytics   │────────────────────────────────>│
     │                │                │                │                │
     │ 7. Return      │                │                │                │
     │    result      │                │                │                │
     │<───────────────│                │                │                │
     │                │                │                │                │
     │ 8. Display     │                │                │                │
     │    prize       │                │                │                │
     │                │                │                │                │
```

### 6.3 Sign-Up Flow

```
┌──────────     ┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────
│ Customer │     │ Backend  │     │ MongoDB  │     │ BullMQ   │     │  Email   │
│  Device  │     │   API    │     │          │     │ (Queue)  │     │ Service  │
└────┬─────┘     └────┬─────┘     └────┬─────┘     └────┬─────┘     └────┬─────┘
     │                │                │                │                │
     │ 1. Submit form │                │                │                │
     │───────────────>│                │                │                │
     │                │                │                │                │
     │                │ 2. Validate    │                │                │
     │                │    input       │                │                │
     │                │                │                │                │
     │                │ 3. Check dup   │                │                │
     │                │    email       │───────────────>│                │
     │                │                │                │                │
     │                │ 4. Create      │                │                │
     │                │    participant │───────────────>│                │
     │                │                │                │                │
     │                │ 5. Queue       │                │                │
     │                │    email       │───────────────>│                │
     │                │                │───────────────>│                │
     │ 6. Success     │                │                │                │
     │    response    │                │                │                │
     │<───────────────│                │                │                │
     │                │                │                │                │
     │                │                │                │ 7. Send email  │
     │                │                │                │───────────────>│
     │                │                │                │                │
```

---

## 7. Integration Points

### 7.1 External Integrations

| Integration | Purpose | Status | Priority |
|-------------|---------|--------|----------|
| **Google Business API** | Review URL generation | ⚠️ Partial | High |
| **NFC Card Supplier** | Card provisioning | ❌ Not started | High |
| **Email Service (SendGrid/SES)** | Transactional emails | ✅ Planned | High |
| **SMS Service** | SMS notifications | ❌ Not started | Medium |
| **Cloud Storage (S3)** | File uploads, reports | ✅ Planned | High |
| **CDN (Cloudflare)** | Static assets, landing pages | ✅ Planned | High |
| **Payment Gateway** | Subscriptions (if multi-tenant) | ❌ Not started | Low |

### 7.2 Integration Details

#### Google Business API

**What's Planned:**
- ✅ Generate Google review URLs
- ✅ Link Google Business profiles
- ✅ Track review clicks (proxy metric)
- ✅ Promoter attribution via nickname matching

**Limitations:**
- ❌ Cannot track actual Google review submissions
- ❌ No API for review content
- ❌ Must rely on click tracking as proxy

**Implementation:**
```typescript
// Google Review URL Generation
const googleReviewUrl = `https://search.google.com/local/writereview?placeid=${placeId}`;

// Nickname matching in reviews (manual process)
const reviews = await scanGoogleReviewsForPromoterMentions(promoter.nickname);
```

#### NFC Card Supplier

**What's Needed:**
- NFC UUID provisioning workflow
- Bulk card ordering
- UUID mapping to landing pages
- Card status tracking

**Options:**
- Taglio
- GoToTags
- Custom NFC suppliers

**Implementation:**
```typescript
// NFC Card Order
const order = await nfcProvider.orders.create({
  quantity: 100,
  type: 'NTAG213',
});

// UUID Mapping
await Card.create({
  nfcUuid: nfcTag.uuid,
  landingPage: page.id,
});
```

#### Email Service

**What's Planned:**
- ✅ Welcome emails
- ✅ Password reset emails
- ✅ Email verification
- ✅ Report completion notifications
- ✅ Prize redemption confirmations

**Provider Options:**
- SendGrid
- AWS SES
- Mailgun

---

## 8. Missing Components & Recommendations

### 8.1 Missing Frontend Components

| Component | Status | Recommendation | Priority |
|-----------|--------|----------------|----------|
| **Admin Dashboard UI** | ✅ Designs exist | Build with React/Next.js | High |
| **Promoter Dashboard UI** | ❌ No designs | Create Figma designs first | High |
| **Customer Landing Pages** | ❌ No designs | Create Figma designs first | High |
| **Promoter Mobile App** | ❌ No designs | Consider PWA first | Medium |
| **Email Templates** | ❌ Not designed | Design transactional emails | Medium |
| **Report Templates** | ❌ Not designed | Design PDF/Excel reports | Low |

### 8.2 Missing Backend Components

| Component | Status | Recommendation | Priority |
|-----------|--------|----------------|----------|
| **Multi-Tenancy** | ❌ Removed | Can add later if needed | Low |
| **SMS Service** | ❌ Not started | Add in Phase 2 | Medium |
| **NFC Supplier Integration** | ❌ Not started | Integrate when ready | High |
| **Push Notifications** | ❌ Not started | Add for mobile app | Low |
| **Webhook System** | ❌ Not started | For third-party integrations | Low |

### 8.3 Recommendations

1. **Start with Admin Dashboard** - Designs exist, backend APIs are planned
2. **Design Promoter Dashboard** - Backend APIs are ready, need UI
3. **Design Customer Landing Pages** - Backend APIs are ready, need UI
4. **Consider PWA for Promoters** - Faster to market than native mobile app
5. **Add NFC Supplier Integration** - Critical for physical card deployment
6. **Plan for Multi-Tenancy** - Can add later if business model changes

---

## 9. Technical Debt & Risks

### 9.1 Technical Risks

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| **Google API Limitations** | High | High | Use click tracking as proxy, manual review matching |
| **NFC UUID Collisions** | High | Low | Unique constraint, validation |
| **Game Fraud** | High | High | Rate limiting, device fingerprinting, pattern detection |
| **Redis Cache Invalidation** | Medium | Medium | Comprehensive invalidation strategy, TTL fallback |
| **BullMQ Queue Backlog** | Medium | Medium | Queue monitoring, auto-scaling workers |
| **MongoDB Performance** | High | Medium | Index optimization, query monitoring, read replicas |

### 9.2 Design Risks

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| **No Promoter Dashboard Designs** | High | High | Create Figma designs before development |
| **No Customer Landing Page Designs** | High | High | Create Figma designs before development |
| **Mobile Responsiveness Unknown** | Medium | Medium | Design mobile-first for landing pages |

### 9.3 Business Risks

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| **NFC Card Supply Issues** | High | Medium | Multiple suppliers, buffer stock |
| **Promoter Adoption** | Medium | Medium | Training, incentives |
| **Customer Engagement** | Medium | Medium | A/B testing, optimization |

---

## 10. Implementation Priority Matrix

### 10.1 Backend Implementation Order

```
Priority 1 (P0) - Foundation:
├── Phase 0: Foundation Setup
├── Phase 1: Auth & User Management
└── Phase 2: Landing Page Management

Priority 2 (P0) - Core Features:
├── Phase 3: Promoter Management
├── Phase 4: Campaign & Event Management
├── Phase 5: Card Management
├── Phase 6: Participant Management
└── Phase 7: Game Engine

Priority 3 (P1) - Analytics & Reporting:
├── Phase 8: Review & Social Tracking
├── Phase 9: Analytics Dashboard
└── Phase 10: Report Generation

Priority 4 (P2) - Integration & Polish:
├── Phase 11: Integration & Polish
├── Phase 12: Testing & QA
└── Phase 13: Deployment & Launch
```

### 10.2 Frontend Implementation Order

```
Priority 1 (P0) - Admin Dashboard:
├── Admin Dashboard UI (designs exist)
├── Landing Page Builder UI
├── Campaign Management UI
├── Promoter Management UI
├── Card Management UI
├── Analytics Dashboard UI
└── Report Generation UI

Priority 2 (P0) - Customer Landing Pages:
├── Create Figma designs
├── Landing page rendering
├── Game interaction UI
├── Sign-up form UI
├── Review prompt UI
└── Prize display UI

Priority 3 (P1) - Promoter Dashboard:
├── Create Figma designs
├── Promoter dashboard UI
├── Personal stats UI
├── Card management UI
├── Event tracking UI
└── Prize redemption UI

Priority 4 (P2) - Mobile (Optional):
├── PWA for promoters
└── Native mobile app (future)
```

---

**End of Complete Component Analysis**

**Document Version:** 1.0  
**Last Updated:** April 7, 2026  
**Status:** Complete System Analysis  
**Next Steps:** Review and approve before development begins
