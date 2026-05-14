# FlashDeck.AI — Master Task List

> Last updated: 14 May 2026  
> Legend: ✅ Done · 🔄 In Progress · ⬜ To Do · 🔒 Blocked (dependency)

---

## 1. Business & Strategy

| # | Task | Status | Notes |
|---|---|---|---|
| 1.1 | Business case document | ✅ Done | Pitched to Cutlass Group (Humayun Shahzad) |
| 1.2 | Technical specification document | ✅ Done | Next.js + Supabase + Claude + Stripe stack |
| 1.3 | Business plan V3 | ✅ Done | Earlier version; superseded by business case |
| 1.4 | Claude API cost research | ✅ Done | Haiku confirmed as best cost/quality model |
| 1.5 | School pricing research | ✅ Done | Benchmarked vs Quizlet, Seneca, Tassomai |
| 1.6 | School pitch deck | ✅ Done | 11-slide deck, dual £/$ pricing, pilot offer, Data & Privacy slide added |
| 1.7 | Define school pilot programme terms | ⬜ To Do | Duration, eligibility, what counts as success |
| 1.8 | Define school partnership contract | ⬜ To Do | Legal agreement for paid school licences |
| 1.9 | Identify first 10 target schools | ⬜ To Do | UK, Pakistan, Nigeria, Middle East |
| 1.10 | Investor / partner update for Cutlass Group | ⬜ To Do | Progress update once MVP is live |

---

## 2. Infrastructure & DevOps

| # | Task | Status | Notes |
|---|---|---|---|
| 2.1 | Domain registration — flashdeck.ai | ✅ Done | Registered via Name.com |
| 2.2 | DNS configuration at Name.com | ✅ Done | A record + CNAME pointing to Vercel |
| 2.3 | GitHub account — FlashdeckAI | ✅ Done | github.com/FlashdeckAI |
| 2.4 | GitHub repository — flashdeck-ai | ✅ Done | Private repo, connected to Vercel |
| 2.5 | Vercel account setup | ✅ Done | Connected to GitHub for auto-deploy |
| 2.6 | Vercel deployment — flashdeck-ai | ✅ Done | Auto-deploys on every GitHub push |
| 2.7 | Custom domain connected — flashdeck.ai | ✅ Done | Live at https://www.flashdeck.ai |
| 2.8 | SSL certificate | ✅ Done | Auto-provisioned by Vercel |
| 2.9 | Supabase project setup | ✅ Done | Project ID: ryatewrjstploeahcpbe — credentials received |
| 2.10 | Supabase database schema | ⬜ To Do | Users, decks, cards, topic bank, schools tables |
| 2.11 | Anthropic (Claude) API account | 🔄 In Progress | Subscription pending |
| 2.12 | Stripe account setup | ⬜ To Do | For $7/month individual + school licences |
| 2.13 | Environment variables in Vercel | ✅ Done | config.js deployed with Supabase URL + Anon Key |
| 2.14 | Email provider setup (transactional) | ✅ Done | Resend configured as SMTP in Supabase — flashdeck.ai domain verified |

---

## 3. Marketing Website (flashdeck.ai)

| # | Task | Status | Notes |
|---|---|---|---|
| 3.1 | Landing page — full design & build | ✅ Done | 14 sections, fully responsive |
| 3.2 | Hero carousel (3 images) | ✅ Done | Auto-rotating, dot indicators |
| 3.3 | Interactive demo section | ✅ Done | Pre-built card data for 4 subjects |
| 3.4 | Pricing section (monthly/annual toggle) | ✅ Done | Free / Premium / School tiers |
| 3.5 | Audience panels (UK, IGCSE, Teachers) | ✅ Done | Product mockup images, corrected copy |
| 3.6 | Testimonials section | ✅ Done | 2 real + 1 placeholder |
| 3.7 | FAQ section | ✅ Done | 6 accordion items |
| 3.8 | Sustainability section | ✅ Done | Eco stats and pledge |
| 3.9 | Log In page | ✅ Done | Email + Google OAuth UI, validation |
| 3.10 | Sign Up page | ✅ Done | 4 exam boards, password strength meter |
| 3.11 | Schools landing page (/schools.html) | ✅ Done | Hero, how it works, 3 pricing tiers, pilot form, FAQ — wired to Formspree |
| 3.12 | Contact page (/contact.html) | ✅ Done | General enquiry form wired to Formspree |
| 3.13 | Privacy Policy page (/privacy.html) | ✅ Done | UK GDPR compliant, student data, data subject rights |
| 3.14 | Terms of Service page (/terms.html) | ✅ Done | Subscriptions, refund policy, AI disclaimer, DPA requirement |
| 3.15 | Sustainability Policy page (/sustainability.html) | ✅ Done | Paper-free stats, comparison table, six commitment cards |
| 3.16 | For Schools + Contact links in main navigation | ✅ Done | Added to desktop and mobile nav in index.html |
| 3.17 | Formspree endpoints wired | ✅ Done | School enquiry: xbdwbqbq · General contact: xeendvwe |
| 3.18 | Factual accuracy audit (exam boards, countries) | ✅ Done | Fixed 10+ exam boards → 4, removed WJEC, fixed countries label, removed Matric |
| 3.19 | Cookie consent banner | ⬜ To Do | Required for GDPR compliance |
| 3.20 | Blog / SEO content | ⬜ To Do | Post-MVP — drives organic student traffic |
| 3.21 | Admin notification on pilot form submission | ⬜ To Do | Email alert when a school submits interest |

