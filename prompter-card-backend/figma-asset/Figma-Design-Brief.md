# PromoterCard - Figma Design Brief
## Missing Screens Design Specification

**Project:** PromoterCard - NFC-Powered Event Promotion Platform  
**Document Type:** Design Brief for Missing Screens  
**Version:** 1.0  
**Created:** April 7, 2026  
**Target Audience:** UI/UX Designer  
**Status:** Ready for Design  

---

## Table of Contents

1. [Overview](#1-overview)
2. [Design System Guidelines](#2-design-system-guidelines)
3. [Promoter Dashboard Screens](#3-promoter-dashboard-screens)
4. [Customer-Facing Landing Page Screens](#4-customer-facing-landing-page-screens)
5. [Responsive Breakpoints](#5-responsive-breakpoints)
6. [Component Library Requirements](#6-component-library-requirements)
7. [Interaction Specifications](#7-interaction-specifications)
8. [Edge Cases & Error States](#8-edge-cases--error-states)
9. [Deliverables Checklist](#9-deliverables-checklist)
10. [Reference Materials](#10-reference-materials)

---

## 1. Overview

### 1.1 Purpose

This document specifies the design requirements for **missing screens** in the PromoterCard platform. These screens need to be designed in Figma before frontend development can begin.

### 1.2 Missing Screens

| Screen Set | Priority | Platform | Status |
|------------|----------|----------|--------|
| **Promoter Dashboard** | High | Web (Responsive) | ❌ Needs Design |
| **Customer Landing Pages** | High | Mobile-First Web | ❌ Needs Design |

### 1.3 Existing Designs (For Reference)

The following screens **already exist** in Figma and should be used as design reference:
- Admin Dashboard (all screens in `admin-dashboard/` folder)
- Dashboard overview, analytics, campaigns, cards, landing pages, games & prizes, participants, promoters, reports, reviews, settings, social links

**Key Design Patterns to Match:**
- Color scheme (orange primary, teal secondary, purple accent)
- Card-based layout with rounded corners
- Icon style (outlined icons with colored backgrounds)
- Typography hierarchy
- Spacing system (8px grid)
- Button styles and states

---

## 2. Design System Guidelines

### 2.1 Color Palette

**Primary Colors:**
```
Orange (Primary):    #F97316 (buttons, highlights, CTAs)
Orange Light:        #FFF7ED (backgrounds, hover states)
Orange Dark:         #EA580C (pressed states, dark mode)

Teal (Secondary):    #14B8A6 (secondary actions, info)
Teal Light:          #F0FDFA (backgrounds)
Teal Dark:           #0D9488 (pressed states)

Purple (Accent):     #8B5CF6 (reviews, special actions)
Purple Light:        #F5F3FF (backgrounds)
Purple Dark:         #7C3AED (pressed states)

Yellow (Warning):    #F59E0B (warnings, highlights)
Yellow Light:        #FFFBEB (backgrounds)

Green (Success):     #10B981 (success states, confirmed)
Green Light:         #ECFDF5 (backgrounds)

Red (Error):         #EF4444 (errors, delete actions)
Red Light:           #FEF2F2 (backgrounds)
```

**Neutral Colors:**
```
Gray 900: #111827 (primary text)
Gray 700: #374151 (secondary text)
Gray 500: #6B7280 (placeholder text)
Gray 300: #D1D5DB (borders, dividers)
Gray 100: #F3F4F6 (backgrounds)
White:    #FFFFFF (cards, surfaces)
```

### 2.2 Typography

**Font Family:** Inter (or similar sans-serif)

**Type Scale:**
```
Display:     32px / 40px (page titles)
H1:          28px / 36px (section titles)
H2:          24px / 32px (card titles)
H3:          20px / 28px (subsection titles)
H4:          18px / 24px (card headers)
Body Large:  16px / 24px (primary content)
Body:        14px / 20px (default text)
Body Small:  12px / 16px (secondary text, captions)
Caption:     10px / 14px (metadata, timestamps)
```

**Font Weights:**
```
Regular: 400 (body text)
Medium:  500 (buttons, labels)
Semibold: 600 (headings, emphasis)
Bold:    700 (titles, highlights)
```

### 2.3 Spacing System

**8px Grid System:**
```
4px   (micro spacing)
8px   (small spacing)
12px  (compact spacing)
16px  (default spacing)
24px  (medium spacing)
32px  (large spacing)
48px  (section spacing)
64px  (page spacing)
```

### 2.4 Component Styles

**Cards:**
- Border radius: 12px
- Shadow: 0 1px 3px rgba(0,0,0,0.1), 0 1px 2px rgba(0,0,0,0.06)
- Padding: 24px
- Background: White
- Border: 1px solid #E5E7EB

**Buttons:**
- Primary: Orange background, white text, 8px radius, 12px padding
- Secondary: White background, orange border, orange text
- Ghost: Transparent background, orange text
- Sizes: Large (48px height), Medium (40px), Small (32px)

**Inputs:**
- Height: 48px
- Border radius: 8px
- Border: 1px solid #D1D5DB
- Focus: 2px solid orange
- Padding: 12px 16px

**Badges/Tags:**
- Small pill shape (16px height)
- Colored background with matching text color
- Padding: 4px 12px
- Border radius: 9999px (full pill)

---

## 3. Promoter Dashboard Screens

### 3.1 Overview

**Platform:** Web (Responsive - Desktop, Tablet, Mobile)  
**Primary Users:** Promoters (field staff)  
**Key Goals:** 
- View personal performance stats
- Manage assigned NFC cards
- Track upcoming events
- Validate prize redemptions
- Update personal profile

**Design Approach:**
- Clean, mobile-friendly interface
- Promoters work on-the-go, so mobile experience is critical
- Quick access to key stats and actions
- Simple, intuitive navigation

---

### 3.2 Screen List

| Screen ID | Screen Name | Description | Priority |
|-----------|-------------|-------------|----------|
| P-01 | Login Screen | Promoter login with email/password | P0 |
| P-02 | Dashboard Overview | Personal stats, ranking, recent activity | P0 |
| P-03 | My Cards | View assigned NFC cards with stats | P0 |
| P-04 | My Events | Upcoming and past events | P0 |
| P-05 | Event Details | Event info, real-time stats | P1 |
| P-06 | Prize Validation | Scan/verify customer prizes | P0 |
| P-07 | Prize History | Redemption history | P1 |
| P-08 | Performance Stats | Detailed performance breakdown | P1 |
| P-09 | Leaderboard | Team ranking view | P1 |
| P-10 | Profile Settings | Update personal info | P1 |
| P-11 | Notifications | Notifications center | P2 |
| P-12 | Help/Support | FAQ, contact support | P2 |

---

### 3.3 Screen Specifications

#### **P-01: Login Screen**

**Purpose:** Promoter authentication

**Layout:**
- Centered login card
- Logo at top
- Email input field
- Password input field
- Login button (orange, full width)
- "Forgot Password?" link
- Error message display (for invalid credentials)

**Components:**
- Logo (PromoterCard brand)
- Email input (with icon)
- Password input (with show/hide toggle)
- Primary button (Login)
- Text link (Forgot Password)
- Error state banner

**States:**
- Default
- Loading (spinner on button)
- Error (invalid credentials message)
- Success (redirect to dashboard)

**Responsive:**
- Desktop: Centered card (max-width 400px)
- Tablet: Full width with padding
- Mobile: Full width, minimal padding

---

#### **P-02: Dashboard Overview**

**Purpose:** Promoter's main dashboard with personal stats

**Layout:**
- Header with greeting ("Good morning, [Name]")
- Performance summary cards (row of 3-4 cards)
- My Rank card (prominent)
- Recent Activity feed
- Quick Actions section
- Upcoming Events (mini list)

**Performance Summary Cards:**
1. **Interactions** - Total interactions this week (with trend %)
2. **Signups** - Total signups generated (with trend %)
3. **Reviews** - Reviews attributed (with trend %)
4. **My Rank** - Current ranking among team (e.g., "#2 of 5")

**Recent Activity Feed:**
- List of recent interactions
- Each item shows: time, action type, details
- "View All" link to full activity log

**Quick Actions:**
- View My Cards
- View My Events
- Validate Prize
- View Leaderboard

**Upcoming Events:**
- Next 2-3 events
- Each shows: event name, date, venue
- "View All Events" link

**Navigation:**
- Bottom tab bar (mobile) or sidebar (desktop)
- Tabs: Dashboard, Cards, Events, Prizes, Profile

**Responsive:**
- Desktop: Sidebar navigation, 2-column layout
- Tablet: Sidebar navigation, single column
- Mobile: Bottom tab bar, single column cards

---

#### **P-03: My Cards**

**Purpose:** View assigned NFC cards and their performance

**Layout:**
- Header: "My Cards" + "Active Cards: X"
- Card grid/list showing assigned cards
- Each card shows:
  - Card name/type (NFC or QR)
  - Status badge (Active/Inactive)
  - Total interactions
  - Last tap time
  - View Details button
  - Download QR button (if QR card)

**Card Display:**
- Visual card representation (colored card mockup)
- Card info overlay (name, stats)
- Action buttons (View Details, Download)

**States:**
- Active cards (green badge)
- Inactive cards (gray badge)
- Empty state (no cards assigned)

**Interactions:**
- Tap card to view details
- Download QR code (if applicable)
- Pull to refresh

**Responsive:**
- Desktop: Grid layout (3 columns)
- Tablet: Grid layout (2 columns)
- Mobile: Single column list

---

#### **P-04: My Events**

**Purpose:** View upcoming and past events

**Layout:**
- Header: "My Events"
- Tab switcher: Upcoming | Past
- Event cards list

**Event Card:**
- Event name
- Date and time
- Venue name
- Location
- Signed up count
- Status badge (Scheduled, Active, Completed)
- "View Details" button
- "Check In" button (for upcoming events)

**Upcoming Events:**
- Sorted by date (nearest first)
- "Check In" action available 1 hour before event

**Past Events:**
- Sorted by date (most recent first)
- Shows final stats (total interactions, signups)

**States:**
- Upcoming event (green badge)
- Active event (orange badge)
- Completed event (gray badge)
- Empty state (no events)

---

#### **P-05: Event Details**

**Purpose:** Detailed event information with real-time stats

**Layout:**
- Header with back button and event name
- Event info section:
  - Date, time, venue, location
  - Status badge
- Real-time stats section:
  - Interactions (live counter)
  - Signups (live counter)
  - Reviews (live counter)
- Assigned Promoters list
- Action buttons:
  - Start Event (if scheduled)
  - End Event (if active)
  - View Report (if completed)

**Real-Time Stats:**
- Animated counters (update every 30 seconds)
- Trend indicators (up/down arrows)
- Last updated timestamp

**Assigned Promoters:**
- List of promoters assigned to event
- Each shows name, interactions count
- "View Promoter Stats" link

---

#### **P-06: Prize Validation**

**Purpose:** Validate customer prize claims

**Layout:**
- Header: "Validate Prize"
- Scanner interface (QR code scanner)
- Manual entry option (prize code input)
- Validation result display

**Scanner Interface:**
- Camera viewfinder (for mobile)
- Scan button
- "Enter Code Manually" link

**Manual Entry:**
- Prize code input field
- Validate button
- Loading state

**Validation Result:**
- Success: Green checkmark, prize details, "Mark as Redeemed" button
- Error: Red X, error message, "Try Again" button

**Prize Details (on success):**
- Prize name
- Won date
- Customer name (if provided)
- Promoter who generated the prize
- Status (Pending/Redeemed)

---

#### **P-07: Prize History**

**Purpose:** View redemption history

**Layout:**
- Header: "Prize History"
- Filter options: All | Redeemed | Pending
- List of prize redemptions

**Prize Item:**
- Prize name
- Customer name (if available)
- Date redeemed
- Status badge
- View Details button

**Filter States:**
- All prizes
- Redeemed only
- Pending only

---

#### **P-08: Performance Stats**

**Purpose:** Detailed performance breakdown

**Layout:**
- Header: "Performance Stats"
- Time range selector: Today | Week | Month | Custom
- Stats overview cards
- Performance charts
- Breakdown by category

**Stats Cards:**
- Total Interactions
- Total Signups
- Total Reviews
- Conversion Rate

**Charts:**
- Interactions over time (line chart)
- Peak activity hours (bar chart)
- Game performance breakdown (pie chart)

**Breakdown:**
- By card (which cards generated most interactions)
- By event (which events performed best)
- By game type (dice vs spin wheel)

---

#### **P-09: Leaderboard**

**Purpose:** View team ranking

**Layout:**
- Header: "Leaderboard"
- Time range selector: Today | Week | Month
- Top performers list

**Leaderboard Item:**
- Rank number (with trophy icon for #1)
- Promoter avatar and name
- Stats: Interactions, Signups, Reviews
- Trend indicator (up/down)
- Highlight current user's position

**States:**
- Top 3 (special styling with medals)
- Current user (highlighted row)
- Others (standard styling)

---

#### **P-10: Profile Settings**

**Purpose:** Update personal information

**Layout:**
- Header: "Profile Settings"
- Profile photo section (with upload button)
- Personal info form:
  - Name
  - Nickname (for review matching)
  - Email
  - Phone
- Account section:
  - Change Password
  - Notifications Preferences
- Danger zone:
  - Logout button

**Form Fields:**
- Editable fields with save button
- Read-only fields (email, if managed by admin)
- Validation feedback

---

### 3.4 Navigation Structure

```
Promoter Dashboard Navigation:

┌─────────────────────────────────────────────────────────────────────┐
│                        MOBILE (Bottom Tabs)                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌──────────┐  ┌──────────  ┌──────────┐  ┌──────────┐  ┌──────┐ │
│  │          │  │          │  │          │  │          │  │      │ │
│  │ Dashboard│  │  Cards   │  │  Events  │  │  Prizes  │  │Profile│ │
│  │          │  │          │  │          │  │          │  │      │ │
│  └──────────┘  └──────────┘  └──────────  └──────────┘  └──────┘ │
│                                                                       │
│  Dashboard Flow:                                                       │
│  Dashboard → Performance Stats → Leaderboard                          │
│  Dashboard → Event Details → Event Report                             │
│                                                                       │
│  Cards Flow:                                                           │
│  My Cards → Card Details → Card Interactions                          │
│                                                                       │
│  Events Flow:                                                          │
│  My Events → Event Details → Real-Time Stats                          │
│                                                                       │
│  Prizes Flow:                                                          │
│  Prize Validation → Validation Result → Prize History                 │
│                                                                       │
│  Profile Flow:                                                         │
│  Profile Settings → Change Password → Notifications                    │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 4. Customer-Facing Landing Page Screens

### 4.1 Overview

**Platform:** Mobile-First Web (Responsive - Mobile, Tablet, Desktop)  
**Primary Users:** Customers (end users who tap NFC cards)  
**Key Goals:**
- View event details
- Play games (dice/spin wheel)
- Sign up for guest list
- Leave Google reviews
- Access social links

**Design Approach:**
- Mobile-first design (most users will be on mobile)
- Fast loading (CDN-delivered)
- Engaging animations (game interactions)
- Clear CTAs
- Minimal friction

---

### 4.2 Screen List

| Screen ID | Screen Name | Description | Priority |
|-----------|-------------|-------------|----------|
| L-01 | Landing Page - Hero | Event hero section with image | P0 |
| L-02 | Landing Page - Event Details | Event info section | P0 |
| L-03 | Landing Page - Description | Event description section | P1 |
| L-04 | Landing Page - Game Section | Game interaction area | P0 |
| L-05 | Landing Page - Sign-Up Form | Guest list sign-up form | P0 |
| L-06 | Landing Page - Review Prompt | Google review prompt | P0 |
| L-07 | Landing Page - Social Links | Social media links | P1 |
| L-08 | Landing Page - Contact Info | Contact information | P1 |
| L-09 | Game Result Screen | Prize won display | P0 |
| L-10 | Sign-Up Confirmation | Success message after signup | P0 |
| L-11 | Prize Redemption Screen | Prize details and redemption instructions | P0 |

---

### 4.3 Screen Specifications

#### **L-01: Landing Page - Hero Section**

**Purpose:** First impression with event branding

**Layout:**
- Full-width hero image
- Event title overlay
- Subtitle/tagline
- Scroll indicator (subtle animation)

**Hero Image:**
- High-quality event image
- Dark overlay for text readability
- Gradient fade at bottom

**Text Overlay:**
- Event title (large, bold)
- Subtitle (medium, lighter)
- Location badge (if applicable)

**Responsive:**
- Mobile: Full-screen hero (100vh)
- Tablet: 80vh hero
- Desktop: 60vh hero with side content

---

#### **L-02: Landing Page - Event Details**

**Purpose:** Event information display

**Layout:**
- Event details section (below hero)
- Info cards with icons:
  - Date
  - Time
  - Venue
  - Location

**Info Card:**
- Icon (calendar, clock, location pin)
- Label (Date, Time, Venue)
- Value (event-specific)

**Layout Options:**
- Grid layout (2 columns on desktop, 1 column on mobile)
- Card style with icons

---

#### **L-03: Landing Page - Description**

**Purpose:** Event description and details

**Layout:**
- Section title
- Description text
- Optional: Image gallery

**Description:**
- Rich text content
- Readable line length (max 60-70 characters)
- Proper spacing

---

#### **L-04: Landing Page - Game Section**

**Purpose:** Interactive game area

**Layout:**
- Game container (prominent, centered)
- Game title ("Roll to Win!" or "Spin to Win!")
- Game visual (dice or wheel)
- Play button (large, prominent)
- Prize info (what you can win)

**Dice Game:**
- 3D dice visualization
- "ROLL THE DICE" button (orange, full width)
- Animation on click
- Result reveal animation

**Spin Wheel Game:**
- Wheel with prize segments
- "SPIN" button
- Spinning animation
- Result pointer animation

**Prize Info:**
- List of possible prizes
- Small text below game

**States:**
- Default (ready to play)
- Playing (animation)
- Result (prize revealed)
- Rate limited (cooldown timer)

---

#### **L-05: Landing Page - Sign-Up Form**

**Purpose:** Guest list sign-up

**Layout:**
- Form container
- Section title ("Join the Guest List")
- Form fields:
  - Name (required)
  - Email (required)
  - Phone (optional)
  - Promoter selector (dropdown)
  - Consent checkbox (required)
- Submit button (orange, full width)

**Form Fields:**
- Input with label
- Placeholder text
- Validation feedback (error states)
- Required field indicator (*)

**Promoter Selector:**
- Dropdown with promoter names
- "Who referred you?" label
- Optional field

**Consent Checkbox:**
- Checkbox with label
- GDPR compliance text
- Required to submit

**States:**
- Default
- Validation errors
- Loading (submitting)
- Success (redirect to confirmation)

---

#### **L-06: Landing Page - Review Prompt**

**Purpose:** Encourage Google reviews

**Layout:**
- Review prompt section
- Prompt text
- "Leave a Review" button
- Optional: Incentive text

**Prompt Text:**
- "Enjoying the event? Leave us a review!"
- Or: "Leave a review and mention your server's name!"

**Button:**
- "Leave a Review" (orange)
- Opens Google review URL in new tab

**Incentive:**
- "Get a bonus prize for leaving a review!"
- Or similar incentive text

---

#### **L-07: Landing Page - Social Links**

**Purpose:** Social media engagement

**Layout:**
- Social links section
- Social media buttons:
  - Instagram
  - TikTok
  - WhatsApp
  - Facebook
  - Website

**Button Style:**
- Icon-based buttons
- Platform-specific colors
- Circular or rounded rectangle
- Click tracking

**Layout:**
- Row of buttons (scrollable on mobile)
- Icon + optional label

---

#### **L-08: Landing Page - Contact Info**

**Purpose:** Contact information display

**Layout:**
- Contact section
- Phone number (with call button)
- Email address (with email button)
- Website link

**Button Style:**
- Icon buttons
- Direct action (tap to call, tap to email)

---

#### **L-09: Game Result Screen**

**Purpose:** Display prize won

**Layout:**
- Modal/overlay
- Celebration animation (confetti, etc.)
- Prize reveal
- Prize details
- Redemption instructions
- Close button or "Continue" button

**Celebration:**
- Confetti animation
- "Congratulations!" text
- Prize name (large)

**Prize Details:**
- Prize name
- Prize description (if applicable)
- Redemption instructions

**Redemption Instructions:**
- "Show this screen to redeem"
- Or: "Prize code: ABC123"
- QR code for validation (optional)

**Actions:**
- "Claim Prize" button
- "Continue" button (to sign-up form)

---

#### **L-10: Sign-Up Confirmation**

**Purpose:** Confirm successful sign-up

**Layout:**
- Full-screen confirmation
- Success icon (checkmark)
- "You're In!" message
- Confirmation details
- Next steps
- "Back to Event" button

**Confirmation Details:**
- "You've been added to the guest list"
- "Check your email for confirmation"

**Next Steps:**
- "Leave a review to get a bonus prize"
- "Follow us on social media"

---

#### **L-11: Prize Redemption Screen**

**Purpose:** Show prize details for redemption

**Layout:**
- Prize display screen
- Prize name
- Prize details
- QR code (for staff scanning)
- Prize code (for manual validation)
- Expiration info (if applicable)

**QR Code:**
- Scannable QR code
- Contains prize ID and validation data

**Prize Code:**
- Alphanumeric code
- Large, readable font

---

### 4.4 Page Block Variations

**Landing pages are built from blocks. Each block can be toggled on/off.**

| Block | Description | Required |
|-------|-------------|----------|
| Hero Section | Event hero image and title | Yes |
| Event Details | Date, time, venue info | Yes |
| Description | Event description text | No |
| Prize Game | Dice roll or spin wheel game | Yes |
| Sign-Up Form | Guest list sign-up | Yes |
| Review Prompt | Google review prompt | No |
| Social Links | Social media buttons | No |
| Contact Info | Phone, email, website | No |

---

### 4.5 Responsive Breakpoints

| Breakpoint | Width | Layout Changes |
|------------|-------|----------------|
| Mobile | < 640px | Single column, full-width elements |
| Tablet | 640px - 1024px | 2-column grid where applicable |
| Desktop | > 1024px | Multi-column, side-by-side sections |

---

## 5. Responsive Breakpoints

### 5.1 Promoter Dashboard

| Breakpoint | Width | Navigation | Layout |
|------------|-------|------------|--------|
| Mobile | < 640px | Bottom tab bar | Single column cards |
| Tablet | 640px - 1024px | Sidebar (collapsible) | 2-column grid |
| Desktop | > 1024px | Sidebar (fixed) | Multi-column layout |

### 5.2 Customer Landing Pages

| Breakpoint | Width | Layout |
|------------|-------|--------|
| Mobile | < 640px | Single column, stacked sections |
| Tablet | 640px - 1024px | 2-column grid for info cards |
| Desktop | > 1024px | Centered content, max-width container |

---

## 6. Component Library Requirements

### 6.1 Components to Create

**Buttons:**
- Primary (orange background)
- Secondary (white background, orange border)
- Ghost (transparent)
- Icon button
- Sizes: Large, Medium, Small
- States: Default, Hover, Pressed, Disabled, Loading

**Inputs:**
- Text input
- Textarea
- Dropdown/Select
- Checkbox
- Radio button
- File upload
- States: Default, Focus, Error, Disabled

**Cards:**
- Stat card (with icon, number, label)
- Info card (with title, content)
- Event card (with event details)
- Promoter card (with avatar, name, stats)
- Prize card (with prize info)

**Navigation:**
- Bottom tab bar (mobile)
- Sidebar navigation (desktop)
- Tab switcher
- Breadcrumbs

**Badges/Tags:**
- Status badges (Active, Inactive, Completed)
- Category tags
- Notification badges

**Lists:**
- Simple list
- Card list
- Table list (desktop)

**Modals:**
- Confirmation modal
- Prize reveal modal
- Full-screen modal

**Empty States:**
- No cards assigned
- No events
- No activities
- Error state

---

## 7. Interaction Specifications

### 7.1 Animations

**Page Transitions:**
- Fade in (landing page load)
- Slide in (modals, drawers)
- Scale (cards on hover)

**Micro-interactions:**
- Button hover (scale 1.02)
- Button press (scale 0.98)
- Card hover (shadow increase)
- Loading spinner
- Success checkmark animation
- Error shake animation

**Game Animations:**
- Dice roll animation (3D rotation)
- Spin wheel animation (rotation with easing)
- Confetti on prize win
- Prize reveal (scale + fade)

**Chart Animations:**
- Line chart draw animation
- Bar chart grow animation
- Donut chart fill animation

### 7.2 Gestures (Mobile)

- Swipe to refresh
- Swipe to dismiss (notifications)
- Pull to refresh
- Long press for context menu
- Pinch to zoom (images)

---

## 8. Edge Cases & Error States

### 8.1 Promoter Dashboard

| Scenario | Error State | Design Solution |
|----------|-------------|-----------------|
| No cards assigned | Empty state | "No cards assigned yet" message |
| No events | Empty state | "No events scheduled" message |
| Network error | Error banner | "Connection lost" with retry button |
| Loading | Skeleton screens | Skeleton placeholders |
| Invalid credentials | Error message | Red banner with error text |
| Session expired | Redirect to login | Auto-redirect with message |

### 8.2 Customer Landing Pages

| Scenario | Error State | Design Solution |
|----------|-------------|-----------------|
| Page not found | 404 page | "Page not found" with home button |
| Game rate limited | Cooldown timer | "Try again in X minutes" message |
| Form validation error | Inline errors | Red text below input fields |
| Duplicate email | Error message | "This email is already registered" |
| Network error | Error banner | "Connection lost" with retry |
| Loading | Skeleton screens | Skeleton placeholders |

---

## 9. Deliverables Checklist

### 9.1 Figma Files Required

**Promoter Dashboard:**
- [ ] P-01 Login Screen
- [ ] P-02 Dashboard Overview
- [ ] P-03 My Cards
- [ ] P-04 My Events
- [ ] P-05 Event Details
- [ ] P-06 Prize Validation
- [ ] P-07 Prize History
- [ ] P-08 Performance Stats
- [ ] P-09 Leaderboard
- [ ] P-10 Profile Settings
- [ ] P-11 Notifications
- [ ] P-12 Help/Support

**Customer Landing Pages:**
- [ ] L-01 Hero Section
- [ ] L-02 Event Details
- [ ] L-03 Description
- [ ] L-04 Game Section (Dice)
- [ ] L-05 Game Section (Spin Wheel)
- [ ] L-06 Sign-Up Form
- [ ] L-07 Review Prompt
- [ ] L-08 Social Links
- [ ] L-09 Contact Info
- [ ] L-10 Game Result Screen
- [ ] L-11 Sign-Up Confirmation
- [ ] L-12 Prize Redemption Screen

**Component Library:**
- [ ] Buttons (all variants)
- [ ] Inputs (all variants)
- [ ] Cards (all variants)
- [ ] Navigation components
- [ ] Badges/Tags
- [ ] Lists
- [ ] Modals
- [ ] Empty states
- [ ] Icons

**Design System:**
- [ ] Color styles
- [ ] Text styles
- [ ] Effect styles (shadows)
- [ ] Layout grid

### 9.2 Deliverable Format

- Figma file with all screens
- Organized by pages (Promoter Dashboard, Customer Landing Pages, Components)
- Auto-layout used for responsive components
- Design tokens exported
- Prototype links for key flows

---

## 10. Reference Materials

### 10.1 Existing Figma Designs

Located in: `figma-asset/admin-dashboard/`

**Screens to Reference:**
- `dashboard/performance-overview-01.png` - Dashboard layout
- `cards/card-management-01.png` - Card display
- `promoters/promoters-01.png` - Promoter list
- `campaigns/campaigns.png` - Campaign layout
- `games-and-prizes/game-performance-01.png` - Game display
- `participants/captured-audienced-data.png` - Data table

### 10.2 Brand Assets

- Logo: PromoterCard logo (orange card icon)
- Icon style: Outlined icons with colored backgrounds
- Illustrations: (if available)

### 10.3 Technical Constraints

- Landing pages must load in < 2 seconds
- Game animations must be performant on low-end devices
- Mobile-first design (most traffic from mobile)
- CDN-delivered static assets

---

**End of Design Brief**

**Document Version:** 1.0  
**Last Updated:** April 7, 2026  
**Status:** Ready for Design  
**Next Steps:** Designer to create Figma screens based on this brief
