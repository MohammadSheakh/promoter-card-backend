# Product Requirements Document (PRD)
## PromoterCard - NFC-Powered Event Promotion Platform

**Version:** 1.0  
**Last Updated:** April 7, 2026  
**Status:** Draft - Ready for Review  
**Author:** Backend Development Team  

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Product Vision](#2-product-vision)
3. [Target Audience](#3-target-audience)
4. [User Roles & Permissions](#4-user-roles--permissions)
5. [Functional Requirements](#5-functional-requirements)
6. [Non-Functional Requirements](#6-non-functional-requirements)
7. [System Architecture](#7-system-architecture)
8. [Database Schema](#8-database-schema)
9. [API Specifications](#9-api-specifications)
10. [Third-Party Integrations](#10-third-party-integrations)
11. [Security Requirements](#11-security-requirements)
12. [Performance Requirements](#12-performance-requirements)
13. [Scalability & Future Roadmap](#13-scalability--future-roadmap)
14. [Assumptions & Dependencies](#14-assumptions--dependencies)
15. [Success Metrics](#15-success-metrics)

---

## 1. Executive Summary

### 1.1 Product Overview

**PromoterCard** is a comprehensive SaaS platform that revolutionizes event promotion by replacing traditional flyer-based marketing with interactive NFC-enabled cards linked to customizable landing pages. The platform enables businesses (primarily hospitality venues like beach clubs, bars, and lounges) to:

- Deploy promoters with NFC cards that link to event-specific landing pages
- Engage potential customers through interactive games (dice roll, spin wheel)
- Capture participant data (email, phone, name) for marketing
- Incentivize Google reviews through prize giveaways
- Track comprehensive analytics on promoter performance and campaign effectiveness

### 1.2 Problem Statement

Traditional event promotion methods (flyers, word-of-mouth) are:
- **Ineffective**: Low engagement and conversion rates
- **Untrackable**: No measurable ROI or performance data
- **Wasteful**: Print materials with limited lifespan
- **Non-interactive**: No immediate engagement mechanism

### 1.3 Solution

PromoterCard provides:
- **Interactive Engagement**: Games and prizes incentivize participation
- **Measurable Results**: Real-time analytics on every interaction
- **Data Capture**: Build customer databases for retargeting
- **Reputation Building**: Systematic Google review collection
- **Promoter Accountability**: Track individual promoter performance

---

## 2. Product Vision

### 2.1 Long-Term Vision

To become the leading platform for interactive, data-driven event promotion in the hospitality and entertainment industry, enabling businesses to replace outdated marketing methods with measurable, engaging digital experiences.

### 2.2 Product Principles

1. **Engagement-First**: Every interaction should be memorable and incentivized
2. **Data-Driven**: All decisions backed by measurable metrics
3. **Promoter-Centric**: Empower promoters with tools and insights
4. **Business-Value**: Clear ROI for businesses using the platform
5. **Scalable**: Support growth from single venue to multi-location chains

---

## 3. Target Audience

### 3.1 Primary Users (Business Customers)

- **Beach Clubs & Nightclubs**: Promote events, capture guest lists
- **Bars & Lounges**: Drive foot traffic, collect reviews
- **Event Organizers**: Manage promoter teams, track campaign performance
- **Restaurant Chains**: Multi-location promotion and review management

### 3.2 Secondary Users (End Users)

- **Promoters**: Field staff who distribute NFC cards and engage customers
- **Customers**: Event attendees who interact with landing pages

### 3.3 Market Size

- Global nightlife and entertainment industry: $500B+
- Target market: Premium venues in major cities and tourist destinations
- Initial focus: Ibiza, Bali, Miami, Las Vegas, Dubai

---

## 4. User Roles & Permissions

### 4.1 Role Hierarchy

```
Super Admin (Platform Owner)
└── Business Admin (Venue Owner/Manager)
    ├── Campaign Manager
    ├── Promoter
    └── Customer (End User)
```

### 4.2 Role Definitions

#### **4.2.1 Super Admin**
- Platform-level administration
- Business account management
- Subscription and billing oversight
- System-wide analytics
- Global settings management

#### **4.2.2 Business Admin**
- Full access to all business data
- Manage promoters and campaigns
- Configure landing pages and games
- View all analytics and reports
- Manage NFC cards and QR codes
- Configure social links and integrations

#### **4.2.3 Campaign Manager**
- Create and manage campaigns
- Assign promoters to events
- Monitor campaign performance
- Generate reports
- Limited admin access (no business settings)

#### **4.2.4 Promoter**
- View personal performance dashboard
- See assigned NFC cards
- Track personal stats (interactions, signups, reviews)
- View upcoming events
- Validate prize redemptions
- Update personal profile

#### **4.2.5 Customer (End User)**
- Access landing pages via NFC/QR
- Play games (dice roll, spin wheel)
- Submit sign-up forms
- Leave Google reviews
- View and claim prizes
- Access social links

### 4.3 Permission Matrix

| Feature | Super Admin | Business Admin | Campaign Manager | Promoter | Customer |
|---------|-------------|----------------|------------------|----------|----------|
| View Platform Analytics | ✅ | ❌ |  | ❌ |  |
| Manage Businesses | ✅ | ❌ | ❌ | ❌ | ❌ |
| Manage Promoters | ❌ | ✅ | ✅ | ❌ | ❌ |
| Create Campaigns | ❌ | ✅ | ✅ | ❌ | ❌ |
| Edit Landing Pages | ❌ | ✅ | ✅ | ❌ | ❌ |
| View Business Analytics | ❌ | ✅ | ✅ | ❌ | ❌ |
| View Personal Stats | ❌ | ✅ | ✅ | ✅ | ❌ |
| Play Games | ❌ | ❌ | ❌ | ❌ | ✅ |
| Submit Sign-Up | ❌ | ❌ | ❌ | ❌ | ✅ |
| Leave Reviews | ❌ | ❌ | ❌ | ❌ | ✅ |

---

## 5. Functional Requirements

### 5.1 Admin Dashboard Module

#### 5.1.1 Dashboard Overview
**Priority: P0 (Critical)**

**Features:**
- Performance overview cards:
  - Total Interactions (with trend %)
  - Total Participants (with trend %)
  - Total Game Plays (with trend %)
  - Sign Ups (with trend %)
  - Google Reviews (with trend %)
- Time range selector: Today, 7 Days, 30 Days, Custom
- Promoter Leaderboard:
  - Ranked list of promoters
  - Individual stats: Interactions, Sign Ups, Reviews
  - Top performer badge
  - Performance trend indicators

**Data Sources:**
- Aggregated from interactions, participants, games, reviews tables
- Real-time updates via WebSocket or polling

---

#### 5.1.2 Analytics Module
**Priority: P0 (Critical)**

**Features:**

**Interaction Analytics:**
- Total Interactions count
- Unique Visitors count
- Return Visitors count
- Active Cards count
- Activity Over Time chart (line graph)
- Peak Activity Hours (bar chart by hour)
- Interactions by Card (donut chart)
- Geographic Distribution (location-based breakdown)
- Promoter Leaderboard (secondary view)

**Key Metrics:**
- Peak time identification
- Best performing day
- Most active locations
- Card performance comparison

---

#### 5.1.3 Landing Pages Module
**Priority: P0 (Critical)**

**Features:**

**Landing Page Editor:**
- Page URL configuration: `promotercard.com/{slug}`
- Promoter assignment (visitors select who referred them)
- Page Blocks (modular content blocks):
  - **Hero Section**:
    - Headline
    - Subheadline
    - Hero Image (URL or upload)
    - Toggle on/off
  - **Event Details**:
    - Venue Name
    - Date
    - Time
    - Location
    - Toggle on/off
  - **Description**:
    - Rich text content
    - Toggle on/off
  - **Prize Game**:
    - Game type selection (Dice Roll / Spin Wheel)
    - Prize 1-6 configuration
    - Review URL (Google review link)
    - Review Prompt text
    - Toggle on/off
  - **Social Links**:
    - Instagram handle
    - TikTok handle
    - WhatsApp number
    - Website URL
    - Toggle on/off
  - **Contact Info**:
    - Phone number
    - Email address
    - Toggle on/off
- Live Preview (mobile and desktop views)
- Duplicate page functionality
- Publish/Unpublish toggle
- Page status tracking

**Data Model:**
```
LandingPage:
  - id: UUID
  - business_id: UUID (FK)
  - slug: string (unique)
  - title: string
  - status: enum (draft, published, archived)
  - blocks: JSON (page block configurations)
  - promoters: array of promoter IDs
  - created_at: timestamp
  - updated_at: timestamp
  - published_at: timestamp
```

---

#### 5.1.4 Games & Prizes Module
**Priority: P0 (Critical)**

**Features:**

**Game Performance Dashboard:**
- Total Game Plays count
- Prizes Redeemed count
- Game type breakdown:
  - Dice Roll (play count)
  - Spin Wheel (play count)
- Game Plays This Week (bar chart by day)
- Peak Playing Hours (bar chart by hour)
- Best day identification

**Prize Distribution:**
- Prize breakdown by type:
  - Prize name
  - Times won
  - Times redeemed
  - Redemption rate (%)
- Most popular prize identification
- Prize configuration per landing page

**Game Logic Specifications:**

**Dice Roll Game:**
- 6-sided dice animation
- Each face maps to a prize
- Configurable probability weights
- Anti-fraud: Rate limiting (1 play per 5 minutes per device)
- Session tracking to prevent abuse

**Spin Wheel Game:**
- Visual wheel with prize segments
- Configurable number of segments (4-8)
- Weighted probability per segment
- Animation and result display
- Same anti-fraud measures as dice roll

**Prize Types:**
- Free Drink
- 50% Off Table
- VIP Entry
- 10% Discount
- Free Entry
- 2-for-1 Cocktails
- VIP Upgrade
- Free Bottle
- Free Shot
- Play Again (no prize)

**Data Model:**
```
GamePlay:
  - id: UUID
  - landing_page_id: UUID (FK)
  - participant_id: UUID (FK, nullable)
  - game_type: enum (dice_roll, spin_wheel)
  - result: string (prize won)
  - redeemed: boolean
  - redeemed_at: timestamp
  - ip_address: string
  - device_fingerprint: string
  - created_at: timestamp

Prize:
  - id: UUID
  - landing_page_id: UUID (FK)
  - name: string
  - probability: float (0-1)
  - max_redemptions: integer (optional)
  - current_redemptions: integer
  - active: boolean
```

---

#### 5.1.5 Participants Module
**Priority: P0 (Critical)**

**Features:**

**Captured Audience Data:**
- Total Participants count
- New Today count (with comparison)
- Email Captured count (with capture rate %)
- Phone Captured count (with capture rate %)
- Participant list with:
  - Name
  - Date joined
  - Email
  - Phone
  - Source (landing page/campaign)
  - Prize Won
  - Status (Confirmed, Pending)
- Search by name or email
- Filter by status
- Export to CSV functionality
- Pagination

**Sign-Up Form Fields:**
- Name (required)
- Email (required)
- Phone (optional but encouraged)
- Promoter attribution (selected by user)
- Consent checkbox (GDPR compliance)

**Data Model:**
```
Participant:
  - id: UUID
  - landing_page_id: UUID (FK)
  - campaign_id: UUID (FK, nullable)
  - promoter_id: UUID (FK, nullable)
  - name: string
  - email: string
  - phone: string (nullable)
  - status: enum (confirmed, pending, bounced)
  - prize_won: string (nullable)
  - source: string
  - consent_given: boolean
  - created_at: timestamp
  - updated_at: timestamp
```

---

#### 5.1.6 Social Links Module
**Priority: P1 (High)**

**Features:**

**Social Links Analytics:**
- Total Clicks count
- Unique Visitors count
- Click Rate (%)
- Active Links count
- Platform breakdown:
  - Instagram (clicks, trend %)
  - WhatsApp (clicks, trend %)
  - TikTok (clicks, trend %)
  - Facebook (clicks, trend %)
- Clicks by Platform (stacked bar chart by day)
- Platform Distribution (donut chart)
- Top Performing Links list:
  - Link name
  - Platform
  - Click count
  - External link icon

**Tracked Platforms:**
- Instagram
- TikTok
- WhatsApp
- Facebook
- Website
- Custom links

**Data Model:**
```
SocialLink:
  - id: UUID
  - landing_page_id: UUID (FK)
  - platform: enum (instagram, tiktok, whatsapp, facebook, website, custom)
  - label: string
  - url: string
  - display_order: integer
  - active: boolean
  - created_at: timestamp

SocialLinkClick:
  - id: UUID
  - social_link_id: UUID (FK)
  - participant_id: UUID (FK, nullable)
  - ip_address: string
  - user_agent: string
  - created_at: timestamp
```

---

#### 5.1.7 Reviews Module
**Priority: P0 (Critical)**

**Features:**

**Review Engagement Dashboard:**
- Review Clicks count (with trend)
- Click Rate (%) of participants
- This Week count (with comparison)
- Avg Daily Clicks (last 7 days)
- "About This Data" information box explaining tracking limitations
- Review Clicks This Week (line chart)
- Review Funnel:
  - Total Participants
  - Clicked Review Link (count and rate %)
  - Industry benchmark comparison
- Recent Review Clicks list:
  - User name
  - Location/venue
  - Prize won
  - Time ago

**Google Review Integration:**
- Google Business Profile API connection
- Review URL generation per venue
- Click tracking (not actual review submission tracking)
- Promoter attribution via nickname matching in review text
- Manual review matching process

**Review Tracking Limitations:**
- Cannot directly track actual Google review submissions
- Track "review link clicks" as proxy metric
- Scan Google reviews for promoter name/nickname mentions
- Display disclaimer about tracking limitations

**Data Model:**
```
ReviewClick:
  - id: UUID
  - landing_page_id: UUID (FK)
  - participant_id: UUID (FK)
  - promoter_id: UUID (FK, nullable)
  - google_review_url: string
  - clicked: boolean
  - created_at: timestamp

Promoter:
  - id: UUID
  - business_id: UUID (FK)
  - name: string
  - nickname: string (for review matching)
  - email: string
  - phone: string
  - status: enum (active, inactive)
  - created_at: timestamp
```

---

#### 5.1.8 Promoters Module
**Priority: P0 (Critical)**

**Features:**

**Promoter Management:**
- Total Promoters count
- Total Selections count
- Top Performer identification
- Search promoters
- Promoter list with:
  - Rank
  - Name (with avatar)
  - Nickname (for review matching)
  - Selections count
  - Actions (edit, delete)
- Add Promoter modal:
  - Name (required)
  - Nickname (for review matching)
  - Explanation of how promoter tracking works

**Promoter Tracking Method:**
- Visitors land on page and select which promoter referred them
- System tracks selections to show who's driving engagement
- Nicknames help match promoters mentioned in Google reviews

**Promoter Profile:**
- Personal information
- Performance history
- Assigned NFC cards
- Campaign assignments
- Activity log

**Data Model:**
```
Promoter:
  - id: UUID
  - business_id: UUID (FK)
  - user_id: UUID (FK, nullable - for login)
  - name: string
  - nickname: string
  - email: string
  - phone: string
  - avatar_url: string (nullable)
  - status: enum (active, inactive)
  - total_interactions: integer
  - total_signups: integer
  - total_reviews: integer
  - created_at: timestamp
  - updated_at: timestamp

PromoterSelection:
  - id: UUID
  - landing_page_id: UUID (FK)
  - promoter_id: UUID (FK)
  - participant_id: UUID (FK, nullable)
  - created_at: timestamp
```

---

#### 5.1.9 Cards Module
**Priority: P0 (Critical)**

**Features:**

**Card Management:**
- Active Cards count
- Total Interactions count
- Team Members count
- Avg Daily interactions
- Card list with:
  - Card name
  - Card type (NFC Card / QR Code)
  - Status (Active, Inactive)
  - Interactions count
  - Assigned To (promoter name)
  - Last tap time
  - Actions (view, edit, download)
- Order New Card modal:
  - Card Name
  - Assign To (team member dropdown)
  - Card Type (NFC Card / QR Code)
  - Continue button

**NFC Card Specifications:**
- UUID-based identification
- Maps to specific landing page
- Tap tracking with timestamp
- Anti-cloning measures
- Card provisioning workflow

**QR Code Specifications:**
- Generated QR codes linking to landing pages
- Downloadable as PNG/SVG
- Trackable scans
- Customizable design

**Data Model:**
```
Card:
  - id: UUID
  - business_id: UUID (FK)
  - promoter_id: UUID (FK, nullable)
  - landing_page_id: UUID (FK)
  - card_type: enum (nfc, qr_code)
  - nfc_uuid: string (for NFC cards)
  - qr_code_url: string (for QR codes)
  - name: string
  - status: enum (active, inactive, lost, replaced)
  - total_interactions: integer
  - last_tap_at: timestamp
  - created_at: timestamp
  - updated_at: timestamp

CardInteraction:
  - id: UUID
  - card_id: UUID (FK)
  - participant_id: UUID (FK, nullable)
  - ip_address: string
  - device_info: string
  - created_at: timestamp
```

---

#### 5.1.10 Campaigns Module
**Priority: P0 (Critical)**

**Features:**

**Campaign Management:**
- Active Campaigns count
- Scheduled Campaigns count
- Total Signups count
- Avg Conversion Rate (%)
- Campaign list with:
  - Campaign name
  - Venue/location
  - Duration (start - end dates)
  - Status (Active, Completed, Scheduled)
  - Interactions count
  - Signups count
  - Prizes count
  - Progress bar (%)
  - Actions (view, edit, delete)
- New Campaign creation
- Tab switch: Campaigns / Upcoming Events

**Upcoming Events View:**
- Event cards with:
  - Event name
  - Venue/location
  - Date and time
  - Signed up count
  - View Details link
- Add New Event placeholder

**Campaign-Event Relationship:**
- Campaigns can span multiple events
- Events are specific instances within a campaign
- Landing pages can be shared across events

**Data Model:**
```
Campaign:
  - id: UUID
  - business_id: UUID (FK)
  - landing_page_id: UUID (FK)
  - name: string
  - venue_name: string
  - start_date: date
  - end_date: date
  - status: enum (scheduled, active, completed, archived)
  - total_interactions: integer
  - total_signups: integer
  - total_prizes: integer
  - created_at: timestamp
  - updated_at: timestamp

Event:
  - id: UUID
  - campaign_id: UUID (FK)
  - name: string
  - venue_name: string
  - event_date: datetime
  - total_signups: integer
  - created_at: timestamp
```

---

#### 5.1.11 Reports Module
**Priority: P1 (High)**

**Features:**

**Reports & Exports Dashboard:**
- Summary cards:
  - Total Interactions
  - Total Participants
  - Review Clicks
  - Conversion Rate
- 6-Month Trend Overview (line chart)
- Time range selector

**Generate Report Section:**
- Monthly Performance Report
- Audience Data Export
- Engagement Report
- Game Performance Report
- Each with "Generate" button

**Recent Reports:**
- List of previously generated reports
- Report name, date, format, file size
- Download button

**Quick Data Export:**
- Audience Data (CSV, Excel)
- Interaction Data (CSV, JSON)
- Game Data (CSV, PDF)

**Report Types:**

1. **Monthly Performance Report (PDF)**
   - Executive summary
   - Key metrics with trends
   - Promoter performance ranking
   - Game performance breakdown
   - Review engagement stats
   - Recommendations

2. **Audience Data Export (CSV/Excel)**
   - Participant details
   - Contact information
   - Source attribution
   - Prize information
   - Signup dates

3. **Engagement Report (PDF)**
   - Interaction trends
   - Conversion funnels
   - Peak engagement times
   - Geographic distribution
   - Social link performance

4. **Game Performance Report (PDF)**
   - Game play statistics
   - Prize distribution
   - Redemption rates
   - Peak playing times
   - Fraud detection alerts

**Data Model:**
```
Report:
  - id: UUID
  - business_id: UUID (FK)
  - report_type: enum (monthly_performance, audience_data, engagement, game_performance)
  - format: enum (pdf, csv, excel, json)
  - status: enum (pending, completed, failed)
  - file_url: string
  - file_size: integer
  - created_at: timestamp
  - completed_at: timestamp
```

---

#### 5.1.12 Settings Module
**Priority: P1 (High)**

**Features:**

**Business Settings:**
- Business name
- Business type
- Address
- Contact information
- Logo upload
- Google Business Profile link

**User Management:**
- Team members list
- Role assignments
- Invite new users
- Permission management

**Notification Preferences:**
- Email notifications
- In-app notifications
- Report delivery settings

**Integration Settings:**
- Google Business API connection
- Email service configuration
- SMS service configuration
- Payment integration (future)

**Subscription Management:**
- Current plan details
- Billing information
- Upgrade/Downgrade options
- Payment history

---

### 5.2 Promoter Dashboard Module

**Priority: P0 (Critical)**
**Status: NOT DESIGNED - Requires new Figma designs**

#### 5.2.1 Promoter Personal Dashboard
**Features:**
- Personal performance overview:
  - Total Interactions (today, week, month)
  - Total Sign-ups generated
  - Total Reviews attributed
  - Ranking among team
- Quick stats cards with trends
- Recent activity feed
- Upcoming events list

#### 5.2.2 Assigned Cards View
**Features:**
- List of assigned NFC cards
- Card status (active, inactive)
- Interaction count per card
- Last tap time
- QR code download (if applicable)

#### 5.2.3 Event Tracking
**Features:**
- Assigned campaigns and events
- Event details and dates
- Real-time interaction count during events
- Post-event performance summary

#### 5.2.4 Prize Redemption
**Features:**
- Prize validation interface
- Scan/verify prize claims
- Redemption history
- Redemption statistics

#### 5.2.5 Profile Management
**Features:**
- Personal information
- Contact details
- Nickname for review matching
- Password change
- Notification preferences

---

### 5.3 Customer-Facing Landing Page Module

**Priority: P0 (Critical)**
**Status: PREVIEW ONLY in admin - Requires full implementation**

#### 5.3.1 Landing Page Rendering
**Features:**
- Mobile-responsive design
- Dynamic content based on page blocks
- Real-time game interaction
- Form submission handling
- Social link display
- Contact information display

#### 5.3.2 Game Interaction
**Features:**
- Dice roll animation and result
- Spin wheel animation and result
- Prize display after game
- Rate limiting enforcement
- Duplicate prevention

#### 5.3.3 Sign-Up Form
**Features:**
- Name input (required)
- Email input (required, validation)
- Phone input (optional)
- Promoter selection dropdown
- Consent checkbox (GDPR)
- Form submission and confirmation

#### 5.3.4 Review Prompt
**Features:**
- Review prompt display
- Redirect to Google review page
- Thank you message
- Social sharing options

#### 5.3.5 Prize Display
**Features:**
- Prize won display
- Redemption instructions
- Validity period
- QR code for validation (if applicable)

---

## 6. Non-Functional Requirements

### 6.1 Performance Requirements

| Metric | Target | Critical Threshold |
|--------|--------|-------------------|
| Landing page load time | < 2 seconds | < 3 seconds |
| API response time (p95) | < 200ms | < 500ms |
| Game interaction response | < 1 second | < 2 seconds |
| Dashboard load time | < 3 seconds | < 5 seconds |
| Concurrent users supported | 10,000+ | 5,000+ |
| Database query time (p95) | < 100ms | < 300ms |

### 6.2 Availability Requirements

- **Uptime SLA**: 99.9% (excluding maintenance windows)
- **Maintenance windows**: Scheduled, communicated 48 hours in advance
- **Disaster recovery**: RTO < 4 hours, RPO < 1 hour
- **Backup frequency**: Daily full backups, hourly incremental

### 6.3 Security Requirements

- **Authentication**: OAuth 2.0 / JWT-based authentication
- **Password policy**: Minimum 8 characters, complexity requirements
- **Session management**: Configurable timeout, concurrent session limits
- **Data encryption**: AES-256 at rest, TLS 1.3 in transit
- **API security**: Rate limiting, CORS, input validation
- **GDPR compliance**: Data consent, right to deletion, data portability
- **PCI DSS**: Not applicable (no payment processing in v1)

### 6.4 Scalability Requirements

- **Horizontal scaling**: Support auto-scaling for stateless services
- **Database scaling**: Read replicas for analytics queries
- **CDN**: Static assets and landing pages served via CDN
- **Caching**: Redis for session management and frequent queries
- **Queue system**: Async processing for reports, emails, analytics

### 6.5 Compliance Requirements

- **GDPR**: Full compliance for EU users
  - Consent management
  - Data deletion requests
  - Data export functionality
  - Privacy policy requirements
- **CCPA**: Compliance for California users
- **Cookie consent**: Banner and preference management
- **Data retention**: Configurable retention policies

### 6.6 Usability Requirements

- **Mobile-first design**: All customer-facing pages mobile-optimized
- **Accessibility**: WCAG 2.1 AA compliance for admin dashboard
- **Browser support**: Chrome, Firefox, Safari, Edge (latest 2 versions)
- **Language support**: English (v1), multi-language (future)
- **Response time feedback**: Loading states for all async operations

---

## 7. System Architecture

### 7.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        CDN / Edge                            │
│              (Static Assets, Landing Pages)                  │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                   Load Balancer / API Gateway                │
│              (Rate Limiting, SSL Termination)                │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
    ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
    │   Admin API  │  │  Public API  │  │Promoter API  │
    │   Service    │  │   Service    │  │   Service    │
    └──────────────┘  └──────────────┘  └──────────────┘
              │               │               │
              └───────────────┼───────────────┘
                              ▼
    ┌──────────────────────────────────────────────────────┐
    │                  Message Queue (Redis/RabbitMQ)       │
    │     (Async Tasks: Analytics, Emails, Reports)         │
    └──────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
    ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
    │   Primary    │  │   Analytics  │  │   Session    │
    │   Database   │  │   Database   │  │    Cache     │
    │  (PostgreSQL)│  │ (TimescaleDB)│  │   (Redis)    │
    └──────────────┘  └──────────────┘  └──────────────┘
```

### 7.2 Service Components

#### 7.2.1 API Services

**Admin API Service:**
- Business administration endpoints
- Campaign management
- Landing page CRUD
- Analytics queries
- Report generation
- User management

**Public API Service:**
- Landing page rendering
- Game interaction handling
- Form submission processing
- Review click tracking
- Social link tracking
- High-traffic, read-heavy

**Promoter API Service:**
- Promoter authentication
- Personal stats retrieval
- Card management
- Event tracking
- Prize redemption validation

#### 7.2.2 Background Workers

**Analytics Worker:**
- Aggregate interaction data
- Calculate metrics and trends
- Update materialized views
- Generate leaderboard data

**Email Worker:**
- Send transactional emails
- Process email templates
- Handle email queues
- Track email delivery

**Report Worker:**
- Generate PDF reports
- Export CSV/Excel data
- Store generated files
- Notify users of completion

**Game Engine Worker:**
- Process game results
- Validate prize wins
- Enforce rate limits
- Detect fraud patterns

#### 7.2.3 External Integrations

**Google Business API:**
- Review URL generation
- Business profile linking
- (Limited) review data access

**NFC Card Provider API:**
- Card provisioning
- UUID mapping
- Order tracking

**Email Service Provider:**
- Transactional emails
- Marketing emails (future)
- Template management

**SMS Service Provider:**
- OTP verification (future)
- Notification SMS (future)

### 7.3 Technology Stack Recommendations

**Backend:**
- Runtime: Node.js (v18+)
- Framework: Express.js
- Database: MongoDb (primary), Redis (cache/sessions), TimescaleDB (analytics)
- Message Queue: Redis Streams or RabbitMQ
- API Documentation: OpenAPI/Swagger

**Frontend (Admin Dashboard):**
- Framework: React/Next.js or Vue/Nuxt
- State Management: Redux/Zustand or Pinia
- UI Library: TailwindCSS + Headless UI
- Charts: Recharts or Chart.js
- Real-time: WebSocket or Server-Sent Events

**Frontend (Landing Pages):**
- Framework: Next.js (SSR for SEO)
- Styling: TailwindCSS
- Animations: Framer Motion
- Performance: Vercel/Cloudflare Pages

**Infrastructure:**
- Hosting: AWS / GCP / Vercel
- CDN: Cloudflare
- Container Orchestration: Docker + Kubernetes (if needed)
- CI/CD: GitHub Actions
- Monitoring: Sentry + Datadog/New Relic
- Logging: ELK Stack or CloudWatch

---

## 8. Database Schema

### 8.1 Entity Relationship Overview

```
Business (1) ─── (N) User
Business (1) ─── (N) Campaign
Business (1) ─── (N) LandingPage
Business (1) ─── (N) Promoter
Business (1) ─── (N) Card
Business (1) ─── (N) Report
Business (1) ─── (N) SocialLink

Campaign (1) ─── (N) Event
Campaign (N) ─── (1) LandingPage

LandingPage (1) ─── (N) Participant
LandingPage (1) ─── (N) GamePlay
LandingPage (1) ─── (N) ReviewClick
LandingPage (1) ─── (N) SocialLinkClick
LandingPage (N) ─── (1) Card

Promoter (1) ─── (N) PromoterSelection
Promoter (1) ─── (N) Card (assigned)

Participant (1) ─── (N) GamePlay (nullable)
Participant (1) ─── (N) ReviewClick
Participant (1) ─── (N) SocialLinkClick
```

### 8.2 Core Tables

#### 8.2.1 Users & Authentication

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    business_id UUID REFERENCES businesses(id),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    name VARCHAR(255) NOT NULL,
    role ENUM('super_admin', 'business_admin', 'campaign_manager', 'promoter') NOT NULL,
    status ENUM('active', 'inactive', 'suspended') DEFAULT 'active',
    email_verified BOOLEAN DEFAULT FALSE,
    last_login_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    token_hash VARCHAR(255) NOT NULL,
    expires_at TIMESTAMP NOT NULL,
    ip_address VARCHAR(45),
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### 8.2.2 Businesses & Multi-Tenancy

```sql
CREATE TABLE businesses (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    business_type VARCHAR(100),
    address TEXT,
    city VARCHAR(100),
    country VARCHAR(100),
    phone VARCHAR(50),
    email VARCHAR(255),
    logo_url VARCHAR(500),
    google_business_url VARCHAR(500),
    subscription_plan ENUM('free', 'starter', 'professional', 'enterprise') DEFAULT 'free',
    subscription_status ENUM('active', 'cancelled', 'expired') DEFAULT 'active',
    subscription_expires_at TIMESTAMP,
    settings JSONB DEFAULT '{}',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### 8.2.3 Landing Pages

```sql
CREATE TABLE landing_pages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    business_id UUID REFERENCES businesses(id) ON DELETE CASCADE,
    slug VARCHAR(100) NOT NULL,
    title VARCHAR(255) NOT NULL,
    status ENUM('draft', 'published', 'archived') DEFAULT 'draft',
    blocks JSONB NOT NULL DEFAULT '{}',
    promoters JSONB DEFAULT '[]',
    settings JSONB DEFAULT '{}',
    published_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(business_id, slug)
);

CREATE INDEX idx_landing_pages_business_id ON landing_pages(business_id);
CREATE INDEX idx_landing_pages_status ON landing_pages(status);
CREATE INDEX idx_landing_pages_slug ON landing_pages(slug);
```

#### 8.2.4 Promoters

```sql
CREATE TABLE promoters (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    business_id UUID REFERENCES businesses(id) ON DELETE CASCADE,
    user_id UUID REFERENCES users(id) ON DELETE SET NULL,
    name VARCHAR(255) NOT NULL,
    nickname VARCHAR(100),
    email VARCHAR(255),
    phone VARCHAR(50),
    avatar_url VARCHAR(500),
    status ENUM('active', 'inactive') DEFAULT 'active',
    total_interactions INTEGER DEFAULT 0,
    total_signups INTEGER DEFAULT 0,
    total_reviews INTEGER DEFAULT 0,
    metadata JSONB DEFAULT '{}',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_promoters_business_id ON promoters(business_id);
CREATE INDEX idx_promoters_status ON promoters(status);
```

#### 8.2.5 Campaigns & Events

```sql
CREATE TABLE campaigns (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    business_id UUID REFERENCES businesses(id) ON DELETE CASCADE,
    landing_page_id UUID REFERENCES landing_pages(id) ON DELETE SET NULL,
    name VARCHAR(255) NOT NULL,
    venue_name VARCHAR(255),
    description TEXT,
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    status ENUM('scheduled', 'active', 'completed', 'archived') DEFAULT 'scheduled',
    total_interactions INTEGER DEFAULT 0,
    total_signups INTEGER DEFAULT 0,
    total_prizes INTEGER DEFAULT 0,
    settings JSONB DEFAULT '{}',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    campaign_id UUID REFERENCES campaigns(id) ON DELETE CASCADE,
    name VARCHAR(255) NOT NULL,
    venue_name VARCHAR(255),
    event_date TIMESTAMP NOT NULL,
    total_signups INTEGER DEFAULT 0,
    settings JSONB DEFAULT '{}',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_campaigns_business_id ON campaigns(business_id);
CREATE INDEX idx_campaigns_status ON campaigns(status);
CREATE INDEX idx_events_campaign_id ON events(campaign_id);
CREATE INDEX idx_events_event_date ON events(event_date);
```

#### 8.2.6 Participants

```sql
CREATE TABLE participants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    landing_page_id UUID REFERENCES landing_pages(id) ON DELETE CASCADE,
    campaign_id UUID REFERENCES campaigns(id) ON DELETE SET NULL,
    promoter_id UUID REFERENCES promoters(id) ON DELETE SET NULL,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL,
    phone VARCHAR(50),
    status ENUM('confirmed', 'pending', 'bounced') DEFAULT 'pending',
    prize_won VARCHAR(255),
    source VARCHAR(100),
    consent_given BOOLEAN DEFAULT FALSE,
    metadata JSONB DEFAULT '{}',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_participants_landing_page_id ON participants(landing_page_id);
CREATE INDEX idx_participants_campaign_id ON participants(campaign_id);
CREATE INDEX idx_participants_promoter_id ON participants(promoter_id);
CREATE INDEX idx_participants_email ON participants(email);
CREATE INDEX idx_participants_created_at ON participants(created_at);
```

#### 8.2.7 Cards (NFC & QR)

```sql
CREATE TABLE cards (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    business_id UUID REFERENCES businesses(id) ON DELETE CASCADE,
    promoter_id UUID REFERENCES promoters(id) ON DELETE SET NULL,
    landing_page_id UUID REFERENCES landing_pages(id) ON DELETE CASCADE,
    card_type ENUM('nfc', 'qr_code') NOT NULL,
    nfc_uuid VARCHAR(255),
    qr_code_url VARCHAR(500),
    name VARCHAR(255) NOT NULL,
    status ENUM('active', 'inactive', 'lost', 'replaced') DEFAULT 'active',
    total_interactions INTEGER DEFAULT 0,
    last_tap_at TIMESTAMP,
    metadata JSONB DEFAULT '{}',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(nfc_uuid)
);

CREATE TABLE card_interactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    card_id UUID REFERENCES cards(id) ON DELETE CASCADE,
    participant_id UUID REFERENCES participants(id) ON DELETE SET NULL,
    ip_address VARCHAR(45),
    device_info TEXT,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_cards_business_id ON cards(business_id);
CREATE INDEX idx_cards_promoter_id ON cards(promoter_id);
CREATE INDEX idx_cards_nfc_uuid ON cards(nfc_uuid);
CREATE INDEX idx_card_interactions_card_id ON card_interactions(card_id);
CREATE INDEX idx_card_interactions_created_at ON card_interactions(created_at);
```

#### 8.2.8 Games & Prizes

```sql
CREATE TABLE game_plays (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    landing_page_id UUID REFERENCES landing_pages(id) ON DELETE CASCADE,
    participant_id UUID REFERENCES participants(id) ON DELETE SET NULL,
    game_type ENUM('dice_roll', 'spin_wheel') NOT NULL,
    result VARCHAR(255) NOT NULL,
    redeemed BOOLEAN DEFAULT FALSE,
    redeemed_at TIMESTAMP,
    ip_address VARCHAR(45),
    device_fingerprint VARCHAR(255),
    session_id VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE prizes (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    landing_page_id UUID REFERENCES landing_pages(id) ON DELETE CASCADE,
    name VARCHAR(255) NOT NULL,
    probability DECIMAL(5,4) NOT NULL,
    max_redemptions INTEGER,
    current_redemptions INTEGER DEFAULT 0,
    active BOOLEAN DEFAULT TRUE,
    display_order INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_game_plays_landing_page_id ON game_plays(landing_page_id);
CREATE INDEX idx_game_plays_created_at ON game_plays(created_at);
CREATE INDEX idx_game_plays_device_fingerprint ON game_plays(device_fingerprint);
CREATE INDEX idx_prizes_landing_page_id ON prizes(landing_page_id);
```

#### 8.2.9 Reviews & Social Links

```sql
CREATE TABLE review_clicks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    landing_page_id UUID REFERENCES landing_pages(id) ON DELETE CASCADE,
    participant_id UUID REFERENCES participants(id) ON DELETE SET NULL,
    promoter_id UUID REFERENCES promoters(id) ON DELETE SET NULL,
    google_review_url VARCHAR(500),
    clicked BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE social_links (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    landing_page_id UUID REFERENCES landing_pages(id) ON DELETE CASCADE,
    platform ENUM('instagram', 'tiktok', 'whatsapp', 'facebook', 'website', 'custom') NOT NULL,
    label VARCHAR(255) NOT NULL,
    url VARCHAR(500) NOT NULL,
    display_order INTEGER DEFAULT 0,
    active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE social_link_clicks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    social_link_id UUID REFERENCES social_links(id) ON DELETE CASCADE,
    participant_id UUID REFERENCES participants(id) ON DELETE SET NULL,
    ip_address VARCHAR(45),
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE promoter_selections (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    landing_page_id UUID REFERENCES landing_pages(id) ON DELETE CASCADE,
    promoter_id UUID REFERENCES promoters(id) ON DELETE CASCADE,
    participant_id UUID REFERENCES participants(id) ON DELETE SET NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_review_clicks_landing_page_id ON review_clicks(landing_page_id);
CREATE INDEX idx_review_clicks_created_at ON review_clicks(created_at);
CREATE INDEX idx_social_link_clicks_social_link_id ON social_link_clicks(social_link_id);
CREATE INDEX idx_social_link_clicks_created_at ON social_link_clicks(created_at);
CREATE INDEX idx_promoter_selections_promoter_id ON promoter_selections(promoter_id);
```

#### 8.2.10 Reports & Analytics

```sql
CREATE TABLE reports (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    business_id UUID REFERENCES businesses(id) ON DELETE CASCADE,
    report_type ENUM('monthly_performance', 'audience_data', 'engagement', 'game_performance') NOT NULL,
    format ENUM('pdf', 'csv', 'excel', 'json') NOT NULL,
    status ENUM('pending', 'completed', 'failed') DEFAULT 'pending',
    file_url VARCHAR(500),
    file_size INTEGER,
    parameters JSONB DEFAULT '{}',
    error_message TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP
);

CREATE TABLE analytics_daily (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    business_id UUID REFERENCES businesses(id) ON DELETE CASCADE,
    date DATE NOT NULL,
    total_interactions INTEGER DEFAULT 0,
    unique_visitors INTEGER DEFAULT 0,
    return_visitors INTEGER DEFAULT 0,
    total_signups INTEGER DEFAULT 0,
    total_game_plays INTEGER DEFAULT 0,
    total_review_clicks INTEGER DEFAULT 0,
    total_social_clicks INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(business_id, date)
);

CREATE INDEX idx_reports_business_id ON reports(business_id);
CREATE INDEX idx_reports_status ON reports(status);
CREATE INDEX idx_analytics_daily_business_id ON analytics_daily(business_id);
CREATE INDEX idx_analytics_daily_date ON analytics_daily(date);
```

### 8.3 Materialized Views (for Performance)

```sql
-- Promoter Leaderboard Materialized View
CREATE MATERIALIZED VIEW mv_promoter_leaderboard AS
SELECT
    p.id AS promoter_id,
    p.business_id,
    p.name,
    p.nickname,
    COUNT(DISTINCT ci.id) AS total_interactions,
    COUNT(DISTINCT pt.id) AS total_signups,
    COUNT(DISTINCT rc.id) AS total_reviews,
    RANK() OVER (PARTITION BY p.business_id ORDER BY COUNT(DISTINCT ci.id) DESC) AS rank
FROM promoters p
LEFT JOIN promoter_selections ps ON p.id = ps.promoter_id
LEFT JOIN card_interactions ci ON ps.participant_id = ci.participant_id
LEFT JOIN participants pt ON p.id = pt.promoter_id
LEFT JOIN review_clicks rc ON p.id = rc.promoter_id
GROUP BY p.id, p.business_id, p.name, p.nickname;

-- Daily Analytics Materialized View
CREATE MATERIALIZED VIEW mv_daily_analytics AS
SELECT
    lp.business_id,
    DATE(ci.created_at) AS activity_date,
    COUNT(DISTINCT ci.id) AS interactions,
    COUNT(DISTINCT ci.ip_address) AS unique_visitors,
    COUNT(DISTINCT pt.id) AS signups,
    COUNT(DISTINCT gp.id) AS game_plays,
    COUNT(DISTINCT rc.id) AS review_clicks
FROM card_interactions ci
JOIN cards c ON ci.card_id = c.id
JOIN landing_pages lp ON c.landing_page_id = lp.id
LEFT JOIN participants pt ON ci.participant_id = pt.id
LEFT JOIN game_plays gp ON pt.id = gp.participant_id
LEFT JOIN review_clicks rc ON pt.id = rc.participant_id
GROUP BY lp.business_id, DATE(ci.created_at);
```

---

## 9. API Specifications

### 9.1 API Design Principles

- **RESTful** architecture with resource-based URLs
- **JSON** request/response format
- **JWT** authentication via Bearer tokens
- **Versioned** APIs (`/api/v1/...`)
- **Pagination** for list endpoints (cursor-based or offset)
- **Rate limiting** per user/IP
- **Comprehensive error responses** with error codes

### 9.2 Authentication Endpoints

```
POST   /api/v1/auth/register          - Register new business admin
POST   /api/v1/auth/login             - Login and receive JWT
POST   /api/v1/auth/logout            - Invalidate session
POST   /api/v1/auth/refresh           - Refresh JWT token
POST   /api/v1/auth/forgot-password   - Request password reset
POST   /api/v1/auth/reset-password    - Reset password with token
GET    /api/v1/auth/me                - Get current user profile
PUT    /api/v1/auth/profile           - Update current user profile
PUT    /api/v1/auth/password          - Change password
```

### 9.3 Business Management Endpoints

```
GET    /api/v1/businesses              - List businesses (super admin)
POST   /api/v1/businesses              - Create business (super admin)
GET    /api/v1/businesses/:id          - Get business details
PUT    /api/v1/businesses/:id          - Update business
DELETE /api/v1/businesses/:id          - Delete business (soft delete)
GET    /api/v1/businesses/:id/settings - Get business settings
PUT    /api/v1/businesses/:id/settings - Update business settings
```

### 9.4 User Management Endpoints

```
GET    /api/v1/users                   - List users in business
POST   /api/v1/users                   - Invite new user
GET    /api/v1/users/:id               - Get user details
PUT    /api/v1/users/:id               - Update user
DELETE /api/v1/users/:id               - Deactivate user
PUT    /api/v1/users/:id/role          - Update user role
```

### 9.5 Landing Page Endpoints

```
GET    /api/v1/landing-pages                  - List landing pages
POST   /api/v1/landing-pages                  - Create landing page
GET    /api/v1/landing-pages/:id              - Get landing page details
PUT    /api/v1/landing-pages/:id              - Update landing page
DELETE /api/v1/landing-pages/:id              - Delete landing page
POST   /api/v1/landing-pages/:id/duplicate    - Duplicate landing page
POST   /api/v1/landing-pages/:id/publish      - Publish landing page
POST   /api/v1/landing-pages/:id/unpublish    - Unpublish landing page
GET    /api/v1/landing-pages/:id/preview      - Preview landing page
```

### 9.6 Public Landing Page Endpoints (High Traffic)

```
GET    /api/v1/public/pages/:slug             - Get landing page content
POST   /api/v1/public/pages/:slug/submit      - Submit sign-up form
POST   /api/v1/public/pages/:slug/game        - Play game (dice/spin)
POST   /api/v1/public/pages/:slug/review      - Track review click
POST   /api/v1/public/pages/:slug/social/:id  - Track social link click
POST   /api/v1/public/pages/:slug/select-promoter - Select promoter
```

### 9.7 Promoter Endpoints

```
GET    /api/v1/promoters                      - List promoters
POST   /api/v1/promoters                      - Create promoter
GET    /api/v1/promoters/:id                  - Get promoter details
PUT    /api/v1/promoters/:id                  - Update promoter
DELETE /api/v1/promoters/:id                  - Delete promoter
GET    /api/v1/promoters/:id/stats            - Get promoter statistics
GET    /api/v1/promoters/:id/cards            - Get assigned cards
GET    /api/v1/promoters/:id/leaderboard      - Get leaderboard position
```

### 9.8 Campaign Endpoints

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
```

### 9.9 Card Endpoints

```
GET    /api/v1/cards                          - List cards
POST   /api/v1/cards                          - Create/order new card
GET    /api/v1/cards/:id                      - Get card details
PUT    /api/v1/cards/:id                      - Update card
DELETE /api/v1/cards/:id                      - Deactivate card
POST   /api/v1/cards/:id/assign               - Assign card to promoter
POST   /api/v1/cards/:id/unassign             - Unassign card
GET    /api/v1/cards/:id/interactions         - Get card interactions
POST   /api/v1/cards/resolve/:nfcUuid         - Resolve NFC UUID to page
```

### 9.10 Participant Endpoints

```
GET    /api/v1/participants                   - List participants
GET    /api/v1/participants/:id               - Get participant details
PUT    /api/v1/participants/:id               - Update participant
DELETE /api/v1/participants/:id               - Delete participant
GET    /api/v1/participants/export            - Export participants (CSV)
GET    /api/v1/participants/stats             - Get participant statistics
```

### 9.11 Analytics Endpoints

```
GET    /api/v1/analytics/overview             - Get overview metrics
GET    /api/v1/analytics/interactions         - Get interaction analytics
GET    /api/v1/analytics/peak-hours           - Get peak activity hours
GET    /api/v1/analytics/geographic           - Get geographic distribution
GET    /api/v1/analytics/cards                - Get card performance
GET    /api/v1/analytics/promoters            - Get promoter leaderboard
GET    /api/v1/analytics/trends               - Get trend data
```

### 9.12 Game & Prize Endpoints

```
GET    /api/v1/games/stats                    - Get game statistics
GET    /api/v1/games/plays                    - List game plays
GET    /api/v1/games/prizes                   - List prizes
POST   /api/v1/games/prizes                   - Create prize
PUT    /api/v1/games/prizes/:id               - Update prize
DELETE /api/v1/games/prizes/:id               - Delete prize
GET    /api/v1/games/prizes/distribution      - Get prize distribution
POST   /api/v1/games/:playId/redeem           - Redeem prize
```

### 9.13 Review Endpoints

```
GET    /api/v1/reviews/stats                  - Get review statistics
GET    /api/v1/reviews/clicks                 - List review clicks
GET    /api/v1/reviews/funnel                 - Get review funnel data
GET    /api/v1/reviews/recent                 - Get recent review clicks
GET    /api/v1/reviews/google                 - Sync Google reviews (if API allows)
```

### 9.14 Social Links Endpoints

```
GET    /api/v1/social-links                   - List social links
POST   /api/v1/social-links                   - Create social link
PUT    /api/v1/social-links/:id               - Update social link
DELETE /api/v1/social-links/:id               - Delete social link
GET    /api/v1/social-links/stats             - Get social link statistics
GET    /api/v1/social-links/top               - Get top performing links
```

### 9.15 Report Endpoints

```
GET    /api/v1/reports                        - List reports
POST   /api/v1/reports/generate               - Generate new report
GET    /api/v1/reports/:id                    - Get report status
GET    /api/v1/reports/:id/download           - Download report
DELETE /api/v1/reports/:id                    - Delete report
POST   /api/v1/reports/export/quick           - Quick data export
```

### 9.16 Event Tracking Endpoints

```
POST   /api/v1/events/:id/track-interaction   - Track interaction at event
GET    /api/v1/events/:id/realtime-stats      - Get real-time event stats
GET    /api/v1/events/:id/signups             - Get event sign-ups
```

---

## 10. Third-Party Integrations

### 10.1 Google Business API

**Purpose:** Google review tracking and business profile linking

**Capabilities:**
- Generate Google review URLs for businesses
- Link Google Business profiles to accounts
- (Limited) access to review data via API

**Limitations:**
- Cannot directly track individual review submissions from platform
- Must rely on click tracking as proxy metric
- Review attribution to promoters via text matching (nicknames)

**Implementation:**
```javascript
// Google Review URL Generation
const googleReviewUrl = `https://search.google.com/local/writereview?placeid=${placeId}`;

// Review Sync (if API access granted)
const reviews = await googleMyBusiness.reviews.list({
  parent: `accounts/${accountId}/locations/${locationId}`
});
```

### 10.2 NFC Card Provider Integration

**Purpose:** NFC card provisioning and UUID management

**Providers (Examples):**
- Taglio
- GoToTags
- Custom NFC suppliers

**Capabilities:**
- Order NFC cards in bulk
- Receive NFC UUIDs
- Map UUIDs to landing pages
- Track card activation

**Implementation:**
```javascript
// NFC Card Order
const order = await nfcProvider.orders.create({
  quantity: 100,
  type: 'NTAG213',
  customization: {
    logo: business.logoUrl,
    color: 'custom'
  }
});

// UUID Mapping
await db.cards.create({
  nfc_uuid: nfcTag.uuid,
  landing_page_id: page.id,
  promoter_id: promoter.id
});
```

### 10.3 QR Code Generation

**Purpose:** Generate QR codes for digital access

**Libraries:**
- `qrcode` (Node.js)
- `qrcode-generator` (Python)

**Implementation:**
```javascript
const QRCode = require('qrcode');

const qrCodeUrl = await QRCode.toDataURL(
  `https://promotercard.com/${landingPageSlug}`,
  {
    width: 500,
    margin: 2,
    color: {
      dark: '#000000',
      light: '#FFFFFF'
    }
  }
);
```

### 10.4 Email Service Integration

**Purpose:** Transactional and marketing emails

**Providers:**
- SendGrid
- AWS SES
- Mailgun

**Email Types:**
- Welcome email for new participants
- Prize redemption confirmation
- Report generation notification
- Password reset
- User invitation

**Implementation:**
```javascript
// Using SendGrid
const sgMail = require('@sendgrid/mail');

await sgMail.send({
  to: participant.email,
  from: 'noreply@promotercard.com',
  templateId: 'welcome_email',
  dynamic_template_data: {
    name: participant.name,
    prize: participant.prize_won
  }
});
```

### 10.5 Analytics & Monitoring

**Purpose:** Application monitoring and error tracking

**Tools:**
- Sentry (error tracking)
- Datadog/New Relic (APM)
- Google Analytics (customer-facing pages)
- Mixpanel/Amplitude (product analytics)

---

## 11. Security Requirements

### 11.1 Authentication Security

**Password Policy:**
- Minimum 8 characters
- At least 1 uppercase letter
- At least 1 number
- At least 1 special character
- Password breach checking (Have I Been Pwned API)
- Password expiration: Not required (NIST guidelines)

**Session Management:**
- JWT with short-lived access tokens (15 minutes)
- Refresh tokens with longer expiry (7 days)
- Session invalidation on logout
- Concurrent session limits (configurable)
- IP-based session binding (optional)

**Multi-Factor Authentication (MFA):**
- TOTP-based MFA for admin users
- SMS-based OTP (optional, future)
- MFA enforcement for super admins

### 11.2 API Security

**Rate Limiting:**
```
Public APIs: 100 requests/minute/IP
Admin APIs: 1000 requests/minute/user
Authentication: 5 attempts/minute/IP
Game APIs: 12 requests/hour/device
```

**Input Validation:**
- All inputs validated and sanitized
- SQL injection prevention (parameterized queries)
- XSS prevention (output encoding)
- CSRF protection for state-changing operations

**CORS Policy:**
- Whitelist allowed origins
- Restrict methods (GET, POST, PUT, DELETE)
- Disable credentials for public APIs

### 11.3 Data Protection

**Encryption:**
- At rest: AES-256 encryption for sensitive data
- In transit: TLS 1.3 for all communications
- Database: Transparent Data Encryption (TDE)
- Backups: Encrypted backup storage

**Sensitive Data Handling:**
- Passwords: Bcrypt hashing (cost factor 12)
- PII: Encrypted at rest, access logged
- API keys: Stored in secrets manager
- Tokens: Hashed before storage

### 11.4 GDPR Compliance

**Consent Management:**
- Explicit consent checkbox on sign-up forms
- Consent timestamp and IP recorded
- Consent withdrawal mechanism
- Granular consent options (email, SMS, analytics)

**Data Subject Rights:**
- Right to access: Export all personal data
- Right to deletion: Anonymize or delete data
- Right to portability: Provide data in machine-readable format
- Right to rectification: Update personal data

**Data Retention:**
- Configurable retention periods
- Automatic deletion after retention period
- Anonymization for analytics data

**Privacy Policy:**
- Clear, accessible privacy policy
- Cookie consent banner
- Data processing agreement for businesses

### 11.5 Fraud Prevention

**Game Fraud:**
- Device fingerprinting
- Rate limiting per device/IP
- Session-based play tracking
- Duplicate submission prevention
- Bot detection (CAPTCHA if needed)

**Review Fraud:**
- Review URL click validation
- Time-based validation (must interact before review)
- Pattern detection (unusual activity spikes)

**Card Fraud:**
- NFC UUID validation
- Card deactivation for lost/stolen cards
- Interaction anomaly detection

---

## 12. Performance Requirements

### 12.1 Response Time Targets

| Endpoint Type | Target (p95) | Target (p99) |
|---------------|--------------|--------------|
| Landing page load | < 200ms | < 500ms |
| Admin dashboard | < 500ms | < 1000ms |
| Analytics queries | < 1000ms | < 2000ms |
| Game interaction | < 300ms | < 500ms |
| Form submission | < 500ms | < 1000ms |
| Report generation | Async | < 60s |

### 12.2 Caching Strategy

**Redis Cache Layers:**
- Session storage
- Frequently accessed data (business settings, landing pages)
- Analytics aggregations (hourly/daily rollups)
- Rate limiting counters
- Leaderboard data

**CDN Caching:**
- Static assets (images, CSS, JS)
- Landing page HTML (with cache invalidation on publish)
- QR code images

**Cache Invalidation:**
- On data update (landing pages, settings)
- TTL-based expiration
- Manual invalidation via admin panel

### 12.3 Database Optimization

**Indexing Strategy:**
- All foreign keys indexed
- Composite indexes for common queries
- Partial indexes for filtered queries
- Regular index maintenance

**Query Optimization:**
- Use EXPLAIN ANALYZE for slow queries
- Avoid N+1 queries (use eager loading)
- Paginate large result sets
- Use materialized views for complex aggregations

**Connection Pooling:**
- PgBouncer or similar connection pooler
- Configurable pool size based on load
- Connection timeout and retry logic

### 12.4 Scalability Considerations

**Horizontal Scaling:**
- Stateless API services
- Load balancer with health checks
- Auto-scaling based on CPU/memory/request rate
- Database read replicas for analytics

**Async Processing:**
- Message queue for background tasks
- Priority queues for critical tasks
- Dead letter queue for failed tasks
- Retry logic with exponential backoff

---

## 13. Scalability & Future Roadmap

### 13.1 Phase 1: MVP (Current)

**Features:**
- Admin dashboard (all modules)
- Basic promoter dashboard
- Customer-facing landing pages
- Game engine (dice roll, spin wheel)
- NFC card and QR code support
- Google review click tracking
- Basic analytics and reporting
- Single business support

**Timeline:** 8-12 weeks

### 13.2 Phase 2: Enhanced Features

**Features:**
- Advanced promoter dashboard (mobile app)
- Multi-business support (franchise model)
- Subscription billing integration
- Email marketing automation
- SMS notifications
- Advanced fraud detection
- A/B testing for landing pages
- Custom domain support
- White-label options

**Timeline:** 8-10 weeks

### 13.3 Phase 3: Advanced Analytics & AI

**Features:**
- Predictive analytics (ML-based)
- Automated campaign optimization
- Customer segmentation
- Sentiment analysis on reviews
- Chatbot integration
- Advanced reporting (custom dashboards)
- API marketplace (third-party integrations)
- Mobile app for customers

**Timeline:** 10-12 weeks

### 13.4 Phase 4: Enterprise Features

**Features:**
- SSO integration (SAML, OAuth)
- Advanced role-based access control
- Audit logging
- Compliance reporting (SOC 2, ISO 27001)
- Dedicated infrastructure options
- SLA guarantees
- Priority support
- Custom development

**Timeline:** 8-10 weeks

---

## 14. Assumptions & Dependencies

### 14.1 Assumptions

1. **NFC Card Supply**: Reliable NFC card supplier available with consistent UUID format
2. **Google API Access**: Google Business API access granted for review tracking
3. **Mobile-First**: Majority of customer interactions occur on mobile devices
4. **Venue WiFi**: Venues have reliable internet for NFC tap processing
5. **Promoter Training**: Businesses will train promoters on platform usage
6. **Legal Compliance**: Businesses are responsible for local promotional regulations
7. **Payment Processing**: Third-party payment provider handles subscriptions

### 14.2 External Dependencies

| Dependency | Purpose | Risk Level | Mitigation |
|------------|---------|------------|------------|
| NFC Card Suppliers | Card provisioning | Medium | Multiple suppliers, buffer stock |
| Google Business API | Review tracking | Low | Fallback to click tracking |
| Email Service Provider | Transactional emails | Low | Multiple providers configured |
| Cloud Hosting | Infrastructure | Low | Multi-region deployment |
| CDN | Static asset delivery | Low | Multiple CDN providers |
| Payment Gateway | Subscription billing | Medium | Backup payment processor |

### 14.3 Technical Constraints

1. **Browser Support**: Modern browsers only (no IE11)
2. **Mobile Browsers**: iOS Safari 14+, Chrome Mobile 90+
3. **NFC Compatibility**: iOS 11+ (NFC reading), Android 5.0+
4. **API Rate Limits**: External APIs may have rate limits
5. **Data Residency**: GDPR requires EU data residency option

---

## 15. Success Metrics

### 15.1 Business Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Customer Acquisition Cost (CAC) | < $100 | Marketing spend / new customers |
| Monthly Recurring Revenue (MRR) | $50K by Month 6 | Subscription revenue |
| Churn Rate | < 5% monthly | Cancelled subscriptions / total |
| Customer Lifetime Value (LTV) | > $1000 | Average revenue per customer |
| Net Promoter Score (NPS) | > 50 | Customer surveys |

### 15.2 Product Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Landing Page Load Time | < 2 seconds | Performance monitoring |
| Game Completion Rate | > 80% | Game plays / page views |
| Sign-Up Conversion Rate | > 30% | Sign-ups / unique visitors |
| Review Click Rate | > 25% | Review clicks / participants |
| Promoter Adoption Rate | > 90% | Active promoters / total |

### 15.3 Technical Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Uptime | 99.9% | Monitoring tools |
| API Response Time (p95) | < 200ms | APM tools |
| Error Rate | < 0.1% | Error tracking |
| Database Query Time (p95) | < 100ms | Database monitoring |
| Page Speed Score | > 90 | Lighthouse audits |

---

## Appendix A: Glossary

| Term | Definition |
|------|------------|
| NFC | Near Field Communication - wireless technology for short-range data transfer |
| Landing Page | Customer-facing web page linked to NFC card |
| Promoter | Staff member who distributes NFC cards at events |
| Interaction | Single tap/scan of an NFC card or QR code |
| Campaign | Marketing initiative spanning a date range |
| Game | Interactive element (dice roll, spin wheel) for engagement |
| Redemption | Participant claiming a prize they won |
| Review Click | Participant clicking the Google review link |

---

## Appendix B: Wireframe References

All wireframes referenced in this document are located in:
`/figma-asset/admin-dashboard/`

Key screens:
- Dashboard: `dashboard/performance-overview-01.png`, `dashboard/performance-overview-02.png`
- Analytics: `analytics/interaction-analytics-01.png`, `analytics/more-analytics-02.png`
- Landing Pages: `landing-pages/landing-page-builder-01.png` through `more-06.png`
- Games: `games-and-prizes/game-performance-01.png`, `more-02.png`
- Cards: `cards/card-management-01.png`, `order-new-card.png`
- Campaigns: `campaigns/campaigns.png`, `upcoming-events.png`
- Promoters: `promoters/promoters-01.png`, `add-promoter.png`
- Participants: `participants/captured-audienced-data.png`
- Reviews: `reviews/review-engagement-01.png`, `review-engagement-02.png`
- Reports: `reports/report-01.png`, `generate-and-export-report-02.png`
- Social Links: `social-links/social-links-01.png`, `more-02.png`

---

## Appendix C: API Response Format

### Success Response
```json
{
  "success": true,
  "data": {
    // Response data
  },
  "meta": {
    "timestamp": "2026-04-07T10:30:00Z",
    "requestId": "req_abc123"
  }
}
```

### Error Response
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ]
  },
  "meta": {
    "timestamp": "2026-04-07T10:30:00Z",
    "requestId": "req_abc123"
  }
}
```

### Paginated Response
```json
{
  "success": true,
  "data": [
    // Array of items
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5,
    "hasNext": true,
    "hasPrev": false
  },
  "meta": {
    "timestamp": "2026-04-07T10:30:00Z",
    "requestId": "req_abc123"
  }
}
```

---

## Document Approval

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Product Owner | | | |
| Technical Lead | | | |
| Backend Lead | | | |
| Frontend Lead | | | |

---

**End of Product Requirements Document**