---

## 3b. Presentations & Version Control

| # | Task | Status | Notes |
|---|---|---|-|
| P.1 | Presentations folder on GitHub | ✅ Done | `/presentations/` folder created in repo |
| P.2 | Schools pitch deck v1 stored on GitHub | ✅ Done | All slides in `/presentations/schools-pitch-v1/` |
| P.3 | Presentations README with versioning guide | ✅ Done | Documents how to add new versions |
| P.4 | Pitch deck — Data & Privacy slide added | ✅ Done | Slide 7: UK GDPR, DPA, no data selling, UK/EEA storage, under-13 protections |
| P.5 | Pitch deck — accuracy corrections | ✅ Done | WJEC removed, Matric removed, year updated to 2026, countries label corrected |
| P.6 | Schools pitch deck v2 (post-feedback) | ⬜ To Do | Update after first round of school pitches |
| P.7 | Investor / Cutlass Group update deck | ⬜ To Do | Progress update once MVP is live |
| P.8 | Student-facing demo deck | ⬜ To Do | Short deck for student recruitment / social media |

---

## 4. Application — Authentication

| # | Task | Status | Notes |
|---|---|---|---|
| 4.1 | Supabase email/password sign-up | ✅ Done | signup.html wired to Supabase via config.js |
| 4.2 | Email verification flow | ✅ Done | Supabase sends verification email via Resend; post-verify redirect to dashboard.html |
| 4.3 | Google OAuth sign-in | ✅ Done | Google provider enabled in Supabase; OAuth buttons wired in login.html and signup.html |
| 4.4 | Log In flow | ✅ Done | login.html wired to Supabase signInWithPassword + role-based routing |
| 4.5 | Forgot password / reset flow | ✅ Done | forgot-password.html + reset-password.html built and wired to Supabase resetPasswordForEmail |
| 4.6 | Session persistence (30-day) | ⬜ To Do | "Keep me logged in" checkbox |
| 4.7 | Protected routes (redirect if not logged in) | ⬜ To Do | Dashboard, Generate, Study all require auth |
| 4.8 | School class code registration | ⬜ To Do | Students join school account via code |

---

## 5. Application — Dashboard & Core UI

**⬜ NEXT SPRINT — Starting tomorrow**

| # | Task | Status | Notes |
|---|---|---|---|
| 5.1 | Navigation shell (sidebar + top bar) | ⬜ To Do | Persistent across all logged-in pages |
| 5.2 | Dashboard home page | ⬜ To Do | Welcome, streak, stats, recent decks, daily goal |
| 5.3 | Generate deck page | ⬜ To Do | Board / subject / level / topic / type / quantity selectors |
| 5.4 | My Decks page | ⬜ To Do | Grid of saved decks, mastery %, last studied |
| 5.5 | Deck viewer / preview | ⬜ To Do | Review cards before saving |
| 5.6 | Study interface | ⬜ To Do | Flip card, Hard/Good/Easy rating |
| 5.7 | Session summary screen | ⬜ To Do | Score, streak update, mastery % |
| 5.8 | Progress page | ⬜ To Do | Heatmap, subject breakdown, mastery rings |
| 5.9 | Settings page | ⬜ To Do | Profile, password, notifications, subscription |
| 5.10 | Mobile responsive layout | ⬜ To Do | Bottom tab bar on mobile |

---

## 6. Application — AI & Topic Bank

| # | Task | Status | Notes |
|---|---|---|---|
| 6.1 | Claude API integration | 🔒 Blocked | Needs API key |
| 6.2 | Prompt engineering for flashcard generation | ⬜ To Do | Per question type: definition, T/F, short answer, diagram, MCQ, calculation |
| 6.3 | Topic Bank database table | ⬜ To Do | Board · Subject · Level · Topic · Type · Cards |
| 6.4 | Cache-check logic (query Topic Bank first) | ⬜ To Do | Return existing cards if enough exist |
| 6.5 | Top-up logic (partial cache hit) | ⬜ To Do | Generate only the shortfall, merge with cached |
| 6.6 | Store new cards in Topic Bank | ⬜ To Do | Every new generation adds to shared pool |
| 6.7 | SVG diagram generation for science | ⬜ To Do | Cell biology, circuits, force diagrams, molecules |
| 6.8 | Question type selector in Generate UI | ⬜ To Do | Definition · True/False · Short Answer · Diagram · MCQ · Calculation |

---

## 7. Application — Spaced Repetition

