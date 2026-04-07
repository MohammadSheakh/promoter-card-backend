# Product Requirements Document v2 (PRD)
## PromoterCard - NFC-Powered Event Promotion Platform

**Version:** 2.0 (MongoDB Edition)  
**Last Updated:** April 7, 2026  
**Status:** Draft - Ready for Review  
**Tech Stack:** Node.js + Express.js + MongoDB + Mongoose  
**Data Modeling:** Referencing Approach (Manual References)  

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Technology Stack](#2-technology-stack)
3. [MongoDB Data Modeling Strategy](#3-mongodb-data-modeling-strategy)
4. [User Roles & Permissions](#4-user-roles--permissions)
5. [Functional Requirements](#5-functional-requirements)
6. [Mongoose Schema Definitions](#6-mongoose-schema-definitions)
7. [API Specifications](#7-api-specifications)
8. [Indexing Strategy](#8-indexing-strategy)
9. [Aggregation Pipelines](#9-aggregation-pipelines)
10. [Third-Party Integrations](#10-third-party-integrations)
11. [Security Requirements](#11-security-requirements)
12. [Performance Optimization](#12-performance-optimization)
13. [Scalability & Sharding Strategy](#13-scalability--sharding-strategy)
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

### 1.2 Why MongoDB?

**Rationale for MongoDB Selection:**

✅ **Flexible Schema**: Landing page blocks are highly customizable; MongoDB's document model accommodates varying structures without schema migrations  
✅ **High Write Throughput**: Game interactions, analytics events, and click tracking generate high-volume writes  
✅ **Horizontal Scalability**: Built-in sharding for multi-tenant SaaS growth  
✅ **Geospatial Queries**: Future location-based features (venue proximity, geographic analytics)  
✅ **JSON-Native**: Seamless integration with Node.js APIs and frontend  
✅ **Time Series Collections**: Built-in support for analytics data (MongoDB 5.0+)  

### 1.3 Why Referencing Approach?

**Rationale for Manual References (Not Embedding):**

✅ **Data Normalization**: Avoids data duplication across documents  
✅ **Consistent Updates**: Single source of truth for entities (e.g., promoter name changes propagate everywhere)  
✅ **Document Size Limits**: MongoDB's 16MB document limit is avoided  
✅ **Independent Lifecycle**: Entities evolve independently (e.g., participants outlive campaigns)  
✅ **Complex Relationships**: Many-to-many relationships (promoters ↔ campaigns ↔ landing pages)  
✅ **Index Efficiency**: Smaller documents = better index utilization  
✅ **Transaction Support**: MongoDB multi-document transactions for referential integrity  

**Trade-offs Accepted:**
- More `$lookup` operations in aggregations
- Slightly more complex queries
- Potential for orphaned references (mitigated by application-level constraints)

---

## 2. Technology Stack

### 2.1 Backend Stack

```yaml
Runtime: Node.js v20.x (LTS)
Framework: Express.js v4.x
Language: TypeScript v5.x (strict mode)
Database: MongoDB v7.x (Community or Atlas)
ODM: Mongoose v8.x
Authentication: JWT (jsonwebtoken) + bcrypt
Validation: Joi / zod
Error Handling: express-async-errors + custom error classes
Logging: Winston + Morgan
Testing: Jest + Supertest
API Documentation: Swagger / OpenAPI 3.0
```

### 2.2 Middleware & Utilities

```yaml
CORS: cors
Compression: compression
Helmet: helmet (security headers)
Rate Limiting: express-rate-limit + rate-limit-redis
File Upload: multer + cloudinary (images)
QR Generation: qrcode
Email: nodemailer + SendGrid
Queue System: BullMQ + Redis
WebSocket: socket.io (real-time updates)
```

### 2.3 Development Tools

```yaml
Linting: ESLint + Prettier
Git Hooks: Husky + lint-staged
Environment: dotenv
Build: ts-node-dev (dev), tsc + node (prod)
Process Manager: PM2
Container: Docker + docker-compose
```

### 2.4 Project Structure

```
prompter-card-backend/
├── src/
│   ├── config/              # Configuration files
│   │   ├── database.ts      # MongoDB connection
│   │   ├── environment.ts   # Environment variables
│   │   └── redis.ts         # Redis connection
│   │
│   ├── models/              # Mongoose models (schemas)
│   │   ├── User.model.ts
│   │   ├── Business.model.ts
│   │   ├── LandingPage.model.ts
│   │   ├── Promoter.model.ts
│   │   ├── Campaign.model.ts
│   │   ├── Event.model.ts
│   │   ├── Participant.model.ts
│   │   ├── Card.model.ts
│   │   ├── GamePlay.model.ts
│   │   ├── Prize.model.ts
│   │   ├── ReviewClick.model.ts
│   │   ├── SocialLink.model.ts
│   │   ├── SocialLinkClick.model.ts
│   │   ├── PromoterSelection.model.ts
│   │   ├── CardInteraction.model.ts
│   │   ├── Report.model.ts
│   │   ├── AnalyticsDaily.model.ts
│   │   └── Session.model.ts
│   │
│   ├── controllers/         # Request handlers
│   │   ├── auth.controller.ts
│   │   ├── business.controller.ts
│   │   ├── landingPage.controller.ts
│   │   ├── promoter.controller.ts
│   │   ├── campaign.controller.ts
│   │   ├── participant.controller.ts
│   │   ├── card.controller.ts
│   │   ├── game.controller.ts
│   │   ├── review.controller.ts
│   │   ├── social.controller.ts
│   │   ├── analytics.controller.ts
│   │   └── report.controller.ts
│   │
│   ├── services/            # Business logic
│   │   ├── auth.service.ts
│   │   ├── landingPage.service.ts
│   │   ├── game.service.ts
│   │   ├── analytics.service.ts
│   │   ├── report.service.ts
│   │   └── notification.service.ts
│   │
│   ├── middleware/          # Express middleware
│   │   ├── auth.middleware.ts
│   │   ├── validation.middleware.ts
│   │   ├── error.middleware.ts
│   │   └── rateLimit.middleware.ts
│   │
│   ├── routes/              # API routes
│   │   ├── auth.routes.ts
│   │   ├── business.routes.ts
│   │   ├── landingPage.routes.ts
│   │   └── ...
│   │
│   ├── validators/          # Request validation schemas
│   │   ├── auth.validator.ts
│   │   ├── landingPage.validator.ts
│   │   └── ...
│   │
│   ├── utils/               # Utility functions
│   │   ├── apiResponse.ts
│   │   ├── errorHandler.ts
│   │   ├── logger.ts
│   │   └── qrGenerator.ts
│   │
│   ├── queues/              # BullMQ job definitions
│   │   ├── analytics.queue.ts
│   │   ├── email.queue.ts
│   │   └── report.queue.ts
│   │
│   ├── types/               # TypeScript type definitions
│   │   ├── express.d.ts
│   │   └── models.d.ts
│   │
│   └── app.ts               # Express app setup
│
├── tests/                   # Test files
├── migrations/              # Database migrations
├── scripts/                 # Utility scripts
├── .env.example
├── package.json
└── tsconfig.json
```

---

## 3. MongoDB Data Modeling Strategy

### 3.1 Design Principles

#### 3.1.1 Referencing Strategy

**All relationships use ObjectId references, NOT embedded documents.**

```typescript
// ❌ BAD: Embedding (Avoid this)
interface BusinessDocument {
  promoters: {
    name: string;
    email: string;
    // ... embedded promoter data
  }[];
}

// ✅ GOOD: Referencing (Use this)
interface BusinessDocument {
  promoters: Types.ObjectId[]; // References to Promoter collection
}
```

#### 3.1.2 When to Reference vs. Embed

| Scenario | Strategy | Rationale |
|----------|----------|-----------|
| One-to-many with independent lifecycle | **Reference** | Promoters exist independently of businesses |
| One-to-few with tight coupling | **Reference** | Maintain flexibility for future changes |
| Many-to-many | **Reference** | MongoDB doesn't support embedded many-to-many well |
| Configuration data | **Reference** | Landing page blocks are complex and variable |
| Audit logs / history | **Reference** | Separate collection for scalability |
| Analytics data | **Reference + Time Series** | Use MongoDB time series collections |

#### 3.1.3 Reference Types

**1. Manual References (Primary Approach)**
```typescript
interface CampaignDocument extends Document {
  business: Types.ObjectId;      // References Business._id
  landingPage: Types.ObjectId;   // References LandingPage._id
  promoter?: Types.ObjectId;     // Optional reference
}
```

**2. Array of References**
```typescript
interface BusinessDocument extends Document {
  promoters: Types.ObjectId[];       // Array of Promoter._id
  landingPages: Types.ObjectId[];    // Array of LandingPage._id
  campaigns: Types.ObjectId[];       // Array of Campaign._id
}
```

**3. Polymorphic References (Rare)**
```typescript
interface CardInteractionDocument extends Document {
  cardType: 'nfc' | 'qr_code';
  card: Types.ObjectId;              // References Card._id
  participant?: Types.ObjectId;      // References Participant._id (optional)
}
```

### 3.2 Population Strategy

**Use Mongoose `populate()` strategically:**

```typescript
// Single reference population
const campaign = await Campaign.findById(id)
  .populate('business', 'name slug')
  .populate('landingPage', 'title slug')
  .populate('promoter', 'name nickname');

// Array population with filtering
const business = await Business.findById(id)
  .populate({
    path: 'promoters',
    select: 'name nickname totalInteractions status',
    match: { status: 'active' },
    options: { sort: { totalInteractions: -1 } }
  });

// Nested population (use sparingly)
const participant = await Participant.findById(id)
  .populate('promoter', 'name')
  .populate({
    path: 'campaign',
    populate: { path: 'landingPage', select: 'title' }
  });
```

**Population Guidelines:**
- ✅ Populate in controllers, not services (keep services clean)
- ✅ Limit population depth (max 2 levels)
- ✅ Select only needed fields in population
- ✅ Use lean queries when population not needed
- ❌ Avoid populating in loops (use aggregation `$lookup` instead)

---

## 4. User Roles & Permissions

### 4.1 Role Hierarchy

```
Super Admin (Platform Owner)
└── Business Admin (Venue Owner/Manager)
    ├── Campaign Manager
    ├── Promoter
    └── Customer (End User - unauthenticated)
```

### 4.2 Role Definitions

#### 4.2.1 Super Admin
- Platform-level administration
- Business account management
- Subscription and billing oversight
- System-wide analytics
- Global settings management

#### 4.2.2 Business Admin
- Full access to all business data
- Manage promoters and campaigns
- Configure landing pages and games
- View all analytics and reports
- Manage NFC cards and QR codes
- Configure social links and integrations

#### 4.2.3 Campaign Manager
- Create and manage campaigns
- Assign promoters to events
- Monitor campaign performance
- Generate reports
- Limited admin access (no business settings)

#### 4.2.4 Promoter
- View personal performance dashboard
- See assigned NFC cards
- Track personal stats (interactions, signups, reviews)
- View upcoming events
- Validate prize redemptions
- Update personal profile

#### 4.2.5 Customer (End User)
- Access landing pages via NFC/QR
- Play games (dice roll, spin wheel)
- Submit sign-up forms
- Leave Google reviews
- View and claim prizes
- Access social links

---

## 5. Functional Requirements

### 5.1 Admin Dashboard Module

#### 5.1.1 Dashboard Overview
**Priority: P0 (Critical)**

**Features:**
- Performance overview cards with trend percentages
- Time range selector (Today, 7 Days, 30 Days, Custom)
- Promoter Leaderboard with ranked performance
- Real-time data updates via WebSocket

**Data Source:** Aggregation pipelines on CardInteraction, Participant, GamePlay, ReviewClick collections

---

#### 5.1.2 Analytics Module
**Priority: P0 (Critical)**

**Features:**
- Interaction Analytics (total, unique, return visitors)
- Peak Activity Hours (by hour of day)
- Geographic Distribution (by location)
- Card Performance Comparison
- Promoter Leaderboard

**Implementation:** MongoDB aggregation pipelines with `$group`, `$facet`, and time-series collections

---

#### 5.1.3 Landing Pages Module
**Priority: P0 (Critical)**

**Features:**
- Landing Page Editor with modular blocks
- Page URL configuration: `promotercard.com/{slug}`
- Block types: Hero, Event Details, Description, Prize Game, Social Links, Contact Info
- Live Preview (mobile and desktop)
- Duplicate, Publish, Archive functionality

**Data Model:** JSONB-like structure in MongoDB for flexible block configurations

---

#### 5.1.4 Games & Prizes Module
**Priority: P0 (Critical)**

**Features:**
- Game Performance Dashboard (plays, redemptions)
- Prize Distribution with redemption rates
- Peak Playing Hours
- Game type breakdown (Dice Roll vs Spin Wheel)

**Game Logic:**
- Dice Roll: 6 outcomes mapped to prizes with weighted probabilities
- Spin Wheel: 4-8 segments with configurable weights
- Rate limiting: 1 play per 5 minutes per device fingerprint
- Anti-fraud: Session tracking, IP monitoring

---

#### 5.1.5 Participants Module
**Priority: P0 (Critical)**

**Features:**
- Captured Audience Data with search and filters
- Export to CSV
- Email/Phone capture rates
- Status management (Confirmed, Pending)

---

#### 5.1.6 Promoters Module
**Priority: P0 (Critical)**

**Features:**
- Promoter list with ranking
- Add/Edit/Delete promoters
- Nickname for review matching
- Selection tracking (which promoter referred whom)

---

#### 5.1.7 Cards Module
**Priority: P0 (Critical)**

**Features:**
- NFC Card and QR Code management
- Card assignment to promoters
- Tap tracking and analytics
- Order new cards workflow
- QR code generation

---

#### 5.1.8 Campaigns Module
**Priority: P0 (Critical)**

**Features:**
- Campaign creation and management
- Event scheduling within campaigns
- Performance tracking per campaign
- Progress indicators
- Upcoming events view

---

#### 5.1.9 Reviews Module
**Priority: P0 (Critical)**

**Features:**
- Review click tracking
- Review funnel visualization
- Recent review clicks list
- Google Business integration (limited)

---

#### 5.1.10 Social Links Module
**Priority: P1 (High)**

**Features:**
- Social link configuration per landing page
- Click tracking by platform
- Platform distribution analytics
- Top performing links

---

#### 5.1.11 Reports Module
**Priority: P1 (High)**

**Features:**
- Generate PDF/CSV/Excel reports
- Schedule automated reports
- Quick data export
- Report history and download

---

### 5.2 Promoter Dashboard Module

**Priority: P0 (Critical)**

**Features:**
- Personal performance overview
- Assigned cards view
- Event tracking
- Prize redemption validation
- Profile management

---

### 5.3 Customer-Facing Landing Page Module

**Priority: P0 (Critical)**

**Features:**
- Mobile-responsive landing pages
- Game interaction (dice roll, spin wheel)
- Sign-up form submission
- Google review redirect
- Prize display and redemption instructions

---

## 6. Mongoose Schema Definitions

### 6.1 User Schema

```typescript
import { Schema, model, Types, Document } from 'mongoose';

export interface IUser extends Document {
  business?: Types.ObjectId;
  email: string;
  passwordHash: string;
  name: string;
  role: 'super_admin' | 'business_admin' | 'campaign_manager' | 'promoter';
  status: 'active' | 'inactive' | 'suspended';
  emailVerified: boolean;
  lastLoginAt?: Date;
  createdAt: Date;
  updatedAt: Date;
}

const userSchema = new Schema<IUser>(
  {
    business: {
      type: Schema.Types.ObjectId,
      ref: 'Business',
      required: false,
      index: true,
    },
    email: {
      type: String,
      required: true,
      unique: true,
      lowercase: true,
      trim: true,
      index: true,
    },
    passwordHash: {
      type: String,
      required: true,
    },
    name: {
      type: String,
      required: true,
      trim: true,
    },
    role: {
      type: String,
      enum: ['super_admin', 'business_admin', 'campaign_manager', 'promoter'],
      required: true,
      index: true,
    },
    status: {
      type: String,
      enum: ['active', 'inactive', 'suspended'],
      default: 'active',
      index: true,
    },
    emailVerified: {
      type: Boolean,
      default: false,
    },
    lastLoginAt: {
      type: Date,
    },
  },
  {
    timestamps: true,
    toJSON: { virtuals: true },
    toObject: { virtuals: true },
  }
);

// Virtual for populating sessions
userSchema.virtual('sessions', {
  ref: 'Session',
  localField: '_id',
  foreignField: 'user',
});

// Index for login queries
userSchema.index({ email: 1, status: 1 });

export const User = model<IUser>('User', userSchema);
```

---

### 6.2 Business Schema

```typescript
export interface IBusiness extends Document {
  name: string;
  slug: string;
  businessType?: string;
  address?: string;
  city?: string;
  country?: string;
  phone?: string;
  email?: string;
  logoUrl?: string;
  googleBusinessUrl?: string;
  subscriptionPlan: 'free' | 'starter' | 'professional' | 'enterprise';
  subscriptionStatus: 'active' | 'cancelled' | 'expired';
  subscriptionExpiresAt?: Date;
  settings: Record<string, any>;
  promoters: Types.ObjectId[];
  users: Types.ObjectId[];
  landingPages: Types.ObjectId[];
  campaigns: Types.ObjectId[];
  cards: Types.ObjectId[];
  createdAt: Date;
  updatedAt: Date;
}

const businessSchema = new Schema<IBusiness>(
  {
    name: {
      type: String,
      required: true,
      trim: true,
    },
    slug: {
      type: String,
      required: true,
      unique: true,
      lowercase: true,
      trim: true,
      index: true,
    },
    businessType: {
      type: String,
      trim: true,
    },
    address: {
      type: String,
      trim: true,
    },
    city: {
      type: String,
      trim: true,
      index: true,
    },
    country: {
      type: String,
      trim: true,
      index: true,
    },
    phone: {
      type: String,
      trim: true,
    },
    email: {
      type: String,
      lowercase: true,
      trim: true,
    },
    logoUrl: {
      type: String,
    },
    googleBusinessUrl: {
      type: String,
    },
    subscriptionPlan: {
      type: String,
      enum: ['free', 'starter', 'professional', 'enterprise'],
      default: 'free',
      index: true,
    },
    subscriptionStatus: {
      type: String,
      enum: ['active', 'cancelled', 'expired'],
      default: 'active',
      index: true,
    },
    subscriptionExpiresAt: {
      type: Date,
      index: true,
    },
    settings: {
      type: Schema.Types.Mixed,
      default: {},
    },
    promoters: [{
      type: Schema.Types.ObjectId,
      ref: 'Promoter',
    }],
    users: [{
      type: Schema.Types.ObjectId,
      ref: 'User',
    }],
    landingPages: [{
      type: Schema.Types.ObjectId,
      ref: 'LandingPage',
    }],
    campaigns: [{
      type: Schema.Types.ObjectId,
      ref: 'Campaign',
    }],
    cards: [{
      type: Schema.Types.ObjectId,
      ref: 'Card',
    }],
  },
  {
    timestamps: true,
  }
);

// Compound index for slug lookups
businessSchema.index({ slug: 1 });

export const Business = model<IBusiness>('Business', businessSchema);
```

---

### 6.3 LandingPage Schema

```typescript
export interface ILandingPage extends Document {
  business: Types.ObjectId;
  slug: string;
  title: string;
  status: 'draft' | 'published' | 'archived';
  blocks: {
    hero?: {
      enabled: boolean;
      headline?: string;
      subheadline?: string;
      heroImage?: string;
    };
    eventDetails?: {
      enabled: boolean;
      venueName?: string;
      date?: string;
      time?: string;
      location?: string;
    };
    description?: {
      enabled: boolean;
      text?: string;
    };
    prizeGame?: {
      enabled: boolean;
      gameType: 'dice_roll' | 'spin_wheel';
      prizes: string[];
      reviewUrl?: string;
      reviewPrompt?: string;
    };
    socialLinks?: {
      enabled: boolean;
      instagram?: string;
      tiktok?: string;
      whatsapp?: string;
      website?: string;
    };
    contactInfo?: {
      enabled: boolean;
      phone?: string;
      email?: string;
    };
  };
  promoters: Types.ObjectId[];
  settings: Record<string, any>;
  publishedAt?: Date;
  createdAt: Date;
  updatedAt: Date;
}

const landingPageSchema = new Schema<ILandingPage>(
  {
    business: {
      type: Schema.Types.ObjectId,
      ref: 'Business',
      required: true,
      index: true,
    },
    slug: {
      type: String,
      required: true,
      trim: true,
      index: true,
    },
    title: {
      type: String,
      required: true,
      trim: true,
    },
    status: {
      type: String,
      enum: ['draft', 'published', 'archived'],
      default: 'draft',
      index: true,
    },
    blocks: {
      type: Schema.Types.Mixed,
      default: {},
    },
    promoters: [{
      type: Schema.Types.ObjectId,
      ref: 'Promoter',
    }],
    settings: {
      type: Schema.Types.Mixed,
      default: {},
    },
    publishedAt: {
      type: Date,
      index: true,
    },
  },
  {
    timestamps: true,
  }
);

// Unique compound index for business + slug
landingPageSchema.index({ business: 1, slug: 1 }, { unique: true });

// Index for status filtering
landingPageSchema.index({ status: 1, publishedAt: -1 });

export const LandingPage = model<ILandingPage>('LandingPage', landingPageSchema);
```

---

### 6.4 Promoter Schema

```typescript
export interface IPromoter extends Document {
  business: Types.ObjectId;
  user?: Types.ObjectId;
  name: string;
  nickname?: string;
  email?: string;
  phone?: string;
  avatarUrl?: string;
  status: 'active' | 'inactive';
  totalInteractions: number;
  totalSignups: number;
  totalReviews: number;
  metadata: Record<string, any>;
  createdAt: Date;
  updatedAt: Date;
}

const promoterSchema = new Schema<IPromoter>(
  {
    business: {
      type: Schema.Types.ObjectId,
      ref: 'Business',
      required: true,
      index: true,
    },
    user: {
      type: Schema.Types.ObjectId,
      ref: 'User',
      required: false,
      index: true,
    },
    name: {
      type: String,
      required: true,
      trim: true,
      index: true,
    },
    nickname: {
      type: String,
      trim: true,
      index: true,
    },
    email: {
      type: String,
      lowercase: true,
      trim: true,
    },
    phone: {
      type: String,
      trim: true,
    },
    avatarUrl: {
      type: String,
    },
    status: {
      type: String,
      enum: ['active', 'inactive'],
      default: 'active',
      index: true,
    },
    totalInteractions: {
      type: Number,
      default: 0,
      index: true,
    },
    totalSignups: {
      type: Number,
      default: 0,
      index: true,
    },
    totalReviews: {
      type: Number,
      default: 0,
      index: true,
    },
    metadata: {
      type: Schema.Types.Mixed,
      default: {},
    },
  },
  {
    timestamps: true,
  }
);

// Compound index for business queries with ranking
promoterSchema.index({ business: 1, totalInteractions: -1 });

// Text index for search
promoterSchema.index({ name: 'text', nickname: 'text' });

export const Promoter = model<IPromoter>('Promoter', promoterSchema);
```

---

### 6.5 Campaign & Event Schemas

```typescript
export interface ICampaign extends Document {
  business: Types.ObjectId;
  landingPage?: Types.ObjectId;
  name: string;
  venueName?: string;
  description?: string;
  startDate: Date;
  endDate: Date;
  status: 'scheduled' | 'active' | 'completed' | 'archived';
  totalInteractions: number;
  totalSignups: number;
  totalPrizes: number;
  settings: Record<string, any>;
  events: Types.ObjectId[];
  createdAt: Date;
  updatedAt: Date;
}

const campaignSchema = new Schema<ICampaign>(
  {
    business: {
      type: Schema.Types.ObjectId,
      ref: 'Business',
      required: true,
      index: true,
    },
    landingPage: {
      type: Schema.Types.ObjectId,
      ref: 'LandingPage',
      required: false,
      index: true,
    },
    name: {
      type: String,
      required: true,
      trim: true,
      index: true,
    },
    venueName: {
      type: String,
      trim: true,
    },
    description: {
      type: String,
      trim: true,
    },
    startDate: {
      type: Date,
      required: true,
      index: true,
    },
    endDate: {
      type: Date,
      required: true,
      index: true,
    },
    status: {
      type: String,
      enum: ['scheduled', 'active', 'completed', 'archived'],
      default: 'scheduled',
      index: true,
    },
    totalInteractions: {
      type: Number,
      default: 0,
    },
    totalSignups: {
      type: Number,
      default: 0,
    },
    totalPrizes: {
      type: Number,
      default: 0,
    },
    settings: {
      type: Schema.Types.Mixed,
      default: {},
    },
    events: [{
      type: Schema.Types.ObjectId,
      ref: 'Event',
    }],
  },
  {
    timestamps: true,
  }
);

// Index for date-based queries
campaignSchema.index({ status: 1, startDate: 1, endDate: 1 });

export const Campaign = model<ICampaign>('Campaign', campaignSchema);

// Event Schema
export interface IEvent extends Document {
  campaign: Types.ObjectId;
  name: string;
  venueName?: string;
  eventDate: Date;
  totalSignups: number;
  settings: Record<string, any>;
  createdAt: Date;
}

const eventSchema = new Schema<IEvent>(
  {
    campaign: {
      type: Schema.Types.ObjectId,
      ref: 'Campaign',
      required: true,
      index: true,
    },
    name: {
      type: String,
      required: true,
      trim: true,
    },
    venueName: {
      type: String,
      trim: true,
    },
    eventDate: {
      type: Date,
      required: true,
      index: true,
    },
    totalSignups: {
      type: Number,
      default: 0,
    },
    settings: {
      type: Schema.Types.Mixed,
      default: {},
    },
  },
  {
    timestamps: true,
  }
);

// Index for upcoming events
eventSchema.index({ eventDate: 1, campaign: 1 });

export const Event = model<IEvent>('Event', eventSchema);
```

---

### 6.6 Participant Schema

```typescript
export interface IParticipant extends Document {
  landingPage: Types.ObjectId;
  campaign?: Types.ObjectId;
  promoter?: Types.ObjectId;
  name: string;
  email: string;
  phone?: string;
  status: 'confirmed' | 'pending' | 'bounced';
  prizeWon?: string;
  source?: string;
  consentGiven: boolean;
  metadata: Record<string, any>;
  createdAt: Date;
  updatedAt: Date;
}

const participantSchema = new Schema<IParticipant>(
  {
    landingPage: {
      type: Schema.Types.ObjectId,
      ref: 'LandingPage',
      required: true,
      index: true,
    },
    campaign: {
      type: Schema.Types.ObjectId,
      ref: 'Campaign',
      required: false,
      index: true,
    },
    promoter: {
      type: Schema.Types.ObjectId,
      ref: 'Promoter',
      required: false,
      index: true,
    },
    name: {
      type: String,
      required: true,
      trim: true,
    },
    email: {
      type: String,
      required: true,
      lowercase: true,
      trim: true,
      index: true,
    },
    phone: {
      type: String,
      trim: true,
    },
    status: {
      type: String,
      enum: ['confirmed', 'pending', 'bounced'],
      default: 'pending',
      index: true,
    },
    prizeWon: {
      type: String,
      trim: true,
    },
    source: {
      type: String,
      trim: true,
    },
    consentGiven: {
      type: Boolean,
      default: false,
    },
    metadata: {
      type: Schema.Types.Mixed,
      default: {},
    },
  },
  {
    timestamps: true,
  }
);

// Compound indexes for common queries
participantSchema.index({ landingPage: 1, createdAt: -1 });
participantSchema.index({ campaign: 1, createdAt: -1 });
participantSchema.index({ promoter: 1, createdAt: -1 });
participantSchema.index({ email: 1 });

// Index for export queries
participantSchema.index({ createdAt: -1, status: 1 });

export const Participant = model<IParticipant>('Participant', participantSchema);
```

---

### 6.7 Card & CardInteraction Schemas

```typescript
export interface ICard extends Document {
  business: Types.ObjectId;
  promoter?: Types.ObjectId;
  landingPage: Types.ObjectId;
  cardType: 'nfc' | 'qr_code';
  nfcUuid?: string;
  qrCodeUrl?: string;
  name: string;
  status: 'active' | 'inactive' | 'lost' | 'replaced';
  totalInteractions: number;
  lastTapAt?: Date;
  metadata: Record<string, any>;
  createdAt: Date;
  updatedAt: Date;
}

const cardSchema = new Schema<ICard>(
  {
    business: {
      type: Schema.Types.ObjectId,
      ref: 'Business',
      required: true,
      index: true,
    },
    promoter: {
      type: Schema.Types.ObjectId,
      ref: 'Promoter',
      required: false,
      index: true,
    },
    landingPage: {
      type: Schema.Types.ObjectId,
      ref: 'LandingPage',
      required: true,
      index: true,
    },
    cardType: {
      type: String,
      enum: ['nfc', 'qr_code'],
      required: true,
      index: true,
    },
    nfcUuid: {
      type: String,
      unique: true,
      sparse: true,
      index: true,
    },
    qrCodeUrl: {
      type: String,
    },
    name: {
      type: String,
      required: true,
      trim: true,
    },
    status: {
      type: String,
      enum: ['active', 'inactive', 'lost', 'replaced'],
      default: 'active',
      index: true,
    },
    totalInteractions: {
      type: Number,
      default: 0,
      index: true,
    },
    lastTapAt: {
      type: Date,
      index: true,
    },
    metadata: {
      type: Schema.Types.Mixed,
      default: {},
    },
  },
  {
    timestamps: true,
  }
);

// Index for NFC lookup (critical for performance)
cardSchema.index({ nfcUuid: 1 }, { unique: true, sparse: true });

// Compound index for promoter cards
cardSchema.index({ promoter: 1, status: 1 });

export const Card = model<ICard>('Card', cardSchema);

// CardInteraction Schema
export interface ICardInteraction extends Document {
  card: Types.ObjectId;
  participant?: Types.ObjectId;
  ipAddress?: string;
  deviceInfo?: string;
  userAgent?: string;
  createdAt: Date;
}

const cardInteractionSchema = new Schema<ICardInteraction>(
  {
    card: {
      type: Schema.Types.ObjectId,
      ref: 'Card',
      required: true,
      index: true,
    },
    participant: {
      type: Schema.Types.ObjectId,
      ref: 'Participant',
      required: false,
      index: true,
    },
    ipAddress: {
      type: String,
      index: true,
    },
    deviceInfo: {
      type: String,
    },
    userAgent: {
      type: String,
    },
  },
  {
    timestamps: { createdAt: true, updatedAt: false },
  }
);

// Index for time-series analytics
cardInteractionSchema.index({ card: 1, createdAt: -1 });
cardInteractionSchema.index({ createdAt: -1 });

// TTL index for raw interaction data (optional, if storing indefinitely is costly)
// cardInteractionSchema.index({ createdAt: 1 }, { expireAfterSeconds: 90 * 24 * 60 * 60 }); // 90 days

export const CardInteraction = model<ICardInteraction>('CardInteraction', cardInteractionSchema);
```

---

### 6.8 GamePlay & Prize Schemas

```typescript
export interface IGamePlay extends Document {
  landingPage: Types.ObjectId;
  participant?: Types.ObjectId;
  gameType: 'dice_roll' | 'spin_wheel';
  result: string;
  redeemed: boolean;
  redeemedAt?: Date;
  ipAddress?: string;
  deviceFingerprint?: string;
  sessionId?: string;
  createdAt: Date;
}

const gamePlaySchema = new Schema<IGamePlay>(
  {
    landingPage: {
      type: Schema.Types.ObjectId,
      ref: 'LandingPage',
      required: true,
      index: true,
    },
    participant: {
      type: Schema.Types.ObjectId,
      ref: 'Participant',
      required: false,
      index: true,
    },
    gameType: {
      type: String,
      enum: ['dice_roll', 'spin_wheel'],
      required: true,
      index: true,
    },
    result: {
      type: String,
      required: true,
      trim: true,
    },
    redeemed: {
      type: Boolean,
      default: false,
      index: true,
    },
    redeemedAt: {
      type: Date,
      index: true,
    },
    ipAddress: {
      type: String,
      index: true,
    },
    deviceFingerprint: {
      type: String,
      index: true,
    },
    sessionId: {
      type: String,
      index: true,
    },
  },
  {
    timestamps: { createdAt: true, updatedAt: false },
  }
);

// Compound indexes for fraud detection
gamePlaySchema.index({ deviceFingerprint: 1, createdAt: -1 });
gamePlaySchema.index({ ipAddress: 1, createdAt: -1 });

// Index for game analytics
gamePlaySchema.index({ landingPage: 1, gameType: 1, createdAt: -1 });

export const GamePlay = model<IGamePlay>('GamePlay', gamePlaySchema);

// Prize Schema
export interface IPrize extends Document {
  landingPage: Types.ObjectId;
  name: string;
  probability: number;
  maxRedemptions?: number;
  currentRedemptions: number;
  active: boolean;
  displayOrder: number;
  createdAt: Date;
}

const prizeSchema = new Schema<IPrize>(
  {
    landingPage: {
      type: Schema.Types.ObjectId,
      ref: 'LandingPage',
      required: true,
      index: true,
    },
    name: {
      type: String,
      required: true,
      trim: true,
    },
    probability: {
      type: Number,
      required: true,
      min: 0,
      max: 1,
    },
    maxRedemptions: {
      type: Number,
      required: false,
    },
    currentRedemptions: {
      type: Number,
      default: 0,
    },
    active: {
      type: Boolean,
      default: true,
      index: true,
    },
    displayOrder: {
      type: Number,
      default: 0,
      index: true,
    },
  },
  {
    timestamps: { createdAt: true, updatedAt: false },
  }
);

// Index for prize queries
prizeSchema.index({ landingPage: 1, active: 1, displayOrder: 1 });

export const Prize = model<IPrize>('Prize', prizeSchema);
```

---

### 6.9 Review & Social Link Schemas

```typescript
// ReviewClick Schema
export interface IReviewClick extends Document {
  landingPage: Types.ObjectId;
  participant?: Types.ObjectId;
  promoter?: Types.ObjectId;
  googleReviewUrl?: string;
  clicked: boolean;
  createdAt: Date;
}

const reviewClickSchema = new Schema<IReviewClick>(
  {
    landingPage: {
      type: Schema.Types.ObjectId,
      ref: 'LandingPage',
      required: true,
      index: true,
    },
    participant: {
      type: Schema.Types.ObjectId,
      ref: 'Participant',
      required: false,
      index: true,
    },
    promoter: {
      type: Schema.Types.ObjectId,
      ref: 'Promoter',
      required: false,
      index: true,
    },
    googleReviewUrl: {
      type: String,
    },
    clicked: {
      type: Boolean,
      default: true,
      index: true,
    },
  },
  {
    timestamps: { createdAt: true, updatedAt: false },
  }
);

// Index for review analytics
reviewClickSchema.index({ landingPage: 1, createdAt: -1 });
reviewClickSchema.index({ promoter: 1, createdAt: -1 });

export const ReviewClick = model<IReviewClick>('ReviewClick', reviewClickSchema);

// SocialLink Schema
export interface ISocialLink extends Document {
  landingPage: Types.ObjectId;
  platform: 'instagram' | 'tiktok' | 'whatsapp' | 'facebook' | 'website' | 'custom';
  label: string;
  url: string;
  displayOrder: number;
  active: boolean;
  createdAt: Date;
}

const socialLinkSchema = new Schema<ISocialLink>(
  {
    landingPage: {
      type: Schema.Types.ObjectId,
      ref: 'LandingPage',
      required: true,
      index: true,
    },
    platform: {
      type: String,
      enum: ['instagram', 'tiktok', 'whatsapp', 'facebook', 'website', 'custom'],
      required: true,
      index: true,
    },
    label: {
      type: String,
      required: true,
      trim: true,
    },
    url: {
      type: String,
      required: true,
      trim: true,
    },
    displayOrder: {
      type: Number,
      default: 0,
      index: true,
    },
    active: {
      type: Boolean,
      default: true,
      index: true,
    },
  },
  {
    timestamps: { createdAt: true, updatedAt: false },
  }
);

// Index for social link queries
socialLinkSchema.index({ landingPage: 1, displayOrder: 1 });

export const SocialLink = model<ISocialLink>('SocialLink', socialLinkSchema);

// SocialLinkClick Schema
export interface ISocialLinkClick extends Document {
  socialLink: Types.ObjectId;
  participant?: Types.ObjectId;
  ipAddress?: string;
  userAgent?: string;
  createdAt: Date;
}

const socialLinkClickSchema = new Schema<ISocialLinkClick>(
  {
    socialLink: {
      type: Schema.Types.ObjectId,
      ref: 'SocialLink',
      required: true,
      index: true,
    },
    participant: {
      type: Schema.Types.ObjectId,
      ref: 'Participant',
      required: false,
      index: true,
    },
    ipAddress: {
      type: String,
      index: true,
    },
    userAgent: {
      type: String,
    },
  },
  {
    timestamps: { createdAt: true, updatedAt: false },
  }
);

// Index for social analytics
socialLinkClickSchema.index({ socialLink: 1, createdAt: -1 });
socialLinkClickSchema.index({ createdAt: -1 });

export const SocialLinkClick = model<ISocialLinkClick>('SocialLinkClick', socialLinkClickSchema);

// PromoterSelection Schema
export interface IPromoterSelection extends Document {
  landingPage: Types.ObjectId;
  promoter: Types.ObjectId;
  participant?: Types.ObjectId;
  createdAt: Date;
}

const promoterSelectionSchema = new Schema<IPromoterSelection>(
  {
    landingPage: {
      type: Schema.Types.ObjectId,
      ref: 'LandingPage',
      required: true,
      index: true,
    },
    promoter: {
      type: Schema.Types.ObjectId,
      ref: 'Promoter',
      required: true,
      index: true,
    },
    participant: {
      type: Schema.Types.ObjectId,
      ref: 'Participant',
      required: false,
      index: true,
    },
  },
  {
    timestamps: { createdAt: true, updatedAt: false },
  }
);

// Index for promoter selection tracking
promoterSelectionSchema.index({ promoter: 1, createdAt: -1 });
promoterSelectionSchema.index({ landingPage: 1, promoter: 1 });

export const PromoterSelection = model<IPromoterSelection>('PromoterSelection', promoterSelectionSchema);
```

---

### 6.10 Report & Analytics Schemas

```typescript
// Report Schema
export interface IReport extends Document {
  business: Types.ObjectId;
  reportType: 'monthly_performance' | 'audience_data' | 'engagement' | 'game_performance';
  format: 'pdf' | 'csv' | 'excel' | 'json';
  status: 'pending' | 'completed' | 'failed';
  fileUrl?: string;
  fileSize?: number;
  parameters: Record<string, any>;
  errorMessage?: string;
  completedAt?: Date;
  createdAt: Date;
}

const reportSchema = new Schema<IReport>(
  {
    business: {
      type: Schema.Types.ObjectId,
      ref: 'Business',
      required: true,
      index: true,
    },
    reportType: {
      type: String,
      enum: ['monthly_performance', 'audience_data', 'engagement', 'game_performance'],
      required: true,
      index: true,
    },
    format: {
      type: String,
      enum: ['pdf', 'csv', 'excel', 'json'],
      required: true,
    },
    status: {
      type: String,
      enum: ['pending', 'completed', 'failed'],
      default: 'pending',
      index: true,
    },
    fileUrl: {
      type: String,
    },
    fileSize: {
      type: Number,
    },
    parameters: {
      type: Schema.Types.Mixed,
      default: {},
    },
    errorMessage: {
      type: String,
    },
    completedAt: {
      type: Date,
      index: true,
    },
  },
  {
    timestamps: { createdAt: true, updatedAt: false },
  }
);

// Index for report queries
reportSchema.index({ business: 1, status: 1, createdAt: -1 });

export const Report = model<IReport>('Report', reportSchema);

// AnalyticsDaily Schema (Pre-aggregated daily metrics)
export interface IAnalyticsDaily extends Document {
  business: Types.ObjectId;
  date: Date;
  totalInteractions: number;
  uniqueVisitors: number;
  returnVisitors: number;
  totalSignups: number;
  totalGamePlays: number;
  totalReviewClicks: number;
  totalSocialClicks: number;
  createdAt: Date;
}

const analyticsDailySchema = new Schema<IAnalyticsDaily>(
  {
    business: {
      type: Schema.Types.ObjectId,
      ref: 'Business',
      required: true,
      index: true,
    },
    date: {
      type: Date,
      required: true,
      index: true,
    },
    totalInteractions: {
      type: Number,
      default: 0,
    },
    uniqueVisitors: {
      type: Number,
      default: 0,
    },
    returnVisitors: {
      type: Number,
      default: 0,
    },
    totalSignups: {
      type: Number,
      default: 0,
    },
    totalGamePlays: {
      type: Number,
      default: 0,
    },
    totalReviewClicks: {
      type: Number,
      default: 0,
    },
    totalSocialClicks: {
      type: Number,
      default: 0,
    },
  },
  {
    timestamps: { createdAt: true, updatedAt: false },
  }
);

// Unique compound index for upsert operations
analyticsDailySchema.index({ business: 1, date: 1 }, { unique: true });

// Index for trend queries
analyticsDailySchema.index({ date: -1 });

export const AnalyticsDaily = model<IAnalyticsDaily>('AnalyticsDaily', analyticsDailySchema);

// Session Schema
export interface ISession extends Document {
  user: Types.ObjectId;
  tokenHash: string;
  expiresAt: Date;
  ipAddress?: string;
  userAgent?: string;
  createdAt: Date;
}

const sessionSchema = new Schema<ISession>(
  {
    user: {
      type: Schema.Types.ObjectId,
      ref: 'User',
      required: true,
      index: true,
    },
    tokenHash: {
      type: String,
      required: true,
      index: true,
    },
    expiresAt: {
      type: Date,
      required: true,
      index: true,
    },
    ipAddress: {
      type: String,
    },
    userAgent: {
      type: String,
    },
  },
  {
    timestamps: { createdAt: true, updatedAt: false },
  }
);

// TTL index for automatic session cleanup
sessionSchema.index({ expiresAt: 1 }, { expireAfterSeconds: 0 });

export const Session = model<ISession>('Session', sessionSchema);
```

---

## 7. API Specifications

### 7.1 Authentication Endpoints

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

### 7.2 Business Management Endpoints

```
GET    /api/v1/businesses              - List businesses (super admin)
POST   /api/v1/businesses              - Create business (super admin)
GET    /api/v1/businesses/:id          - Get business details
PUT    /api/v1/businesses/:id          - Update business
DELETE /api/v1/businesses/:id          - Delete business (soft delete)
GET    /api/v1/businesses/:id/settings - Get business settings
PUT    /api/v1/businesses/:id/settings - Update business settings
```

### 7.3 Landing Page Endpoints

```
GET    /api/v1/landing-pages                  - List landing pages
POST   /api/v1/landing-pages                  - Create landing page
GET    /api/v1/landing-pages/:id              - Get landing page details
PUT    /api/v1/landing-pages/:id              - Update landing page
DELETE /api/v1/landing-pages/:id              - Delete landing page
POST   /api/v1/landing-pages/:id/duplicate    - Duplicate landing page
POST   /api/v1/landing-pages/:id/publish      - Publish landing page
POST   /api/v1/landing-pages/:id/unpublish    - Unpublish landing page
```

### 7.4 Public Landing Page Endpoints

```
GET    /api/v1/public/pages/:slug             - Get landing page content
POST   /api/v1/public/pages/:slug/submit      - Submit sign-up form
POST   /api/v1/public/pages/:slug/game        - Play game (dice/spin)
POST   /api/v1/public/pages/:slug/review      - Track review click
POST   /api/v1/public/pages/:slug/social/:id  - Track social link click
POST   /api/v1/public/pages/:slug/select-promoter - Select promoter
```

### 7.5 Promoter Endpoints

```
GET    /api/v1/promoters                      - List promoters
POST   /api/v1/promoters                      - Create promoter
GET    /api/v1/promoters/:id                  - Get promoter details
PUT    /api/v1/promoters/:id                  - Update promoter
DELETE /api/v1/promoters/:id                  - Delete promoter
GET    /api/v1/promoters/:id/stats            - Get promoter statistics
GET    /api/v1/promoters/:id/cards            - Get assigned cards
```

### 7.6 Campaign Endpoints

```
GET    /api/v1/campaigns                      - List campaigns
POST   /api/v1/campaigns                      - Create campaign
GET    /api/v1/campaigns/:id                  - Get campaign details
PUT    /api/v1/campaigns/:id                  - Update campaign
DELETE /api/v1/campaigns/:id                  - Delete campaign
GET    /api/v1/campaigns/:id/events           - List campaign events
POST   /api/v1/campaigns/:id/events           - Create event
GET    /api/v1/campaigns/:id/stats            - Get campaign statistics
```

### 7.7 Card Endpoints

```
GET    /api/v1/cards                          - List cards
POST   /api/v1/cards                          - Create/order new card
GET    /api/v1/cards/:id                      - Get card details
PUT    /api/v1/cards/:id                      - Update card
DELETE /api/v1/cards/:id                      - Deactivate card
POST   /api/v1/cards/:id/assign               - Assign card to promoter
GET    /api/v1/cards/:id/interactions         - Get card interactions
POST   /api/v1/cards/resolve/:nfcUuid         - Resolve NFC UUID to page
```

### 7.8 Analytics Endpoints

```
GET    /api/v1/analytics/overview             - Get overview metrics
GET    /api/v1/analytics/interactions         - Get interaction analytics
GET    /api/v1/analytics/peak-hours           - Get peak activity hours
GET    /api/v1/analytics/geographic           - Get geographic distribution
GET    /api/v1/analytics/cards                - Get card performance
GET    /api/v1/analytics/promoters            - Get promoter leaderboard
GET    /api/v1/analytics/trends               - Get trend data
```

### 7.9 Report Endpoints

```
GET    /api/v1/reports                        - List reports
POST   /api/v1/reports/generate               - Generate new report
GET    /api/v1/reports/:id                    - Get report status
GET    /api/v1/reports/:id/download           - Download report
POST   /api/v1/reports/export/quick           - Quick data export
```

---

## 8. Indexing Strategy

### 8.1 Critical Indexes

| Collection | Index | Type | Purpose |
|------------|-------|------|---------|
| Card | `{ nfcUuid: 1 }` | Unique, Sparse | NFC card resolution (most critical query) |
| LandingPage | `{ business: 1, slug: 1 }` | Unique Compound | Public page lookup |
| Participant | `{ landingPage: 1, createdAt: -1 }` | Compound | Participant list per page |
| Participant | `{ email: 1 }` | Single | Duplicate email check |
| GamePlay | `{ deviceFingerprint: 1, createdAt: -1 }` | Compound | Fraud detection |
| CardInteraction | `{ card: 1, createdAt: -1 }` | Compound | Card analytics |
| Promoter | `{ business: 1, totalInteractions: -1 }` | Compound | Leaderboard queries |
| AnalyticsDaily | `{ business: 1, date: 1 }` | Unique Compound | Daily metrics upsert |

### 8.2 Index Creation Order

```typescript
// Run during application startup or migration
async function createIndexes() {
  // Card indexes (highest priority - NFC resolution)
  await Card.createIndexes();
  
  // LandingPage indexes (public-facing performance)
  await LandingPage.createIndexes();
  
  // Participant indexes (high query volume)
  await Participant.createIndexes();
  
  // GamePlay indexes (fraud detection + analytics)
  await GamePlay.createIndexes();
  
  // Analytics indexes
  await AnalyticsDaily.createIndexes();
  
  // All other indexes
  await Promise.all([
    Business.createIndexes(),
    Promoter.createIndexes(),
    Campaign.createIndexes(),
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

### 8.3 Index Monitoring

```typescript
// Monitor index usage
const indexStats = await CardInteraction.aggregate([
  { $indexStats: {} }
]);

// Identify unused indexes
const unusedIndexes = indexStats.filter(stat => stat.accesses.ops === 0);
```

---

## 9. Aggregation Pipelines

### 9.1 Promoter Leaderboard

```typescript
async function getPromoterLeaderboard(businessId: Types.ObjectId, days: number = 7) {
  const startDate = new Date(Date.now() - days * 24 * 60 * 60 * 1000);

  const leaderboard = await Promoter.aggregate([
    // Match promoters for this business
    { $match: { business: businessId, status: 'active' } },
    
    // Lookup interactions
    {
      $lookup: {
        from: 'promoterselections',
        let: { promoterId: '$_id' },
        pipeline: [
          { $match: { $expr: { $eq: ['$promoter', '$$promoterId'] } } },
          { $match: { createdAt: { $gte: startDate } } },
          { $count: 'count' }
        ],
        as: 'interactions'
      }
    },
    
    // Lookup signups
    {
      $lookup: {
        from: 'participants',
        let: { promoterId: '$_id' },
        pipeline: [
          { $match: { $expr: { $eq: ['$promoter', '$$promoterId'] } } },
          { $match: { createdAt: { $gte: startDate } } },
          { $count: 'count' }
        ],
        as: 'signups'
      }
    },
    
    // Lookup reviews
    {
      $lookup: {
        from: 'reviewclicks',
        let: { promoterId: '$_id' },
        pipeline: [
          { $match: { $expr: { $eq: ['$promoter', '$$promoterId'] } } },
          { $match: { createdAt: { $gte: startDate } } },
          { $count: 'count' }
        ],
        as: 'reviews'
      }
    },
    
    // Calculate totals
    {
      $addFields: {
        totalInteractions: { $ifNull: [{ $first: '$interactions.count' }, 0] },
        totalSignups: { $ifNull: [{ $first: '$signups.count' }, 0] },
        totalReviews: { $ifNull: [{ $first: '$reviews.count' }, 0] }
      }
    },
    
    // Sort by interactions
    { $sort: { totalInteractions: -1 } },
    
    // Add rank
    {
      $setWindowFields: {
        sortBy: { totalInteractions: -1 },
        output: { rank: { $documentNumber: {} } }
      }
    },
    
    // Project final output
    {
      $project: {
        _id: 1,
        name: 1,
        nickname: 1,
        avatarUrl: 1,
        rank: 1,
        totalInteractions: 1,
        totalSignups: 1,
        totalReviews: 1
      }
    }
  ]);

  return leaderboard;
}
```

### 9.2 Dashboard Overview Metrics

```typescript
async function getDashboardOverview(businessId: Types.ObjectId, days: number = 7) {
  const startDate = new Date(Date.now() - days * 24 * 60 * 60 * 1000);
  const previousStartDate = new Date(startDate.getTime() - days * 24 * 60 * 60 * 1000);

  // Get landing pages for business
  const landingPages = await LandingPage.find({ business: businessId }).distinct('_id');

  // Get cards for business
  const cards = await Card.find({ business: businessId }).distinct('_id');

  // Parallel aggregation for all metrics
  const [
    interactions,
    previousInteractions,
    participants,
    previousParticipants,
    gamePlays,
    previousGamePlays,
    signups,
    previousSignups,
    reviews,
    previousReviews
  ] = await Promise.all([
    CardInteraction.countDocuments({ card: { $in: cards }, createdAt: { $gte: startDate } }),
    CardInteraction.countDocuments({ card: { $in: cards }, createdAt: { $gte: previousStartDate, $lt: startDate } }),
    Participant.countDocuments({ landingPage: { $in: landingPages }, createdAt: { $gte: startDate } }),
    Participant.countDocuments({ landingPage: { $in: landingPages }, createdAt: { $gte: previousStartDate, $lt: startDate } }),
    GamePlay.countDocuments({ landingPage: { $in: landingPages }, createdAt: { $gte: startDate } }),
    GamePlay.countDocuments({ landingPage: { $in: landingPages }, createdAt: { $gte: previousStartDate, $lt: startDate } }),
    Participant.countDocuments({ landingPage: { $in: landingPages }, createdAt: { $gte: startDate } }),
    Participant.countDocuments({ landingPage: { $in: landingPages }, createdAt: { $gte: previousStartDate, $lt: startDate } }),
    ReviewClick.countDocuments({ landingPage: { $in: landingPages }, createdAt: { $gte: startDate } }),
    ReviewClick.countDocuments({ landingPage: { $in: landingPages }, createdAt: { $gte: previousStartDate, $lt: startDate } })
  ]);

  // Calculate trends
  const calculateTrend = (current: number, previous: number) => {
    if (previous === 0) return current > 0 ? 100 : 0;
    return Math.round(((current - previous) / previous) * 100);
  };

  return {
    totalInteractions: interactions,
    interactionsTrend: calculateTrend(interactions, previousInteractions),
    totalParticipants: participants,
    participantsTrend: calculateTrend(participants, previousParticipants),
    totalGamePlays: gamePlays,
    gamePlaysTrend: calculateTrend(gamePlays, previousGamePlays),
    totalSignups: signups,
    signupsTrend: calculateTrend(signups, previousSignups),
    totalReviews: reviews,
    reviewsTrend: calculateTrend(reviews, previousReviews)
  };
}
```

### 9.3 Peak Activity Hours

```typescript
async function getPeakActivityHours(businessId: Types.ObjectId, days: number = 7) {
  const startDate = new Date(Date.now() - days * 24 * 60 * 60 * 1000);
  const cards = await Card.find({ business: businessId }).distinct('_id');

  const peakHours = await CardInteraction.aggregate([
    // Match interactions for business cards
    { $match: { card: { $in: cards }, createdAt: { $gte: startDate } } },
    
    // Extract hour
    {
      $addFields: {
        hour: { $hour: '$createdAt' }
      }
    },
    
    // Group by hour
    {
      $group: {
        _id: '$hour',
        interactions: { $sum: 1 }
      }
    },
    
    // Sort by hour
    { $sort: { _id: 1 } },
    
    // Project output
    {
      $project: {
        hour: '$_id',
        interactions: 1,
        _id: 0
      }
    }
  ]);

  // Find peak hour
  const peakHour = peakHours.reduce((max, current) => 
    current.interactions > max.interactions ? current : max
  , { hour: 0, interactions: 0 });

  return {
    peakHour,
    hourlyBreakdown: peakHours
  };
}
```

### 9.4 Geographic Distribution

```typescript
async function getGeographicDistribution(businessId: Types.ObjectId, days: number = 7) {
  const startDate = new Date(Date.now() - days * 24 * 60 * 60 * 1000);
  const cards = await Card.find({ business: businessId }).distinct('_id');

  const distribution = await CardInteraction.aggregate([
    { $match: { card: { $in: cards }, createdAt: { $gte: startDate } } },
    
    // Lookup participant for location data
    {
      $lookup: {
        from: 'participants',
        localField: 'participant',
        foreignField: '_id',
        as: 'participantData'
      }
    },
    
    // Unwind participant (optional, if exists)
    {
      $addFields: {
        location: {
          $ifNull: [
            { $first: '$participantData.metadata.location' },
            'Unknown'
          ]
        }
      }
    },
    
    // Group by location
    {
      $group: {
        _id: '$location',
        interactions: { $sum: 1 }
      }
    },
    
    // Sort by interactions
    { $sort: { interactions: -1 } },
    
    // Project
    {
      $project: {
        location: '$_id',
        interactions: 1,
        _id: 0
      }
    }
  ]);

  const totalInteractions = distribution.reduce((sum, d) => sum + d.interactions, 0);

  return distribution.map(d => ({
    ...d,
    percentage: totalInteractions > 0 ? Math.round((d.interactions / totalInteractions) * 1000) / 10 : 0
  }));
}
```

### 9.5 Game Performance Analytics

```typescript
async function getGamePerformance(businessId: Types.ObjectId, days: number = 7) {
  const startDate = new Date(Date.now() - days * 24 * 60 * 60 * 1000);
  const landingPages = await LandingPage.find({ business: businessId }).distinct('_id');

  const [gameStats, prizeDistribution] = await Promise.all([
    // Game type breakdown
    GamePlay.aggregate([
      { $match: { landingPage: { $in: landingPages }, createdAt: { $gte: startDate } } },
      {
        $group: {
          _id: '$gameType',
          plays: { $sum: 1 },
          redeemed: { $sum: { $cond: ['$redeemed', 1, 0] } }
        }
      },
      {
        $project: {
          gameType: '$_id',
          plays: 1,
          redeemed: 1,
          redemptionRate: {
            $cond: [
              { $eq: ['$plays', 0] },
              0,
              { $round: [{ $multiply: [{ $divide: ['$redeemed', '$plays'] }, 100] }, 2] }
            ]
          },
          _id: 0
        }
      }
    ]),
    
    // Prize distribution
    GamePlay.aggregate([
      { $match: { landingPage: { $in: landingPages }, createdAt: { $gte: startDate } } },
      {
        $group: {
          _id: '$result',
          won: { $sum: 1 }
        }
      },
      { $sort: { won: -1 } },
      {
        $project: {
          prize: '$_id',
          won: 1,
          _id: 0
        }
      }
    ])
  ]);

  return {
    totalGamePlays: gameStats.reduce((sum, g) => sum + g.plays, 0),
    totalRedeemed: gameStats.reduce((sum, g) => sum + g.redeemed, 0),
    gameTypeBreakdown: gameStats,
    prizeDistribution
  };
}
```

---

## 10. Third-Party Integrations

### 10.1 MongoDB Atlas (Recommended)

**Why Atlas:**
- Built-in monitoring and alerting
- Automatic backups and point-in-time recovery
- Easy scaling (vertical and horizontal)
- Built-in data lake for analytics
- Global clusters for multi-region deployment

**Configuration:**
```typescript
// config/database.ts
import mongoose from 'mongoose';

export async function connectDatabase() {
  const mongoUri = process.env.MONGODB_URI || 'mongodb://localhost:27017/promotercard';
  
  mongoose.set('strictQuery', true);
  mongoose.set('debug', process.env.NODE_ENV === 'development');
  
  try {
    await mongoose.connect(mongoUri, {
      maxPoolSize: 100,
      minPoolSize: 10,
      serverSelectionTimeoutMS: 5000,
      socketTimeoutMS: 45000,
      retryWrites: true,
      retryReads: true,
    });
    
    console.log('✅ MongoDB connected successfully');
  } catch (error) {
    console.error('❌ MongoDB connection error:', error);
    process.exit(1);
  }
}
```

### 10.2 Redis (Caching & Queues)

**Purpose:**
- Session storage
- Rate limiting counters
- BullMQ job queues
- Cache for frequent queries

**Configuration:**
```typescript
// config/redis.ts
import { createClient } from 'redis';

export const redisClient = createClient({
  url: process.env.REDIS_URL || 'redis://localhost:6379',
  socket: {
    reconnectStrategy: (retries) => Math.min(retries * 50, 2000),
  },
});

redisClient.on('error', (err) => console.error('Redis Error:', err));

export async function connectRedis() {
  await redisClient.connect();
  console.log('✅ Redis connected');
}
```

---

## 11. Security Requirements

### 11.1 Authentication

```typescript
// JWT Configuration
export const jwtConfig = {
  accessToken: {
    secret: process.env.JWT_ACCESS_SECRET!,
    expiresIn: '15m',
  },
  refreshToken: {
    secret: process.env.JWT_REFRESH_SECRET!,
    expiresIn: '7d',
  },
};

// Password Hashing
import bcrypt from 'bcrypt';

export async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, 12);
}

export async function comparePassword(password: string, hash: string): Promise<boolean> {
  return bcrypt.compare(password, hash);
}
```

### 11.2 Rate Limiting

```typescript
// Rate limiting per endpoint type
export const rateLimits = {
  public: {
    windowMs: 60 * 1000,    // 1 minute
    max: 100,                // 100 requests
    standardHeaders: true,
    keyGenerator: (req) => req.ip,
  },
  admin: {
    windowMs: 60 * 1000,
    max: 1000,
  },
  auth: {
    windowMs: 60 * 1000,
    max: 5,
  },
  game: {
    windowMs: 60 * 60 * 1000,  // 1 hour
    max: 12,
    keyGenerator: (req) => req.body.deviceFingerprint || req.ip,
  },
};
```

### 11.3 Data Validation

```typescript
// Using Joi for validation
import Joi from 'joi';

export const participantSchema = Joi.object({
  name: Joi.string().required().max(100).trim(),
  email: Joi.string().email().required().lowercase(),
  phone: Joi.string().pattern(/^\+?[1-9]\d{1,14}$/).allow('', null),
  promoter: Joi.string().pattern(/^[0-9a-fA-F]{24}$/).allow(null),
  consentGiven: Joi.boolean().required(),
});
```

---

## 12. Performance Optimization

### 12.1 Query Optimization

```typescript
// Use lean() for read-only queries
const participants = await Participant.find({ landingPage: pageId })
  .lean()
  .sort({ createdAt: -1 })
  .limit(20);

// Use select() to limit fields
const promoters = await Promoter.find({ business: businessId })
  .select('name nickname totalInteractions status')
  .lean();

// Use indexes explicitly
const card = await Card.findOne({ nfcUuid: uuid })
  .hint({ nfcUuid: 1 })
  .lean();
```

### 12.2 Caching Strategy

```typescript
// Redis caching for frequent queries
async function getCachedLandingPage(slug: string) {
  const cacheKey = `landing_page:${slug}`;
  
  // Check cache
  const cached = await redisClient.get(cacheKey);
  if (cached) {
    return JSON.parse(cached);
  }
  
  // Query database
  const page = await LandingPage.findOne({ slug }).lean();
  
  // Cache for 5 minutes (if published)
  if (page?.status === 'published') {
    await redisClient.setEx(cacheKey, 300, JSON.stringify(page));
  }
  
  return page;
}

// Cache invalidation on update
async function invalidateLandingPageCache(slug: string) {
  await redisClient.del(`landing_page:${slug}`);
}
```

### 12.3 Bulk Operations

```typescript
// Bulk write for analytics aggregation
async function updateDailyAnalytics(businessId: Types.ObjectId, date: Date) {
  const startDate = new Date(date.setHours(0, 0, 0, 0));
  const endDate = new Date(date.setHours(23, 59, 59, 999));
  
  const cards = await Card.find({ business: businessId }).distinct('_id');
  const landingPages = await LandingPage.find({ business: businessId }).distinct('_id');
  
  const [interactions, signups, gamePlays, reviewClicks] = await Promise.all([
    CardInteraction.countDocuments({ card: { $in: cards }, createdAt: { $gte: startDate, $lte: endDate } }),
    Participant.countDocuments({ landingPage: { $in: landingPages }, createdAt: { $gte: startDate, $lte: endDate } }),
    GamePlay.countDocuments({ landingPage: { $in: landingPages }, createdAt: { $gte: startDate, $lte: endDate } }),
    ReviewClick.countDocuments({ landingPage: { $in: landingPages }, createdAt: { $gte: startDate, $lte: endDate } }),
  ]);
  
  // Upsert daily analytics
  await AnalyticsDaily.updateOne(
    { business: businessId, date: startDate },
    {
      $set: {
        totalInteractions: interactions,
        totalSignups: signups,
        totalGamePlays: gamePlays,
        totalReviewClicks: reviewClicks,
      }
    },
    { upsert: true }
  );
}
```

---

## 13. Scalability & Sharding Strategy

### 13.1 Sharding Strategy

**Shard Key Selection:**

| Collection | Shard Key | Rationale |
|------------|-----------|-----------|
| CardInteraction | `{ createdAt: 'hashed' }` | Time-series data, even distribution |
| GamePlay | `{ createdAt: 'hashed' }` | High-volume writes |
| Participant | `{ landingPage: 1, createdAt: 1 }` | Compound shard key for locality |
| SocialLinkClick | `{ createdAt: 'hashed' }` | High-volume, time-based |
| ReviewClick | `{ landingPage: 1, createdAt: 1 }` | Business locality |

**Sharding Configuration:**
```typescript
// Enable sharding on database
db.adminCommand({ enableSharding: 'promotercard' });

// Shard collections
db.adminCommand({
  shardCollection: 'promotercard.cardinteractions',
  key: { createdAt: 'hashed' }
});

db.adminCommand({
  shardCollection: 'promotercard.gameplays',
  key: { createdAt: 'hashed' }
});
```

### 13.2 Read Preference

```typescript
// Use secondary reads for analytics
mongoose.connect(uri, {
  readPreference: 'secondaryPreferred',
});

// Primary reads for transactions
const session = await mongoose.startSession();
session.startTransaction({
  readConcern: { level: 'snapshot' },
  writeConcern: { w: 'majority' },
});
```

---

## 14. Assumptions & Dependencies

### 14.1 Assumptions

1. MongoDB Atlas or self-hosted MongoDB v7+ available
2. Redis available for caching and queues
3. Node.js v20+ runtime environment
4. NFC card supplier provides consistent UUID format
5. Google Business API access for review tracking

### 14.2 External Dependencies

| Dependency | Purpose | Version |
|------------|---------|---------|
| mongoose | MongoDB ODM | ^8.0.0 |
| express | Web framework | ^4.18.0 |
| jsonwebtoken | JWT authentication | ^9.0.0 |
| bcrypt | Password hashing | ^5.1.0 |
| redis | Redis client | ^4.6.0 |
| bullmq | Job queues | ^4.0.0 |
| joi | Validation | ^17.11.0 |
| winston | Logging | ^3.11.0 |

---

## 15. Success Metrics

### 15.1 Technical Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| API Response Time (p95) | < 200ms | APM monitoring |
| Database Query Time (p95) | < 50ms | MongoDB profiler |
| Cache Hit Rate | > 80% | Redis stats |
| Error Rate | < 0.1% | Error tracking |
| Uptime | 99.9% | Monitoring |

### 15.2 Database Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Index Hit Ratio | > 95% | MongoDB metrics |
| Connection Pool Utilization | < 80% | MongoDB metrics |
| Replication Lag | < 1 second | MongoDB metrics |
| Document Size (avg) | < 100KB | MongoDB metrics |

---

**End of Product Requirements Document v2 (MongoDB Edition)**
