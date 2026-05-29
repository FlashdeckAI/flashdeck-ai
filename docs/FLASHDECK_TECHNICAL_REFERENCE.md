# FlashDeck.AI — Technical & Business Logic Reference

> **Document status:** Living document — updated as each module is designed and built  
> **Version:** 1.1 — 29 May 2026  
> **Author:** Zulfi (Cutlass Group) with Manus AI  
> **Audience:** Development team, technical stakeholders, Cutlass Group  
> **Repository:** github.com/FlashdeckAI/flashdeck-ai

---

## Contents

1. [Overview & Architecture](#1-overview--architecture)
2. [User Types & Roles](#2-user-types--roles)
3. [Authentication & Registration](#3-authentication--registration)
4. [Parent & Family Accounts](#4-parent--family-accounts)
5. [School Pilot Lifecycle](#5-school-pilot-lifecycle)
6. [Teacher & School Admin Onboarding](#6-teacher--school-admin-onboarding)
7. [Dashboard Logic](#7-dashboard-logic)
8. [Generate Deck Flow](#8-generate-deck-flow)
9. [Study Interface & Spaced Repetition](#9-study-interface--spaced-repetition)
10. [Monetisation & Subscription Logic](#10-monetisation--subscription-logic)
11. [Data Retention & Compliance](#11-data-retention--compliance)
12. [Admin Panel (Super Admin)](#12-admin-panel-super-admin)
13. [Open Questions & Decisions Log](#13-open-questions--decisions-log)

---

## 1. Overview & Architecture

FlashDeck.AI is an AI-powered flashcard revision platform targeting GCSE, IGCSE, and A Level students across the United Kingdom, Pakistan, Nigeria, and the Middle East. The platform uses the Anthropic Claude API to generate exam-board-aligned flashcards on demand, with a shared Topic Bank that caches generated content to reduce API costs over time.

### Technology Stack

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | HTML / CSS / JavaScript (static) → React (post-MVP) | Marketing site and application UI |
| Backend / Auth | Supabase | Authentication, database, row-level security |
| AI Generation | Anthropic Claude (Haiku model) | Flashcard generation |
| Payments | Stripe | Individual, family, and school subscriptions |
| Hosting | Vercel | Static site deployment, auto-deploy from GitHub |
| Forms | Formspree | School enquiry and general contact forms |
| Email (transactional) | Resend (domain: flashdeck.ai — verified 15 May 2026) | Verification emails, invite links, reminders |
| Repository | GitHub (FlashdeckAI/flashdeck-ai) | Version control, documentation |

### Design Principles

The platform is built around three non-negotiable principles:

**Student-first, parent-managed.** The study experience belongs entirely to the student. Billing, consent, and account management belong to the parent or guardian. These two experiences are kept completely separate in the UI.

**Invite-only for elevated roles.** Teachers and school admins are never self-registered. They are onboarded only after a school pilot has been formally approved by the FlashDeck admin. This protects student data and ensures every elevated account is verified.

**Cache before generate.** Every AI-generated flashcard is stored in a shared, anonymised Topic Bank. Before calling the Claude API, the system checks whether sufficient cards already exist for the requested board, subject, level, and topic. This reduces API costs and improves response times as the platform scales.

---

## 2. User Types & Roles

FlashDeck has six distinct user types. Each has a different registration path, a different set of permissions, and a different UI experience.

| User Type | Registration Path | Who Pays | Dashboard |
|---|---|---|---|
| **Student (individual)** | Self-register via signup.html | Self (if 18+) or parent via payment link | Student study dashboard |
| **Child sub-account** | Created by parent during parent registration | Parent's subscription covers it | Student study dashboard (same as individual) |
| **Parent / Guardian** | Self-register via signup.html (parent flow) | Themselves | Parent dashboard (children's progress + billing) |
| **Teacher** | Invite-only — email invite after school pilot approved | School licence | Teacher dashboard (class management, shared decks) |
| **School Admin** | Invite-only — email invite after school pilot approved | School licence | School admin dashboard (school-wide oversight) |
| **Super Admin** | Internal only (Zulfi) | N/A | Full admin panel |

### Permission Matrix

| Action | Student | Child | Parent | Teacher | School Admin | Super Admin |
|---|---|---|---|---|---|---|
| Generate flashcards | ✅ (trial/premium) | ✅ (parent plan) | ✅ | ✅ | ✅ | ✅ |
| View grade prediction | ✅ (trial/premium) | ✅ (parent plan) | ✅ | ✅ | ✅ | ✅ |
| Study own decks | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| View own progress | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| View child's progress | ✗ | ✗ | ✅ | ✗ | ✗ | ✅ |
| Manage class | ✗ | ✗ | ✗ | ✅ | ✅ | ✅ |
| Create shared decks | ✗ | ✗ | ✗ | ✅ | ✅ | ✅ |
| View school-wide data | ✗ | ✗ | ✗ | ✗ | ✅ | ✅ |
| Approve pilot applications | ✗ | ✗ | ✗ | ✗ | ✗ | ✅ |
| Manage subscriptions | ✗ | ✗ | ✅ (own) | ✗ | ✗ | ✅ |
| Export PDF | 🔒 Premium | 🔒 Premium | 🔒 Premium | ✅ | ✅ | ✅ |
| Access admin panel | ✗ | ✗ | ✗ | ✗ | ✗ | ✅ |

---

## 3. Authentication & Registration

### 3.1 Email Verification

Email verification is **mandatory for all account types** without exception. The rationale is threefold: it provides a verified contact point for a platform handling minors' data; it prevents bulk fake registrations that would inflate Claude API costs; and it satisfies the expectation of any school DPO reviewing the platform's data practices.

**Verification flow:**
1. User completes registration form and submits
2. Supabase sends a verification email automatically (template configured in Supabase dashboard)
3. User is shown a "Check your inbox" holding screen — they cannot access the dashboard until verified
4. User clicks the verification link in the email → redirected to dashboard
5. If the user attempts to log in before verifying, they see a clear message: *"Please verify your email address first. [Resend verification email]"*
6. Verification links expire after **24 hours**. After expiry, the user must request a new link.

### 3.2 The Sign-Up Page — Who Are You?

The sign-up page (`signup.html`) presents three paths at the top before any form fields are shown:

**Path A — Student:** I am revising for GCSE, IGCSE or A Level  
**Path B — Parent / Guardian:** I am setting up an account for my child, or paying for one  
**Path C — Teacher / School:** I represent a school *(this option does not show a registration form — it redirects to `/schools.html`)*

This keeps the self-registration path clean and student/parent-only. Teachers and school admins never encounter the public sign-up form.

### 3.3 Student Registration Flow

The student registration form collects the following fields:

| Field | Type | Validation | Notes |
|---|---|---|---|
| First name | Text | Required, 2–50 chars | |
| Last name | Text | Required, 2–50 chars | |
| Email address | Email | Required, RFC 5322 regex | Must be unique |
| Password | Password | Min 8 chars, strength meter shown | |
| Confirm password | Password | Must match password field | |
| Exam board | Selector | Required — AQA / Edexcel / Cambridge / OCR | |
| Exam level | Selector | Required — GCSE / IGCSE / A Level | |
| Year group | Selector | Required — Year 10 / Year 11 / Year 12 / Year 13 / Other | |
| Terms & Privacy | Checkbox | Required | Links to /terms.html and /privacy.html |
| Age confirmation | Checkbox | Required | "I confirm I am 13 years of age or older" |

On successful submission: account created in Supabase, verification email sent via Resend, user shown "Check your inbox" screen. Account role set to `student`, subscription tier set to `trial` with a 14-day expiry timestamp stored in `users.trial_expires_at`.

**Post-verification routing:** Student lands on `/dashboard.html`.

### 3.4 Parent Registration Flow

The parent registration form is a multi-step flow modelled on IXL's approach, adapted for FlashDeck's per-child exam configuration.

**Step 1 — Parent account details:**

| Field | Type | Validation |
|---|---|---|
| First name | Text | Required |
| Last name | Text | Required |
| Email address | Email | Required, unique |
| Password | Password | Min 8 chars |
| Confirm password | Password | Must match |
| Terms & Privacy | Checkbox | Required |

**Step 2 — How many children?**

A stepper control (−/+) allowing 1 to 4 children. Pricing updates in real time as the count changes (see Section 10 for pricing). Each additional child beyond the first adds £3/month.

**Step 3 — Child profile(s):**

For each child, the parent fills in:

| Field | Type | Notes |
|---|---|---|
| Child's first name | Text | Used for display only — no surname required |
| Year group | Selector | Year 9 / Year 10 / Year 11 / Year 12 / Year 13 |
| Exam board | Selector | AQA / Edexcel / Cambridge / OCR |
| Exam level | Selector | GCSE / IGCSE / A Level |
| Child's login email | Email | Can be the child's own email or a sub-address (e.g. child@family.com) |
| Child's password | Password | Parent sets this; child can change it later |

Each child is created as a `child_account` in Supabase, linked to the parent's `user_id` via a `parent_id` foreign key.

**Step 4 — Plan selection & payment:**

Parent selects monthly or annual billing, reviews the total, and enters payment details via Stripe Checkout. On successful payment, all child accounts are immediately activated at Premium tier.

**Post-verification routing:** Parent lands on `/parent-dashboard.html`. Each child's account is active and ready to use.

### 3.5 Login Flow

The login page (`login.html`) handles all user types with a single form. Supabase returns the user's role on successful authentication, and the front end routes accordingly:

| Role | Post-login destination |
|---|---|
| `student` | `/dashboard.html` |
| `child` | `/dashboard.html` |
| `parent` | `/parent-dashboard.html` |
| `teacher` | `/teacher-dashboard.html` |
| `school_admin` | `/school-admin.html` |
| `super_admin` | `/admin.html` |

**Login form fields:**
- Email address (with RFC 5322 validation)
- Password (with show/hide toggle)
- "Keep me logged in for 30 days" checkbox (sets Supabase session duration)
- "Forgot password?" link → `/forgot-password.html`
- Google OAuth button → Supabase Google provider

**Error states:**
- Invalid credentials: *"The email or password you entered is incorrect."* (deliberately vague — do not confirm whether the email exists)
- Unverified email: *"Please verify your email address. [Resend verification email]"*
- Trial expired: *"Your free trial has ended. [Upgrade to Premium]"*
- Account locked (future): *"Your account has been temporarily locked. Please contact support."*

### 3.6 Forgot Password Flow

**Page:** `/forgot-password.html`

1. User enters their email address
2. If the email exists in Supabase, a password reset link is sent (Supabase handles this)
3. The page always shows the same confirmation message regardless of whether the email exists: *"If an account exists for that address, you'll receive a reset link within a few minutes."* (prevents email enumeration)
4. Reset link expires after **1 hour**
5. User clicks link → `index.html` detects `type=recovery` in the URL hash → immediately redirects to `/reset-password.html` with the full token intact

**Page:** `/reset-password.html`

1. URL contains a Supabase reset token (handled automatically)
2. User enters new password + confirm password
3. On success: password updated, user redirected to `/login.html` with a success message: *"Your password has been updated. Please log in."*

### 3.7 Google OAuth

Google OAuth is handled via Supabase's Google provider. On first sign-in with Google, Supabase creates a new user. The platform must then prompt the user to complete their profile (exam board, level, year group) before they can access the dashboard — this is a one-time "complete your profile" step shown immediately after the first Google sign-in.

For returning users, Google OAuth bypasses the email/password form entirely and routes directly to the appropriate dashboard based on their stored role.

---

## 4. Parent & Family Accounts

### 4.1 Parent Dashboard

The parent dashboard (`/parent-dashboard.html`) is entirely separate from the student study dashboard. It shows:

- A card for each child: name, year group, last active, current streak, overall mastery %
- Quick link to each child's detailed progress view (read-only — parent cannot modify study data)
- Subscription management: current plan, next billing date, add/remove children, upgrade/downgrade
- Billing history (Stripe Customer Portal link)
- Account settings: parent's own name, email, password

### 4.2 Child Account Experience

A child sub-account has an identical study experience to an individual student account. The child logs in with their own email and password, sees only their own dashboard, and has no visibility of siblings' data or the parent's billing information. The only difference is that the "Upgrade to Premium" prompt is replaced with *"Ask a parent to upgrade your account"* — because the child's subscription is managed by the parent.

### 4.3 Family Pricing Model

| Plan | Monthly | Annual | Children covered |
|---|---|---|---|
| Individual Premium | £7/month | £70/year (save £14) | 1 student |
| Family — 2 children | £10/month | £100/year (save £20) | 2 children |
| Family — 3 children | £13/month | £130/year (save £26) | 3 children |
| Family — 4 children | £16/month | £160/year (save £32) | 4 children |
| Additional child | +£3/month | +£30/year | Add any time |

> **Note:** These prices are indicative and subject to commercial review. The incremental £3/child model is borrowed from IXL's proven pricing structure.

### 4.4 Individual Student Upgrade Path (Option C)

For students who registered individually (not under a parent account), the upgrade path works as follows:

1. Student sees trial expiry prompt: *"Your free trial has ended — upgrade to keep your progress and continue revising."*
2. Student clicks "Upgrade" → sees two options:
   - **Pay myself** (for students aged 18+ or with their own payment method)
   - **Ask a parent to pay** → student enters parent's email address → parent receives a payment link email → parent clicks link → Stripe Checkout → on payment, student's account upgrades automatically
4. The "Ask a parent" email contains a clear explanation of what they're paying for and a link to the Privacy Policy

---

## 5. School Pilot Lifecycle

### 5.1 Overview

Schools do not self-register. The entire school onboarding process is initiated by a school submitting an interest form on `/schools.html`, reviewed by the FlashDeck super admin (Zulfi), and activated manually. This ensures every school account is verified and that a Data Processing Agreement (DPA) is in place before any student data is processed.

### 5.2 Stage 1 — Interest Form Submission

A teacher or school administrator completes the pilot interest form on `/schools.html`. Fields collected:

- Full name and job title
- School name and type (state / independent / international)
- Country
- Estimated number of students
- Exam boards used
- Message (optional)

On submission, Formspree sends a notification to the FlashDeck admin email and an auto-reply to the school contact.

### 5.3 Stage 2 — Admin Review

The super admin reviews the submission in the admin panel. Three possible outcomes:

| Decision | Action |
|---|---|
| **Approved** | Admin creates a school account in Supabase, sets pilot flag = true, sets expiry date (6 weeks from activation), sends setup email to school contact |
| **Waitlisted** | Admin sends a polite holding email; school added to waitlist in admin panel |
| **Declined** | Admin sends a polite decline email with an explanation |

### 5.4 Stage 3 — School Setup

On approval, the super admin:
1. Creates the school record in Supabase (`schools` table)
2. Creates the school admin account and sends an invite email with a time-limited registration link (48-hour expiry)
3. Attaches a Data Processing Agreement (DPA) to the email for the school to sign and return

### 5.5 Stage 4 — Teacher & Student Onboarding

Once the school admin has registered:
1. School admin logs in → lands on `/school-admin.html`
2. School admin creates teacher accounts (invite emails sent to each teacher)
3. Teachers log in → generate class codes for their classes
4. Students register using the class code, which links their account to the school and the teacher's class

### 5.6 Stage 5 — Pilot Period

During the 6-week pilot:
- All students and teachers have full Premium-equivalent access at no cost
- Teacher dashboard is enabled
- School admin can view school-wide usage data
- FlashDeck admin can monitor engagement via the admin panel

### 5.7 Stage 6 — Pilot Expiry

Two weeks before expiry, the school admin receives an email: *"Your FlashDeck pilot ends in 14 days — here's how to continue."*

One week before expiry, a banner appears in the school admin dashboard and teacher dashboard.

On expiry day, if no upgrade has been arranged:
- Student and teacher accounts enter a **read-only grace period** of 7 days (students can still study existing decks but cannot generate new ones)
- After 7 days, accounts are locked
- Student data is retained for 30 days, then deleted in accordance with the Privacy Policy

### 5.8 Stage 7 — Upgrade to School Partnership

If the school decides to continue:
1. FlashDeck admin selects the appropriate tier (Starter / Growth / Whole School) in the admin panel
2. An invoice is generated and sent to the school's finance contact
3. On payment confirmation, the pilot flag is removed, the expiry date is cleared, and the account transitions to a paid school partnership

---

## 6. Teacher & School Admin Onboarding

### 6.1 Invite Link Flow

Teachers and school admins receive a unique, time-limited invite link by email. The link contains a signed token that:
- Pre-fills the school name and role on the registration page
- Expires after 48 hours
- Can only be used once

If the link has expired, the recipient sees a clear message: *"This invite link has expired. Please contact your school administrator or FlashDeck support."*

### 6.2 Teacher Registration Form

| Field | Type | Notes |
|---|---|---|
| First name | Text | Pre-filled from admin setup if available |
| Last name | Text | |
| Email address | Email | Pre-filled from invite; cannot be changed |
| Password | Password | Min 8 chars |
| Confirm password | Password | |
| Subject(s) taught | Multi-select | Biology, Chemistry, Physics, Maths, English, History, Geography, etc. |

On registration, the teacher account is linked to the school record via `school_id` in Supabase. Role set to `teacher`.

### 6.3 School Admin Registration Form

Same as teacher, with the addition of:
- Job title (e.g. Head of Year, Assistant Principal, IT Manager)
- Role set to `school_admin`

---

## 7. Dashboard Logic

### 7.1 Student / Child Dashboard (`/dashboard.html`)

The student dashboard is the primary post-login destination for all student and child accounts. It displays:

- **Welcome banner** — "Welcome back, [first name]" with current date
- **Streak counter** — consecutive days studied (resets at midnight if no study session that day)
- **Daily goal** — configurable target (default: 20 cards/day); progress bar shown
- **Mastery overview** — overall mastery % across all subjects
- **Recent decks** — last 3 decks studied, with "Continue" shortcut
- **Due for review today** — cards flagged by the spaced repetition engine as due
- **Quick actions** — Generate New Deck, My Decks, Progress, Settings

### 7.2 Parent Dashboard (`/parent-dashboard.html`)

- Cards for each child (name, streak, mastery %, last active)
- Read-only progress view per child
- Subscription management
- Billing history

### 7.3 Teacher Dashboard (`/teacher-dashboard.html`)

- Class overview: all students, mastery % per topic, engagement trend
- Knowledge gap heatmap: topic × student grid (green = strong, amber = developing, red = needs support)
- Shared deck library: create, edit, assign decks to classes
- Class codes: generate and manage codes for student registration
- Student reports: individual mastery and engagement per student

### 7.4 School Admin Dashboard (`/school-admin.html`)

- School-wide usage: total students, active this week, average mastery %
- Teacher accounts: list, invite new teachers, deactivate
- Class management: view all classes and their teachers
- Pilot / subscription status: days remaining, upgrade prompt if applicable
- DPA status: signed / pending

### 7.5 Super Admin Panel (`/admin.html`)

- All schools: list, status (pilot / active / expired / locked), usage stats
- Pilot applications: review queue, approve / waitlist / decline
- User management: search, view, deactivate any account
- Topic Bank: browse cached cards, flag for review, delete
- Subscription overview: MRR, active subscriptions, churn
- System health: API usage, error rates

---

## 8. Generate Deck Flow

### 8.1 User Inputs

The generate page collects:

| Field | Options |
|---|---|
| Exam board | AQA / Edexcel (Pearson) / Cambridge Assessment / OCR |
| Subject | Biology / Chemistry / Physics / Maths / English Literature / English Language / History / Geography / Economics / Psychology / Computer Science / Business Studies |
| Level | GCSE / IGCSE / A Level |
| Topic | Free text or dropdown (populated from Topic Bank if available) |
| Question type | Definition / True or False / Short Answer / Diagram (SVG) / Multiple Choice / Calculation |
| Number of cards | 10 / 20 / 30 / 50 (Premium: up to 100) |

### 8.2 Free Tier Enforcement

Before generation begins, the system checks the user's monthly card count against their tier limit:

- **Free tier:** 100 cards/month (resets on the 1st of each month)
- **Premium / Family / School:** Unlimited

If the requested quantity would exceed the remaining free allowance, the user sees an upgrade prompt. They can choose to generate a reduced quantity within their remaining allowance, or upgrade to Premium.

### 8.3 Topic Bank Cache Logic

The system queries the Topic Bank (Supabase) before calling the Claude API:

1. **Full cache hit** (≥ requested quantity available): Return cards from bank. No API call. Cost: £0.
2. **Partial cache hit** (some cards available, not enough): Return existing cards + call Claude for the shortfall only. Store new cards in bank.
3. **Cache miss** (no cards for this combination): Call Claude for the full quantity. Store all cards in bank.

All cards stored in the Topic Bank are **anonymised** — they are not linked to any individual user. They are shared infrastructure.

### 8.4 Claude API Call

Model: **Claude Haiku** (best cost/quality ratio for structured generation tasks)

The prompt instructs Claude to return a JSON array of flashcard objects. Each object contains:
- `question` (string)
- `answer` (string)
- `type` (definition / true_false / short_answer / diagram / mcq / calculation)
- `difficulty` (1–3: easy / medium / hard)
- `svg` (string, only for diagram type — inline SVG markup)
- `board` (exam board)
- `subject`
- `level`
- `topic`

### 8.5 Deck Preview & Save

After generation, the user sees a preview of all cards before saving. They can:
- Flip individual cards to review question and answer
- Delete cards they don't want
- Edit card text (personal copy only — does not affect the shared Topic Bank)
- Regenerate the entire deck (counts against their monthly allowance again)
- Save the deck to their personal library

---

## 9. Study Interface & Spaced Repetition

### 9.1 Study Session Flow

1. User selects a deck from My Decks or the dashboard "due for review" queue
2. Cards are presented one at a time, question-side up
3. User taps/clicks to flip — answer (and SVG diagram if applicable) is revealed
4. User rates the card: **😓 Hard** / **🙂 Good** / **😄 Easy**
5. Rating is stored in Supabase (`card_ratings` table) with a timestamp
6. Next card shown until the session queue is exhausted
7. Session summary shown: cards reviewed, Hard/Good/Easy split, streak updated, mastery % updated

### 9.2 Spaced Repetition Algorithm

FlashDeck uses a simplified SM-2 algorithm for scheduling:

| Rating | Next review interval |
|---|---|
| Hard | 1 day |
| Good | 3 days |
| Easy | 7 days |

If a card is rated Hard on a subsequent review, the interval resets to 1 day. If rated Easy consistently, the interval grows (7 → 14 → 30 days). The exact growth multiplier is set to 2× for Easy ratings after the first review.

**Exam-date aware compression:** If the user has set an exam date in Settings, the spaced repetition engine compresses all intervals proportionally as the exam approaches, ensuring all cards are reviewed at least once in the final two weeks.

### 9.3 Mastery Calculation

Mastery % per topic is calculated as:

> (Number of cards rated Easy in the last 30 days) ÷ (Total cards in topic) × 100

A card is considered "mastered" when it has been rated Easy on two consecutive reviews. Mastery % is shown on the dashboard, the progress page, and (for teachers) the class heatmap.

---

## 10. Grade Prediction Engine

Grade prediction is a core differentiating feature of FlashDeck.AI, available to all users during the 14-day trial and to all Premium subscribers. It is locked for expired trial accounts as a conversion lever.

### 10.1 How the Prediction Works

The grade prediction uses a weighted mastery model tied directly to the official exam board topic weightings. This makes the prediction genuinely credible — it reflects how the actual exam paper is marked, not just a raw average of flashcard performance.

**Formula:**

> **Predicted Score = Σ (Topic Mastery % × Topic Exam Weighting %)**

The resulting score is mapped to the grade boundary table for the relevant exam board, subject, and level. Grade boundaries are stored in Supabase and updated annually after each exam series.

### 10.2 Prediction Display

The predicted grade is displayed as a **range** (e.g., "Grade 5–6") rather than a single number to manage expectations. As the student completes more study sessions, the confidence interval narrows. The prediction is recalculated after every study session and displayed on the student dashboard home (headline metric with trend arrow), the Progress page (Grade Trajectory chart, Focus Areas, AI Recommendation), the parent dashboard (per-child card), and the teacher dashboard (per-student, read-only).

### 10.3 Grade Trajectory Chart

The Grade Trajectory chart on the Progress page shows the student's predicted grade trajectory over the last 6 weeks (blue line) against their target grade (green line). The target grade is set by the student in Settings, defaulting to one grade above the current prediction.

### 10.4 AI Recommendation

After each study session, the system generates a short recommendation based on the student's weakest topics (lowest mastery × highest exam weighting). For example: *"Focus on Infection & Response this week. Your mastery has dropped 12% in the last 3 sessions and this topic carries 15% of the paper."* A "Start Targeted Session" button pre-loads a study session for that topic. This button is locked for expired trial accounts.

---

## 11. Monetisation & Subscription Logic

### 11.1 Subscription Tiers

FlashDeck operates a **14-day free trial** model, replacing the previous free tier. There is no permanently free plan. After the trial expires, users must upgrade to continue studying.

**Currency display:** UK visitors (detected via IP geolocation using ipapi.co) see GBP prices. All other visitors see USD prices. The pricing page updates dynamically on page load.

**Pricing page heading:** *"Simple pricing. No surprises."*

| Tier | Price (UK) | Price (Global) | Features |
|---|---|---|---|
| **14-Day Free Trial** | Free | Free | Full Premium access for 14 days. No credit card required. |
| **Individual Premium** | £7/month or £70/year | $7/month or $67/year | Unlimited cards, grade prediction, PDF export, priority AI generation |
| **Family — 2 children** | £10/month | $10/month | All Premium features for 2 children + parent dashboard |
| **Family — 3 children** | £13/month | $13/month | All Premium features for 3 children + parent dashboard |
| **Family — 4 children** | £16/month | $16/month | All Premium features for 4 children + parent dashboard |
| **School Starter** | £499/year | $599/year | Up to 100 students, teacher dashboard, shared decks |
| **School Growth** | £999/year | $1,199/year | Up to 300 students, multi-teacher, student reports |
| **School Whole School** | £1,999/year | $2,399/year | Unlimited students, full analytics, DPA included |

### 11.2 Trial Model

- Trial duration: 14 days from email verification
- No credit card required
- Full Premium access during trial (all features including grade prediction and PDF export)
- Trial expiry timestamp stored in `users.trial_expires_at`
- On expiry: account enters read-only mode — student can view existing decks and progress but cannot generate new cards or start new study sessions
- Grade prediction remains visible after expiry (as a conversion lever) but the "Start Targeted Session" button is locked

### 11.3 Stripe Integration
- Individual and family subscriptions use **Stripe Subscriptions** with monthly or annual billing
- School licences use **Stripe Invoicing** (invoice sent, paid by bank transfer or card)
- The "Ask a parent to pay" flow uses a **Stripe Payment Link** generated on demand
- Stripe webhooks update subscription status in Supabase in real timerely.

---

## 11. Data Retention & Compliance

### 11.1 UK GDPR Compliance

FlashDeck processes personal data under the following lawful bases:
- **Contract performance** — processing necessary to provide the service the user has signed up for
- **Legitimate interests** — improving the Topic Bank and platform quality (anonymised data only)
- **Consent** — marketing emails (separate opt-in, not bundled with terms)

### 11.2 Under-13 Policy

FlashDeck does not knowingly collect data from children under 13 without verifiable parental consent. The sign-up form requires users to confirm they are 13 or older. For child sub-accounts created by a parent, the parent's registration constitutes parental consent under GDPR Article 8 and the ICO's Children's Code.

### 11.3 Data Retention Schedule

| Data type | Retention period | Trigger for deletion |
|---|---|---|
| Active user account | Indefinitely while account is active | User requests deletion |
| Inactive user account | 2 years from last login | Automated purge |
| Pilot school data | 30 days after pilot expiry (if not upgraded) | Automated purge |
| Card ratings & study sessions | 2 years | Automated purge |
| Topic Bank cards | Indefinitely (anonymised) | Manual review only |
| Payment records | 7 years | UK tax law requirement |

### 11.4 Data Subject Rights

Users can exercise the following rights via the Settings page or by emailing privacy@flashdeck.ai:
- **Right of access** — download a copy of their data
- **Right to erasure** — delete their account and all associated data
- **Right to rectification** — correct inaccurate personal data
- **Right to portability** — export study data in a machine-readable format

### 11.5 School Data Processing Agreement

Every school that activates a pilot or paid partnership must sign a Data Processing Agreement (DPA) before any student data is processed. The DPA confirms:
- FlashDeck acts as the **data processor**; the school acts as the **data controller**
- Data is stored within the UK/EEA
- Sub-processors (Supabase, Vercel, Anthropic) are listed with their data locations
- Breach notification within 72 hours

---

## 12. Admin Panel (Super Admin)

The super admin panel (`/admin.html`) is accessible only to accounts with the `super_admin` role. It is not linked from any public page. Access is via direct URL only.

Key functions:

**School management:** View all school accounts, their status (pilot / active / expired / locked), pilot expiry dates, number of active students and teachers, and usage statistics. Approve, waitlist, or decline pilot applications. Generate and send invite links to school admins.

**User management:** Search for any user by email or name. View account details, role, subscription status, and activity. Deactivate or delete accounts. Reset passwords on behalf of users.

**Topic Bank management:** Browse all cached flashcards by board, subject, level, and topic. Flag cards for quality review. Delete cards that are factually incorrect or low quality.

**Subscription overview:** Total active subscriptions by tier, monthly recurring revenue (MRR), recent sign-ups, recent cancellations, and churn rate. Links to Stripe dashboard for detailed billing data.

---

## 13. Open Questions & Decisions Log

This section records decisions that have been made and questions that remain open. It is updated as each module is built.

| # | Question / Decision | Status | Resolution |
|---|---|---|---|
| 1 | Email verification — mandatory for all? | ✅ Decided | Yes — mandatory for all account types |
| 2 | Teacher / school admin self-registration? | ✅ Decided | No — invite-only after pilot approval |
| 3 | Parent account model | ✅ Decided | Parent is primary account holder; children are sub-accounts (IXL model) |
| 4 | Individual student upgrade path | ✅ Decided | Option C — "Ask a parent to pay" email + Stripe Payment Link |
| 5 | Family pricing structure | ✅ Decided | £7 base + £3/additional child; annual saves ~2 months |
| 6 | Nigeria — supported market? | ✅ Decided | Yes — Cambridge IGCSE & A Level only (no WAEC) |
| 7 | Matric (Pakistan) — supported? | ✅ Decided | No — Cambridge IGCSE & A Level only for Pakistan |
| 8 | WJEC — supported exam board? | ✅ Decided | No — not in V1 |
| 9 | School licence payment method | ✅ Decided | Stripe Invoicing (invoice + bank transfer or card) |
| 10 | Transactional email provider | ✅ Decided | Resend — domain flashdeck.ai verified 15 May 2026 |
| 11 (new) | Free tier vs. trial model | ✅ Decided | 14-day full-access trial replaces free tier. No credit card required. After expiry, account enters read-only mode. |
| 12 (new) | Currency display | ✅ Decided | GBP for UK visitors (IP geolocation via ipapi.co), USD for all others. Detected client-side on page load. |
| 13 (new) | Grade prediction — scope | ✅ Decided | Core feature, available during trial and to all Premium subscribers. Locked after trial expiry as conversion lever. |
| 14 (new) | Pricing page heading | ✅ Decided | Changed from "Simple, honest pricing" to "Simple pricing. No surprises." |
| 20 (new) | Parent dashboard — V1 scope? | ⬜ Open | To be confirmed by Zulfi |
| 11 | Age gate for under-13 (parental consent flow) | ⬜ Open | How to handle if a child enters age < 13 at sign-up? |
| 12 | Google OAuth for child sub-accounts | ⬜ Open | Should children be able to use Google sign-in, or email/password only? |
| 13 | Exam date field — required or optional? | ⬜ Open | If required, what happens for students who don't know their date yet? |
| 14 | Family package — launch with MVP or post-MVP? | ✅ Decided | Post-MVP — keeps initial build focused; Individual Premium only at launch |
| 15 | PDF export format | ⬜ Open | A4 portrait, 2 cards per page? Or full-page card per page? |

---

---

## 16. Change Log

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 13 May 2026 | Zulfi / Manus AI | Initial document |
| 1.1 | 29 May 2026 | Zulfi / Manus AI | Replaced free tier with 14-day full-access trial (Section 11). Added geo-based currency detection GBP/USD (Section 11.1). Added Grade Prediction Engine as new Section 10 (sections 10–13 renumbered). Updated permission matrix to include grade prediction row. Updated trial expiry login error state. Updated password reset redirect flow. Resolved open questions 9, 10. Added new decisions 11–14. Added open question 20 (parent dashboard V1 scope). |

---

*This document is maintained in the FlashDeck GitHub repository at `/docs/FLASHDECK_TECHNICAL_REFERENCE.md`. All significant design decisions should be recorded in Section 15 before implementation begins.*