| # | Task | Status | Notes |
|---|---|---|---|
| 7.1 | Card rating system (Hard / Good / Easy) | ⬜ To Do | Stored per card per user |
| 7.2 | Review schedule calculation | ⬜ To Do | Hard=1 day, Good=3 days, Easy=7 days (SM-2 algorithm) |
| 7.3 | Due-today queue | ⬜ To Do | Surface cards due for review on dashboard |
| 7.4 | Exam-date aware scheduling | ⬜ To Do | Compress schedule as exam date approaches |
| 7.5 | Mastery % calculation per topic | ⬜ To Do | Based on Easy ratings over time |

---

## 8. Application — Monetisation

| # | Task | Status | Notes |
|---|---|---|---|
| 8.1 | Free tier enforcement (100 cards/month) | ⬜ To Do | Counter in Supabase, checked on generate |
| 8.2 | Stripe individual subscription ($7/month) | ⬜ To Do | Checkout, webhook, subscription status |
| 8.3 | Premium feature gates (PDF export, unlimited) | ⬜ To Do | Check subscription status before allowing |
| 8.4 | Annual discount (e.g. 2 months free) | ⬜ To Do | Toggle on pricing page already built |
| 8.5 | School pilot account type | ⬜ To Do | Pilot flag + expiry date in Supabase |
| 8.6 | School pilot interest form (/schools) | ✅ Done | Live at /schools.html, wired to Formspree |
| 8.7 | Admin panel — approve/reject pilot requests | ⬜ To Do | Simple admin UI for Zulfi to manage pilots |
| 8.8 | Pilot expiry flow | ⬜ To Do | Banner shown, account locked after grace period |
| 8.9 | School partnership upgrade flow | ⬜ To Do | Invoice generation or Stripe school licence |
| 8.10 | School licence tiers (Starter/Growth/Whole School) | ✅ Done | Defined on /schools.html — Starter £499 / Growth £999 / Whole School £1,999 |

---

## 9. Application — Teacher & School Features

| # | Task | Status | Notes |
|---|---|---|---|
| 9.1 | Teacher dashboard | ⬜ To Do | Class progress, mastery rates, engagement |
| 9.2 | Shared deck library | ⬜ To Do | Teachers create and assign decks to classes |
| 9.3 | Student reports | ⬜ To Do | Individual mastery per topic |
| 9.4 | Class code system | ⬜ To Do | Generate codes, students join class |
| 9.5 | Multi-teacher support per school | ⬜ To Do | Growth and Whole School tiers |

---

## 10. Compliance & Legal

| # | Task | Status | Notes |
|---|---|---|---|
| 10.1 | Privacy Policy | ✅ Done | Live at /privacy.html — UK GDPR, student data, data subject rights |
| 10.2 | Terms of Service | ✅ Done | Live at /terms.html — subscriptions, refund, AI disclaimer, DPA requirement |
| 10.3 | Cookie Policy + consent banner | ⬜ To Do | Required for EU/UK visitors |
| 10.4 | COPPA / GDPR-K compliance (under-13s) | ⬜ To Do | Age gate or parental consent for younger students |
| 10.5 | Data retention policy | ⬜ To Do | How long student data is kept |
| 10.6 | School data processing agreement (DPA) | ⬜ To Do | Required by UK schools before signing — template needed |

---

## 11. Testing & Launch

| # | Task | Status | Notes |
|---|---|---|---|
| 11.1 | Internal testing (Zulfi + team) | ⬜ To Do | Full user journey end-to-end |
| 11.2 | Beta invite — 10–20 students | ⬜ To Do | Gather real usage feedback |
| 11.3 | School pilot — first 2–3 schools | ⬜ To Do | Target post-exam season (end of June) |
| 11.4 | Performance testing | ⬜ To Do | Load test Claude API + Supabase queries |
| 11.5 | Cross-browser / cross-device testing | ⬜ To Do | Chrome, Safari, Firefox; iOS, Android |
| 11.6 | Public launch | ⬜ To Do | Announce on LinkedIn, ProductHunt, etc. |

---

## Summary

| Category | Done | In Progress | To Do | Total |
|---|---|---|---|---|
| Business & Strategy | 6 | 0 | 4 | 10 |
| Infrastructure & DevOps | 11 | 1 | 2 | 14 |
| Marketing Website | 18 | 0 | 3 | 21 |
| Presentations & Version Control | 5 | 0 | 3 | 8 |
| Authentication | 5 | 0 | 3 | 8 |
| Dashboard & Core UI | 0 | 0 | 10 | 10 |
| AI & Topic Bank | 0 | 1 | 7 | 8 |
| Spaced Repetition | 0 | 0 | 5 | 5 |
| Monetisation | 2 | 0 | 8 | 10 |
| Teacher & School Features | 0 | 0 | 5 | 5 |
| Compliance & Legal | 2 | 0 | 4 | 6 |
| Testing & Launch | 0 | 0 | 6 | 6 |
| **Total** | **49** | **2** | **60** | **111** |
