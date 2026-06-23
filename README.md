# Cryter - Full-Stack Crypto Investment Platform

> This repository contains a showcase of Cryter, a full-stack financial platform built across 30+ development phases. The production source code is private; this repository documents the product architecture, features, workflows, and implementation highlights.

<br />

<div align="center">

![Status](https://img.shields.io/badge/Status-Production%20Ready-22c55e?style=for-the-badge)
![Backend](https://img.shields.io/badge/Backend-Django%20REST%20Framework-092e20?style=for-the-badge&logo=django)
![Frontend](https://img.shields.io/badge/Frontend-Next.js%2014-000000?style=for-the-badge&logo=nextdotjs)
![Database](https://img.shields.io/badge/Database-PostgreSQL-336791?style=for-the-badge&logo=postgresql)
![Auth](https://img.shields.io/badge/Auth-JWT%20%2B%20TOTP%202FA-f59e0b?style=for-the-badge)
![Deployment](https://img.shields.io/badge/Deployment-Render-46e3b7?style=for-the-badge)

</div>

<br />

---

## Table of Contents

- [Overview](#overview)
- [Why Cryter Was Built](#why-cryter-was-built)
- [Feature Breakdown](#feature-breakdown)
- [User Dashboard](#user-dashboard)
- [Admin Dashboard](#admin-dashboard)
- [KYC Verification Workflow](#kyc-verification-workflow)
- [Wallet System](#wallet-system)
- [Investment Engine](#investment-engine)
- [Trading Pools](#trading-pools)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Security Model](#security-model)
- [User Journey](#user-journey)
- [Admin Journey](#admin-journey)
- [Flow Diagrams](#flow-diagrams)
- [Problems Solved During Development](#problems-solved-during-development)
- [Implementation Highlights](#implementation-highlights)
- [Production-Level Features](#production-level-features)
- [Scalability Considerations](#scalability-considerations)
- [Future Expansion Opportunities](#future-expansion-opportunities)
- [Demo](#demo)

---



## Demo

### Product Walkthrough

The repository includes a full walkthrough video covering:

- Authentication
- Email verification
- Wallet management
- KYC submission and review
- Investment creation
- Trading pools
- Withdrawal processing
- Notification system
- Admin control panel

The video demonstrates the platform's core workflows and production functionality.

[🎥 Watch Demo Video](./demo/cryter-demo.mp4)

---


## Overview

Cryter is a production-grade crypto investment platform that allows users to grow their capital through structured investment plans and admin-managed trading pools. It handles the full financial lifecycle - from user onboarding and KYC identity verification, through wallet funding and investment creation, to automated profit distribution and withdrawal processing.

The platform is built for operators who need full administrative control: every user, every wallet, every investment, every transaction, and every document submission is manageable from a dedicated admin control panel with a polished, information-dense UI.

Cryter is not a prototype. It is a complete, deployable financial product with authentication, security middleware, async task scheduling, transactional email, real-time market data, a referral system, multi-tier KYC, and role-based access control - all built and shipped by a single developer across 30+ focused development phases.

---

## Why Cryter Was Built

The crypto investment space is saturated with platforms that are either technically weak (no real KYC, no audit trail, no admin tooling) or prohibitively expensive to white-label. Cryter was built to demonstrate that a single developer, given the right architecture and discipline, can ship a platform that competes with funded teams on both technical depth and product quality.

**The problems Cryter solves:**

- Operators need to manage user investments without giving users direct access to trading - Cryter's pool system lets admins trade on behalf of users and distribute profits when ready
- Compliance requirements demand verified identities - Cryter's KYC system handles document upload, live selfie capture, and admin review in a single workflow
- Financial platforms need immutable audit trails - every wallet transaction in Cryter is recorded with type, status, balance-after, and reason
- Security cannot be an afterthought - Cryter implements IP banning, TOTP 2FA, client key middleware, login lockouts, and rate limiting from day one
- Admin tools are usually an afterthought - Cryter's control panel was built with the same care as the user dashboard, giving operators real visibility into the platform

---

## Feature Breakdown

### Authentication & Identity
- JWT-based authentication with silent access token refresh
- TOTP two-factor authentication (Google Authenticator compatible)
- Email verification with time-limited signed tokens
- Password reset via secure token sent to registered email
- Admin ability to disable 2FA for locked-out users

### Wallet System
- Per-user wallets with full double-entry transaction ledger
- Six transaction types: DEPOSIT, WITHDRAWAL, INVESTMENT, RETURN, ADJUSTMENT, REFERRAL_BONUS
- Admin balance adjustment (credit or debit) with mandatory reason field
- Withdrawal request queue with admin approve/reject workflow
- Multi-network deposit addresses (BTC, ETH, USDT) with QR codes

### Investment Plans
- APY-based investment plans with configurable duration, minimum, and maximum amounts
- Public plans (visible to all users) and private custom plans (assigned to one specific user)
- Automated daily maturity check via Celery beat - principal plus profit credited automatically on maturity date
- Soft delete system - plans with active investments cannot be deleted until maturity
- Hard delete - permanent removal only when zero investment records exist
- Per-plan example return calculator shown to users before investing

### Trading Pools
- Admin-created pools for collective or private managed trading
- Two-step workflow: pool creation (no funds move) followed by explicit investment addition (wallet deducted)
- Configurable platform fee and user share percentage per pool
- Manual value updates - admin sets new pool value after trading, profit calculated automatically
- Profit distribution - partial or full, proportional to each investor's share
- Pool completion - final payout calculated per investor, wallets credited, notifications sent
- Full distribution audit trail via PoolDistribution records

### KYC Verification
- Document submission: passport or national ID (front and back)
- Live selfie capture via device camera (no third-party service)
- Admin review panel with full document preview and approve/reject flow
- Rejection reason communicated back to user with resubmission allowed
- KYC status gates - platform can restrict investment and withdrawal for unverified users

### Market Data
- Real-time top 50 coins via CoinGecko proxy
- Trending coins section
- Individual coin detail pages with full market data, ATH/ATL, 7-day change
- Portfolio value endpoint - wallet balance plus active investment value in USD
- Server-side caching and rate limiting on all market endpoints

### Referral System
- Unique referral code generated per user at registration
- Shareable URL with `?ref=` parameter auto-filled on register page
- Referral bonus fires on referred user's first investment - credited to referrer's wallet
- Referral stats dashboard - total referrals, credited vs pending, total earned, full history

### Notification System
- Typed notifications: INVESTMENT_CREATED, INVESTMENT_MATURED, KYC_UPDATE, REFERRAL_BONUS, WITHDRAWAL_UPDATE, INFO
- Unread count badge in navigation, mark-all-read, per-notification read state
- Automatic firing on all key platform events - investment maturity, KYC decision, pool completion, withdrawal approval

### Support System
- User ticket submission with categories: GENERAL, WITHDRAWAL, INVESTMENT, KYC, TECHNICAL, OTHER
- Admin ticket management - status workflow: OPEN → IN_PROGRESS → RESOLVED → CLOSED
- Admin notes field for internal tracking separate from user-visible responses

---

## User Dashboard

The user dashboard is built with a glass-morphism aesthetic on a dark sapphire palette. Every screen is responsive and information-dense without feeling cluttered.

### Dashboard Home
The main dashboard presents the user's wallet balance prominently, followed by an active investments summary showing progress bars, days remaining until maturity, and expected returns. A portfolio value card aggregates wallet balance plus all active investment positions in USD using live market rates.

### Wallet Page
Users see their current balance, a paginated transaction history with type badges and status indicators, deposit addresses per cryptocurrency network displayed with QR codes, and a withdrawal request form. Every transaction shows the balance-after value so users can audit their own history.

### Investments Page
Displays all investment plans available to the user - public plans visible to everyone plus any private plans an admin has assigned exclusively to them. Each plan card shows the APY, duration, minimum amount, and an example return calculation based on the minimum investment. Active investments are listed separately with real-time progress bars.

### Market Page
A live cryptocurrency market overview powered by a CoinGecko proxy. Users can browse top coins by price, 24-hour change, volume, and market cap. Clicking a coin opens a detail page with full market data and a description. The proxy adds server-side rate limiting and caching so users always see fast responses.

### Pools Page
Users see trading pools that are either public or privately assigned to them. Each pool card shows the pool name, current value, profit/loss since inception, and the admin's public notes. Private pools marked as such are invisible to all other users.

### Referrals Page
A dedicated referrals page shows the user's unique referral code, a pre-built shareable URL, and earnings history. Stats show total referrals made, how many have been credited, and total bonus earnings.

### Settings Page
A multi-section settings page covers profile editing, password change, 2FA setup and management, KYC document submission with live camera capture, support ticket submission, and ticket history.

---

## Admin Dashboard

The admin control panel lives at `/cp-portal/control` and is protected at the middleware level - access requires both `is_staff = true` on the user account and a `cryter_is_staff` cookie set on successful admin login. The panel matches the visual quality of the user dashboard rather than feeling like a bolted-on afterthought.

### Overview
Platform-wide statistics at a glance: total users, active users, banned accounts, verified users, KYC queue size, total platform deposits, total withdrawals, pending withdrawal count and value, active investment count and value, total investment returns paid, and new user counts for today, this week, and this month. Statistics refresh every 60 seconds.

### Users
A searchable, filterable table of all platform users with balance, active investment count, KYC status, and verification state visible inline. Admins can filter by active/banned status, KYC state, and staff flag. Clicking a user opens their detail page.

### User Detail Page
The most feature-rich admin page. Displays complete profile information, financial summary (balance, total deposited, total withdrawn, total invested, active investment count), KYC status with inline override, email copy button, 2FA status with a disable button that appears only when 2FA is enabled, ban/unban toggle with session invalidation warning, balance adjustment form with reason field, links to that user's filtered transactions and investments, and a full custom plans section for creating private investment plans assigned exclusively to that user.

### Investment Plans
Two-tab interface - Active and Deleted. Active tab shows all public investment plans with controls to star/feature, edit, toggle active status, and soft-delete. Deleted tab shows soft-deleted plans with restore and permanent delete options. Soft delete is blocked if the plan has active investor positions.

### Investments
Platform-wide investment history across all users. Filterable by status (ACTIVE, MATURED, CANCELLED) and searchable by user email. Each row links to the investor's profile. Plan names show `[Deleted]` label when the plan has been soft-deleted, preserving the historical record.

### Trading Pools
Full pool management - create new pools with assignment to specific users, view all pools with status badges, navigate to pool detail pages for investment management, value updates, profit distribution, and pool completion.

### Withdrawals
Pending withdrawal queue with approve/reject controls. Each withdrawal shows the user, amount, destination address, network, and submission timestamp. Rejected withdrawals require a reason. The queue auto-refreshes every 30 seconds.

### KYC
Review queue for pending identity verifications. Admin can view submitted documents (ID front, ID back, selfie) directly in the panel, then approve or reject with a typed rejection reason that is communicated to the user.

### Transactions
Platform-wide transaction ledger filterable by type and status. Full financial audit trail across all users and all transaction types.

### Support Tickets
Admin ticket dashboard showing open, in-progress, and resolved tickets. Admins can update status and add internal notes. Tickets sourced from both the contact form and the in-app ticket system are unified here.

---

## KYC Verification Workflow

KYC (Know Your Customer) verification is required for full platform access. The workflow is designed to be simple for users while giving admins everything they need to make a decision.

### User Submission Flow

1. User navigates to Settings → KYC section
2. Selects document type: Passport or National ID
3. Uploads front of document (file picker or camera capture)
4. Uploads back of document (required for National ID)
5. Takes a live selfie using the browser's `getUserMedia` camera API - no app required, works on mobile and desktop
6. Submits - status immediately changes to PENDING and admin is notified

### Admin Review Flow

1. Admin opens KYC panel - pending submissions listed with user email and submission timestamp
2. Admin clicks a submission to open the full review view
3. Document front, document back, and selfie displayed for comparison
4. Admin selects Approve or Reject
5. If rejecting, admin enters a rejection reason
6. Decision saved - user KYC status updated, notification sent to user

### Status States

```
NOT_SUBMITTED → PENDING → APPROVED
                       ↘ REJECTED → (user resubmits) → PENDING
```

### Document Storage

All KYC documents are stored on a Render Disk mounted to the backend service - a persistent volume that survives deployments. No third-party document storage service is required, keeping sensitive identity documents within the platform's own infrastructure.

---

## Wallet System

Every user on Cryter has exactly one wallet. The wallet is the source of truth for that user's available balance. All financial operations - investments, returns, bonuses, adjustments, withdrawals - flow through the wallet and are recorded in the transaction ledger.

### Transaction Types

| Type | Direction | Trigger |
|---|---|---|
| DEPOSIT | Credit | Admin confirms receipt of crypto deposit |
| WITHDRAWAL | Debit | User requests withdrawal, admin approves |
| INVESTMENT | Debit | User creates an investment or admin adds pool investment |
| RETURN | Credit | Investment matures, Celery task fires |
| ADJUSTMENT | Credit or Debit | Admin manually adjusts balance with reason |
| REFERRAL_BONUS | Credit | Referred user completes first investment |

### Wallet Flow

```
User Wallet
    │
    ├── DEPOSIT (+ credit)
    │     └── Admin confirms deposit → balance increases
    │
    ├── INVESTMENT (- debit)
    │     ├── User invests in plan → funds locked
    │     └── Admin adds pool investment → funds moved to pool
    │
    ├── RETURN (+ credit)
    │     ├── Celery task: plan matures → principal + profit returned
    │     └── Pool completion → principal + net profit returned
    │
    ├── REFERRAL_BONUS (+ credit)
    │     └── Referral fires on referred user's first investment
    │
    ├── ADJUSTMENT (± admin)
    │     └── Admin credits or debits with mandatory reason
    │
    └── WITHDRAWAL (- debit)
          └── User requests → admin approves → balance deducted
```

### Transaction Integrity

Every transaction records:
- Transaction type and status
- Amount
- Balance-after (wallet balance immediately after this transaction)
- Reason or metadata
- Timestamp

This means every user's transaction history is self-auditing - you can reconstruct their wallet balance at any point in time by replaying the transaction log.

---

## Investment Engine

### How Investment Plans Work

Investment plans are the core financial product. Each plan defines a fixed APY, a duration in days, and minimum/maximum investment amounts. When a user invests, funds are immediately debited from their wallet and an investment record is created. A Celery beat task runs daily and checks for investments whose maturity date has passed - when found, it credits the principal plus calculated profit back to the user's wallet and marks the investment as matured.

**Return calculation:**
```
Daily Rate  = APY / 100 / 365
Profit      = Amount × Daily Rate × Duration Days
Total Return = Amount + Profit
```

**Example:**
```
Investment:    $10,000
APY:           18.5%
Duration:      30 days

Daily Rate  = 18.5 / 100 / 365 = 0.0005068
Profit      = $10,000 × 0.0005068 × 30 = $152.05
Total Return = $10,152.05
```

### Investment Lifecycle

```
User selects plan
        │
        ▼
Validation (balance, min/max, email verified)
        │
        ▼
Wallet debited (INVESTMENT transaction created)
        │
        ▼
UserInvestment record created [status: ACTIVE]
        │
        ▼
Celery checks daily: maturity_date <= now?
        │
   YES  │
        ▼
Wallet credited (RETURN transaction created)
        │
        ▼
UserInvestment [status: MATURED]
        │
        ▼
Notification sent to user
```

### Public vs Private Plans

Public plans are visible to all authenticated users. Private plans are created by an admin for a specific user - they appear in that user's investment list but are invisible to everyone else. This enables admins to offer custom terms to high-value users without affecting the public offering.

### Plan Deletion Rules

A plan cannot be deleted while it has active investments - this protects investors currently in a running plan. Once all investments have matured or been cancelled, the plan can be soft-deleted (hidden from users and the active admin list). If the plan also has zero historical investment records, it can be permanently hard-deleted.

---

## Trading Pools

Trading pools represent a more flexible financial product where the admin actively manages the investment on behalf of users. Unlike fixed-APY plans, pool returns are determined by actual trading performance.

### Pool Lifecycle

```
Admin creates pool (assigned to user or public)
        │
        ▼
Pool status: PENDING
        │
        ▼
Admin adds user investment → wallet debited
        │
        ▼
Pool status: ACTIVE
        │
        ▼
Admin trades externally, updates pool value manually
        │
        ▼
Profit calculated: current_amount - initial_amount
        │
        ▼
Admin completes pool when trading period ends
        │
        ▼
Each investor receives: principal + (gross_profit × user_share_percentage)
        │
        ▼
Wallets credited, notifications sent, pool status: COMPLETED
```

### Fee Structure

```
Pool Final Value:        $29,800
Initial Investment:      $25,000
Gross Profit:            $4,800

Platform Fee (20%):      $960
User Net Profit (80%):   $3,840
Total Returned to User:  $28,840
```

Both the platform fee percentage and user share percentage are configurable per pool, allowing different terms for different users or trading strategies.

---

## Architecture

### System Overview

Cryter follows a clean separation between the API backend and the frontend application. The Django backend exposes a RESTful JSON API consumed exclusively by the Next.js frontend. There is no server-side rendering of HTML from Django - all rendering happens in the browser via React.

```
┌─────────────────────────────────────────────────────┐
│                    User / Browser                    │
└──────────────────────┬──────────────────────────────┘
                       │ HTTPS
                       ▼
┌─────────────────────────────────────────────────────┐
│              Next.js 14 Frontend (Render)            │
│   App Router · TypeScript · Tailwind v4 · TanStack  │
└──────────────────────┬──────────────────────────────┘
                       │ REST API (JSON)
                       │ X-Client-Key header on every request
                       ▼
┌─────────────────────────────────────────────────────┐
│            Django REST Framework Backend (Render)    │
│   JWT Auth · Middleware Stack · drf-spectacular     │
└────┬──────────────┬───────────────┬─────────────────┘
     │              │               │
     ▼              ▼               ▼
┌─────────┐  ┌──────────┐  ┌──────────────┐
│Postgres │  │  Redis   │  │  Render Disk │
│(primary │  │(cache +  │  │(KYC document │
│   DB)   │  │ sessions)│  │   storage)   │
└─────────┘  └──────────┘  └──────────────┘
                  │
                  ▼
          ┌──────────────┐
          │Celery Worker │
          │+ Beat Scheduler│
          └──────────────┘
```

### Backend App Structure

The Django backend is organized into focused apps, each owning its own models, serializers, views, and URLs:

| App | Responsibility |
|---|---|
| `users` | Registration, login, profile, password, 2FA |
| `wallets` | Wallet model, transactions, deposits, withdrawals |
| `investments` | Investment plans, user investments, trading pools |
| `kyc` | Document submission, admin review |
| `referrals` | Referral codes, tracking, bonus crediting |
| `notifications` | Typed notification creation and delivery |
| `market` | CoinGecko proxy, caching, portfolio value |
| `support` | Support tickets, admin management |
| `administration` | Admin-facing views, stats, platform-wide queries |
| `core` | Base models, middleware, exceptions, pagination, utils |

### Frontend Structure

The Next.js frontend uses the App Router with route groups to separate authenticated dashboard routes from public auth routes:

```
app/
├── (auth)/          → login, register, 2FA, verify-email, reset-password
├── (dashboard)/     → protected user-facing pages
│   ├── dashboard/
│   ├── wallet/
│   ├── investments/
│   ├── pools/
│   ├── market/
│   ├── referrals/
│   ├── notifications/
│   └── settings/
└── cp-portal/
    └── control/     → admin panel (middleware-protected)
        ├── users/
        ├── investments/
        ├── pools/
        ├── kyc/
        ├── withdrawals/
        ├── transactions/
        └── support/
```

### Data Flow

1. User action triggers a React event handler
2. Handler calls a mutation from a TanStack Query hook
3. Hook calls an API function in `lib/api/`
4. API function uses the axios client (with automatic JWT refresh interceptor)
5. Request arrives at Django with `Authorization: Bearer <token>` and `X-Client-Key` headers
6. Middleware stack validates both headers, checks IP ban status, applies rate limits
7. View processes request, returns standardized `ApiResponse` envelope
8. TanStack Query caches the response, updates UI reactively

---

## Tech Stack

### Backend

**Django 4.2 + Django REST Framework**
Chosen for its maturity, the quality of its ORM, and the richness of the DRF ecosystem. Django's batteries-included approach - migrations, admin, signals, management commands - accelerated development significantly. DRF's serializer system made it straightforward to build consistent, validated API responses across 40+ endpoints.

**PostgreSQL**
The production database. Chosen over SQLite for its support for UUID primary keys, JSON fields, proper indexing, and production reliability. Cryter uses composite indexes on frequently queried field combinations (status + maturity_date, user + status) to keep investment queries fast as data grows.

**Redis**
Used for two purposes: Django's cache backend (rate limiting, IP ban records, TOTP token storage) and Celery's message broker. Redis was the natural choice because it's already required for Celery, and using it for caching avoids introducing a second infrastructure dependency.

**Celery + Celery Beat**
Handles all asynchronous work: sending transactional emails via Resend, processing matured investments daily, and any future background tasks. Beat provides the cron-like scheduling for the daily maturity check. The maturity task is idempotent - running it multiple times on the same day produces the same result.

**Resend (via django-anymail)**
Transactional email provider. Used for email verification, password reset, and any future user-facing email. Anymail's abstraction layer means the email backend can be swapped without changing any application code.

**drf-spectacular**
Generates a fully annotated OpenAPI 3.0 schema from the codebase. Every endpoint has tags, summaries, request bodies, and response types documented. This makes the API self-documenting and ready for third-party integration.

### Frontend

**Next.js 14 with App Router**
The App Router's route group system makes it clean to separate auth routes, user dashboard routes, and admin routes with different layouts and middleware behavior. Server Components are used for static layout elements; Client Components handle all interactive state.

**TypeScript**
Strict typing across the entire frontend. Every API response, every component prop, every hook return type is explicitly typed. This caught dozens of integration bugs at compile time rather than in the browser.

**Tailwind CSS v4**
Used for all styling. The utility-first approach kept the codebase consistent without a component library dependency. Custom design tokens define the dark sapphire palette, glass-morphism effects, and animation utilities shared across both the user dashboard and admin panel.

**TanStack Query (React Query)**
Handles all server state - fetching, caching, background refetching, and mutation side effects. Every API call goes through a typed hook (`useAdminPlans`, `useUserInvestments`, etc.) that manages loading, error, and success states. Mutations automatically invalidate relevant query keys to keep the UI in sync without manual refresh.

**Sonner**
Toast notification library for mutation feedback. Every admin action (approve, reject, update, delete) produces a typed toast - success in green, error in red with the API's error message surfaced directly.

### Infrastructure

**Render**
All services deployed on Render: backend as a Web Service, frontend as a Static Site, PostgreSQL as a managed database, Redis as a managed cache, and a Render Disk for KYC document storage. The `render.yaml` blueprint defines all services declaratively.

---

## Security Model

### Authentication

Cryter uses JWT (JSON Web Tokens) with a short-lived access token (15 minutes) and a longer-lived refresh token (7 days). The access token is stored in memory - never in localStorage - which protects against XSS attacks. The refresh token is stored in an httpOnly cookie, protecting it from JavaScript access.

When an API request fails with 401, the axios interceptor automatically attempts a silent refresh using the refresh token. If the refresh succeeds, the original request is retried transparently. If it fails, the user is logged out.

### Two-Factor Authentication

TOTP (Time-based One-Time Password) 2FA is implemented using the `pyotp` library. Setup flow:

1. Backend generates a TOTP secret and returns a QR code (base64 PNG)
2. User scans with Google Authenticator or compatible app
3. User confirms setup by entering a valid TOTP code
4. Secret stored on user record, `is_2fa_enabled` set to True

On login with 2FA enabled, the backend returns `{requires_2fa: true, temp_token}` instead of real JWTs. The frontend redirects to a 2FA verification page where the user enters their 6-digit code. The temp token is Redis-backed with a 5-minute TTL.

Admins can disable 2FA for locked-out users directly from the user detail page, which clears the TOTP secret and resets the flag.

### API Protection

**ClientKeyMiddleware**
Every API request must include an `X-Client-Key` header matching a secret value shared between the backend and frontend environment variables. Requests without this header are rejected with 403. This prevents unauthorized API access even if someone discovers the API base URL.

**BanMiddleware**
Every incoming request's IP address is checked against a Redis-backed ban list. Banned IPs receive a 403 response immediately, before any view logic executes. Bans are set with configurable TTLs and can be applied manually or automatically (e.g., on honeypot route access).

**Honeypot Routes**
Common scanner targets (`.env`, `wp-admin`, `phpmyadmin`, etc.) are registered as honeypot routes. Any request to these paths results in an immediate IP ban and a Discord webhook alert. Legitimate users never hit these routes.

**Rate Limiting**
DRF throttling is applied at multiple levels:
- Registration: 5 per hour per IP
- Login: 5 attempts per 30-minute window before lockout
- Investment creation: 10 per hour per user
- Market data proxy: 50 requests per minute per IP

**Role-Based Access Control**
- Unauthenticated users can only access auth endpoints
- Authenticated users access their own data only - all queries are filtered by `user=request.user`
- Admin endpoints require `IsAdminUser` permission - `is_staff=True` on the user record
- The admin panel route is protected at the Next.js middleware level using the `cryter_is_staff` cookie

### Password Security

Passwords are hashed using Django's default PBKDF2 with SHA256, with a salt. Plain-text passwords are never stored. Password reset tokens are single-use, time-limited (1 hour), and stored hashed. Django's password validators enforce minimum length and complexity.

### Financial Controls

- Investment creation validates wallet balance before deducting - insufficient funds returns a typed error, no partial deduction occurs
- All wallet operations use Django's `select_for_update()` to prevent race conditions on concurrent requests
- Every financial operation is wrapped in a database transaction - either the entire operation succeeds or nothing changes
- Balance-after is recorded on every transaction, enabling full audit reconstruction

### Audit Tracking

- Every transaction records who initiated it, when, and why
- Admin actions (KYC decisions, balance adjustments, withdrawal approvals) are logged with the acting admin's identity
- Plan deletions and restorations are logged at the INFO level
- Hard deletes are logged at WARNING level
- Discord webhook fires on security events (honeypot hits, IP bans)

---

## User Journey

### Registration

1. User navigates to `/register`
2. Enters first name, last name, email, and password (with confirm)
3. Optionally enters a referral code (or `?ref=CODE` in the URL pre-fills it)
4. On submit, backend validates email uniqueness and password strength
5. User account created, wallet created, referral record created if applicable
6. Verification email sent via Celery + Resend
7. User redirected to dashboard with an email verification banner

### Email Verification

1. User receives email with a time-limited verification link
2. Clicking the link hits the `/verify-email?token=...` page
3. Frontend sends token to backend, which validates and marks email as verified
4. Verification banner disappears, full platform access granted

### Login

1. User enters email and password
2. If 2FA is not enabled: JWT tokens returned, user redirected to dashboard
3. If 2FA is enabled: backend returns `{requires_2fa: true, temp_token}`, user redirected to `/2fa`
4. User enters 6-digit TOTP code
5. Backend validates against stored secret with ±30 second clock drift tolerance
6. JWT tokens returned, user redirected to dashboard

### KYC Submission

1. User navigates to Settings → KYC
2. Selects document type
3. Uploads document front (camera or file)
4. Uploads document back (if National ID)
5. Takes live selfie via browser camera API
6. Submits - status changes to PENDING
7. Admin reviews and approves or rejects
8. User receives notification with decision

### Wallet Funding

1. User navigates to Wallet → Deposit
2. Selects cryptocurrency network
3. Copies wallet address or scans QR code
4. Sends funds externally
5. Admin confirms receipt, creates DEPOSIT transaction
6. Balance updated, notification sent to user

### Investment Creation

1. User browses investment plans on `/investments`
2. Clicks a plan to see full details and example returns
3. Enters investment amount (validated against min/max)
4. Confirms - funds debited immediately, investment record created
5. Investment appears in active investments with live progress bar and maturity countdown
6. On maturity date, Celery task credits principal plus profit, sends notification

### Earnings Tracking

1. User views active investments on dashboard home and investments page
2. Each investment shows: amount invested, expected return, progress bar, days remaining
3. After maturity: investment shows MATURED status, RETURN transaction visible in wallet history
4. Referral earnings visible on dedicated referrals page

### Withdrawal Request

1. User navigates to Wallet → Withdraw
2. Enters amount, destination cryptocurrency address, and network
3. Submits request - status: PENDING
4. Admin sees request in withdrawal queue
5. Admin reviews and approves or rejects with reason
6. If approved: wallet debited, WITHDRAWAL transaction created, notification sent
7. If rejected: no balance change, rejection reason sent in notification

---

## Admin Journey

### User Management

Admin navigates to `/cp-portal/control/users`. The table shows all users with email, full name, balance, active investments, KYC status, and verification state. Admin can:

- Search by email
- Filter by active/banned status, KYC state, staff flag
- Click any user to open their full detail page
- Ban or unban users (token blacklisting fires immediately)
- Adjust wallet balance with a mandatory reason
- Override KYC status directly
- Disable 2FA for locked-out users
- Create private investment plans assigned exclusively to that user

### KYC Review

Admin opens the KYC panel. Pending submissions are listed. Admin clicks a submission to view the uploaded documents - ID front, ID back, and selfie are displayed side by side for comparison. Admin selects Approve or Reject. If rejecting, a text field captures the reason which is delivered to the user via notification.

### Deposit Monitoring

Deposits in Cryter are manually confirmed by admin (a common model for crypto platforms without automated blockchain monitoring). When a user reports a deposit, admin navigates to their profile, uses the balance adjustment tool to credit the amount with type DEPOSIT and the transaction hash as the reason.

### Withdrawal Approval

Admin navigates to `/cp-portal/control/withdrawals`. Pending requests are listed with user email, amount, destination address, and network. Admin reviews each request, confirms the details, and approves or rejects. Approval debits the wallet and notifies the user. The queue auto-refreshes every 30 seconds.

### Investment Plan Management

Admin creates, edits, features, activates, deactivates, and deletes public investment plans from `/cp-portal/control/plans`. The Active/Deleted tab system keeps the main view clean. Private plans are managed from individual user profiles, not the global plans page.

### Financial Oversight

Admin has full visibility via:
- Platform stats on the overview dashboard (refreshed every 60 seconds)
- Platform-wide investments filterable by status and user
- Platform-wide transactions filterable by type and status
- Individual user financial summaries on each user's detail page

---

## Flow Diagrams

### User Registration & Verification Flow

```
Register
    │
    ├── Email exists? → 400 error
    ├── Password weak? → 400 error
    ├── Referral code valid? → link referred_by
    │
    ▼
User created + Wallet created + Referral record created
    │
    ▼
Verification email queued (Celery)
    │
    ▼
User lands on dashboard [unverified]
    │
    ▼
User clicks email link → token validated → is_email_verified = True
    │
    ▼
Full platform access
```

### Wallet Flow Diagram

```
                    ┌─────────────┐
                    │  User Wallet │
                    │  Balance: $X │
                    └──────┬──────┘
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
    DEPOSIT (+)      INVESTMENT (-)    WITHDRAWAL (-)
    Admin confirms   User invests      User requests
    receipt          in plan/pool      → Admin approves
          │                │                │
          │           Maturity date         │
          │           reached               │
          │                │                │
          │                ▼                │
          │         RETURN (+)              │
          │         Principal +             │
          │         Profit credited         │
          │                                 │
          └────────────────┬────────────────┘
                           │
                    REFERRAL_BONUS (+)
                    First investment by
                    referred user
                           │
                    ADJUSTMENT (±)
                    Admin manual
                    correction
```

### Investment Lifecycle Diagram

```
Plan Created (admin)
        │
        ▼
Plan Active (visible to users)
        │
        ▼
User Invests → Wallet debited → UserInvestment [ACTIVE]
        │
        ▼
Celery Beat runs daily at midnight UTC
        │
        ├── maturity_date > now → skip
        │
        └── maturity_date <= now
                │
                ▼
        Calculate return
        Debit: Amount × (APY/100/365) × Duration
                │
                ▼
        Credit wallet (RETURN transaction)
                │
                ▼
        UserInvestment [MATURED]
                │
                ▼
        Notification sent to user
```

### KYC Approval Workflow

```
User submits documents
        │
        ▼
KYC record created [PENDING]
        │
        ▼
Admin opens KYC review panel
        │
        ├── Views: ID front, ID back, selfie
        │
        ├── APPROVE
        │       │
        │       ▼
        │   kyc_status = APPROVED
        │   Notification sent: "Your identity has been verified"
        │
        └── REJECT (+ reason)
                │
                ▼
            kyc_status = REJECTED
            Notification sent with rejection reason
                │
                ▼
            User resubmits → PENDING again
```

### Admin Withdrawal Review Workflow

```
User submits withdrawal request
        │
        ▼
Transaction created [PENDING]
Wallet balance unchanged
        │
        ▼
Admin opens withdrawal queue
        │
        ├── Reviews: user, amount, address, network
        │
        ├── APPROVE
        │       │
        │       ▼
        │   Wallet debited
        │   Transaction [COMPLETED]
        │   Notification: "Withdrawal approved"
        │
        └── REJECT (+ reason)
                │
                ▼
            Wallet unchanged
            Transaction [CANCELLED]
            Notification: "Withdrawal rejected - [reason]"
```

---

## Problems Solved During Development

### Silent JWT Refresh Without UX Disruption

**Problem:** Access tokens expire every 15 minutes. Requiring users to re-login this frequently is unacceptable. But storing long-lived tokens insecurely creates security risks.

**Solution:** An axios response interceptor watches for 401 responses. When one occurs, it automatically calls the refresh endpoint using the httpOnly cookie, updates the in-memory access token, and retries the original request - all transparently. From the user's perspective, they never see a login prompt unless their refresh token has also expired.

### Admin Panel Reload Login Loop

**Problem:** The admin panel was logging out on page reload. The access token is stored in memory and clears on page reload. The silent refresh requires the `X-Client-Key` header, which was missing because `NEXT_PUBLIC_CLIENT_KEY` was not set in the frontend environment.

**Solution:** Added `X-Client-Key` as a default header on the axios client and documented the environment variable requirement. The silent refresh now works correctly on admin panel reload.

### DateField vs DateTimeField Serializer Mismatch

**Problem:** The admin investments list endpoint was returning a 500 error because `AdminInvestmentSerializer` declared `start_date` and `maturity_date` as `serializers.DateField()` while the model uses `DateTimeField`. Django REST Framework refused to serialize datetime values into a date-only field.

**Solution:** Changed both fields to `serializers.DateTimeField()`. A one-character fix that unblocked the entire investments admin panel.

### Python 3.14 Incompatibility

**Problem:** Django's template context `__copy__` method broke on Python 3.14 due to changes in the internal `copy` module protocol, causing 500 errors across the entire application.

**Solution:** Identified the root cause from the traceback, downgraded the development environment to Python 3.12 (the current Django-recommended version), and documented the venv recreation steps.

### ModelSerializer vs Plain Serializer Admin Investments

**Problem:** The `AdminInvestmentListView` was manually building a list of dicts and passing it to a `ModelSerializer`. `ModelSerializer` expects model instances, not dicts - the `user_email`, `user_id`, and `plan_name` fields all resolved to `None`, appearing as blank or `undefined` in the frontend.

**Solution:** Removed the manual dict-building loop entirely and passed the queryset page directly to the serializer. Added `select_related("user", "plan")` to the queryset to avoid N+1 queries.

### Referral Bonus Never Firing

**Problem:** `process_referral_bonus()` in the investments service was looking for a `Referral` record with `status=PENDING` for the referring user. These records never existed because `create_referral_record()` was never called - the registration serializer set `referred_by` on the user but never created the `Referral` table row.

**Solution:** Added a `try/except` wrapped call to `create_referral_record()` in `RegisterSerializer.create()` after user creation, inside a block that never raises to the caller. Referral records now exist and bonuses fire correctly on first investment.

### Soft Delete Showing [Deleted Plan] on Active Investments

**Problem:** When a plan was soft-deleted, existing investments were showing `[Deleted Plan]` in the admin investments panel even for investments that were active at the time of deletion. The `get_plan_name` serializer method's `except Exception` block was catching `AttributeError` on `obj.plan.is_deleted` because the migration adding `is_deleted` to `InvestmentPlan` had not been run.

**Solution:** Running `makemigrations investments && migrate` resolved the issue. The `is_deleted` field now exists on the database table, the property resolves correctly, and only plans that are genuinely soft-deleted show the `[Deleted]` label.

### Trading Pool Double Notification

**Problem:** The pool creation frontend was calling `api.post('/notifications/send/')` after pool creation, but the backend `AdminTradingPoolListCreateView` already called `create_notification()` internally. Users received two identical notifications. Additionally, `/notifications/send/` does not exist as an endpoint and was silently 404-ing.

**Solution:** Removed the frontend notification call entirely. Backend notification handling is authoritative and requires no duplication on the frontend.

---

## Implementation Highlights

### Celery Beat Maturity Task

The investment maturity processor is the most financially critical piece of background logic in the platform. It runs daily via Celery Beat and processes all `ACTIVE` investments where `maturity_date <= now`. For each:

1. Calculates profit using the plan's APY and the actual investment duration
2. Credits `amount + profit` to the user's wallet as a `RETURN` transaction
3. Sets `UserInvestment.status = MATURED` and `matured_at = now()`
4. Sends a typed notification to the user

The task is idempotent - the `ACTIVE` status filter ensures already-matured investments are never double-processed.

### Custom Middleware Stack

The Django middleware stack processes every request through multiple validation layers before any view logic runs:

1. **ClientKeyMiddleware** - validates the `X-Client-Key` header
2. **BanMiddleware** - checks IP against Redis ban list
3. **HoneypotMiddleware** - checks path against known scanner targets

This means even a malformed or malicious request that somehow gets past CORS never reaches application code.

### TanStack Query Cache Architecture

Every API resource has a typed query key factory:

```typescript
adminKeys = {
  stats:        ["admin", "stats"],
  users:        (f) => ["admin", "users", f],
  user:         (id) => ["admin", "user", id],
  plans:        ["admin", "plans"],
  deletedPlans: ["admin", "plans", "deleted"],
  customPlans:  (userId) => ["admin", "custom-plans", userId],
  // ...
}
```

Mutations invalidate precisely the right keys - creating a custom plan for a user invalidates `adminKeys.customPlans(userId)` but not the global plans list. This keeps the UI reactive without over-fetching.

### Admin Panel Middleware Protection

The admin panel route (`/cp-portal`) is protected at two levels:
1. **Next.js middleware** - checks for `cryter_is_staff` cookie on every request to `/cp-portal/**`. Missing cookie redirects to login immediately, before any page renders.
2. **Backend permissions** - all admin API endpoints use `IsAdminUser` which checks `is_staff` on the JWT payload. Even if someone forged the cookie, they cannot call any admin API endpoint without a legitimate staff JWT.

### Wallet Transaction Atomicity

All wallet operations use Django's `atomic()` context manager and `select_for_update()` on the wallet row. This means:

- No two concurrent requests can simultaneously read and modify the same wallet balance
- If any part of the transaction fails (balance check, transaction creation, investment creation), the entire database operation rolls back
- The balance-after field on each transaction is always accurate because it's calculated inside the same atomic block

---

## Production-Level Features

**Environment Split**
Separate `dev`, `prod`, and `base` settings modules. Development uses the console email backend, `DEBUG=True`, and local Redis. Production loads from `.env.production` with Resend email, `DEBUG=False`, security headers enabled, and Render-managed Postgres and Redis.

**OpenAPI Documentation**
drf-spectacular generates a complete OpenAPI 3.0 schema from the live codebase. Every endpoint is tagged, summarized, and has documented request/response types. The schema is available at `/api/schema/` and a Swagger UI at `/api/docs/`.

**Render Blueprint**
A `render.yaml` file declaratively defines all Render services - the Django web service, the Celery worker, the Next.js static site, Postgres, Redis, and the Render Disk for KYC storage. A single `render deploy` command provisions the entire infrastructure.

**Discord Security Alerts**
A Discord webhook is called on honeypot route access and IP ban events. Admins receive real-time alerts when scanners or malicious actors probe the platform.

**Sentry Integration**
Sentry DSN is configurable via environment variable. In production, all unhandled exceptions are captured with full stack traces and sent to Sentry for monitoring.

**Transactional Email**
All user-facing emails (verification, password reset) are delivered via Resend with proper `From` addressing. Email delivery is asynchronous via Celery - the API response is never delayed by email sending.

**Pagination**
All list endpoints use `StandardResultsPagination` which returns `count`, `next`, `previous`, and `results`. The frontend uses the count for UI labels and the next/previous for pagination controls.

---

## Scalability Considerations

**Database Indexing**
Compound indexes on the most-queried field combinations:
- `(status, maturity_date)` on `UserInvestment` - the Celery maturity task queries exactly this
- `(user, status)` on `UserInvestment` - the user investments list queries exactly this
- `(status, assigned_user)` on `TradingPool` - the admin pool filter queries exactly this
- `is_deleted` and `is_active` on `InvestmentPlan` - indexed separately for filtered list queries

**Redis Caching**
Market data responses from CoinGecko are cached in Redis with appropriate TTLs. High-frequency reads (top coins, trending) never hit the external API more than necessary. The cache layer also absorbs the majority of traffic on the market endpoints.

**Celery Worker Scaling**
The Celery worker is a separate Render service. If task processing becomes a bottleneck, the worker's `concurrency` setting can be increased, or multiple worker instances can be deployed. The task queue (Redis) is already separate from the database, so workers can scale independently.

**Stateless API**
The Django API is stateless - all state lives in the database, Redis, or client-side storage. This means the web service can be horizontally scaled by adding more instances behind Render's load balancer without any session affinity requirements.

**Async Email Delivery**
Email is never sent synchronously in a request/response cycle. All email tasks are queued to Celery. This means email delivery latency never affects API response time, and email failures never cause API errors.

**Soft Delete Pattern**
Financial records are never hard-deleted unless absolutely safe to do so. Investment plans with history are soft-deleted. Investments themselves are never deleted. This preserves the audit trail and prevents cascading foreign key issues as the platform grows.

---

## Future Expansion Opportunities

**Automated Deposit Detection**
Current deposits are manually confirmed by admin. The natural next step is a blockchain monitoring service that watches deposit wallet addresses and automatically credits user accounts when incoming transactions are confirmed. This could be implemented as a Celery task polling a blockchain API (Blockcypher, Moralis, etc.) or via webhook.

**Tax Reporting**
The transaction ledger and investment records contain all the data needed to generate 1099-MISC or 1099-B forms for US users. A reporting module could query all RETURN and REFERRAL_BONUS transactions per user per tax year and produce downloadable PDFs.

**Tiered KYC**
The current KYC system has a single verification level. A tiered system (Basic: email verified, Enhanced: ID verified, Premium: video call verified) would allow different withdrawal limits and investment caps at each tier - a common compliance pattern for fintech platforms.

**Mobile Application**
The Django backend is already a pure JSON API with no web-specific assumptions. A React Native or Expo mobile app could consume the same API with minimal backend changes. The 2FA flow, KYC document upload, and live selfie capture are all patterns that translate naturally to mobile.

**Automated Pool Trading**
The current trading pool system requires manual value updates from admin. An exchange API integration (Binance, Bybit, Kraken) could automatically sync pool values from real trading positions, calculate profit/loss in real time, and give users live visibility into their pool performance.

**Referral Tiers**
The current referral system pays a flat bonus on first investment. A tiered referral system - paying recurring commissions on each subsequent investment, or different rates for different KYC tiers - would incentivize more aggressive user acquisition.

**White-Label Deployment**
The codebase is structured cleanly enough that the platform name, color palette, fee structure, and supported cryptocurrencies could all be made configurable. This opens the door to white-labeling Cryter for other operators who want a managed investment platform without building from scratch.


---



---



<br />

<div align="center">

**Built solo by [@whoknowsasaint](https://github.com/whoknowsasaint)**

*30+ development phases · Full-stack · Production-ready*

[![GitHub](https://img.shields.io/badge/GitHub-whoknowsasaint-181717?style=for-the-badge&logo=github)](https://github.com/whoknowsasaint)
[![Twitter](https://img.shields.io/badge/Twitter-@saintjs__-1DA1F2?style=for-the-badge&logo=twitter)](https://twitter.com/saintjs_)

</div>
