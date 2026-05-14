# FlashDeck.AI — Auth Flow Test Scenarios

**Document version:** 1.0  
**Date:** May 2026  
**Prepared by:** Cutlass Group / FlashDeck.AI  
**For:** Support team — pre-dashboard QA pass  
**Live site:** https://www.flashdeck.ai

---

## Overview

This document covers all test scenarios for the four authentication pages currently live on FlashDeck.AI. Each scenario includes the page under test, the steps to execute, and the expected result. Tests should be run in a clean browser session (incognito/private window recommended) unless otherwise noted.

**Pages covered:**
- `/signup.html` — Student registration
- `/login.html` — Email/password and Google login
- `/forgot-password.html` — Password reset request
- `/reset-password.html` — New password entry

**Prerequisites:**
- Access to a real email inbox (e.g. a Gmail or Outlook account you control)
- Supabase dashboard access to verify user records: https://ryatewrjstploeahcpbe.supabase.co
- A browser with DevTools available (Chrome recommended)

---

## Section 1 — Sign-Up Page (`/signup.html`)

### 1.1 — User Type Selector

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| S-01 | Student tab reveals registration form | Click **Student** button | Student tab highlights in blue; full registration form appears below (Google button, name, email, password, exam board, level, year group) |
| S-02 | Parent tab shows redirect panel | Click **Parent** button | Parent tab highlights; "Family accounts — coming soon" panel appears; no registration form |
| S-03 | Teacher/School tab shows redirect panel | Click **Teacher / School** button | Teacher tab highlights; "Teachers & schools — apply for a pilot" panel appears with link to schools page |
| S-04 | Switching tabs resets view | Click Student → then Parent → then Student | Each tab switch correctly shows/hides the relevant panel; no residual content from previous selection |

---

### 1.2 — Student Form Validation

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| S-05 | Submit empty form | Click Student, then click **Create Free Account** without filling anything | Red error states appear on all required fields; form does not submit |
| S-06 | First name too short | Enter a single character in First name, tab away | Field border turns red; error message "Please enter your first name." appears beneath the field |
| S-07 | Last name too short | Enter a single character in Last name, tab away | Same red error as S-06 for Last name |
| S-08 | Invalid email format | Enter `notanemail` in Email, tab away | Red error: "Please enter a valid email address." |
| S-09 | Valid email clears error | After S-08, correct the email to `test@example.com` and tab away | Red error disappears; field border turns green |
| S-10 | Password too short | Enter `abc123` (6 chars) in Password, tab away | Red error: "Password must be at least 8 characters." |
| S-11 | Password strength indicator | Type progressively longer passwords (6 chars → 8 chars → add uppercase → add number) | Strength bar fills progressively; label changes from Weak → Fair → Good → Strong |
| S-12 | Passwords do not match | Enter `Password1` in Password, enter `Password2` in Confirm password, tab away | Red error on Confirm password: "Passwords do not match." |
| S-13 | Passwords match clears error | After S-12, correct Confirm password to `Password1`, tab away | Error clears; field turns green |
| S-14 | No exam board selected | Fill all other fields correctly, leave exam board unselected, submit | Error message appears: "Please select your exam board." |
| S-15 | No level selected | Fill all fields including exam board, leave Level as "Select level…", submit | Error: "Please select your level." |
| S-16 | No year group selected | Fill all fields including level, leave Year group as "Select year…", submit | Error: "Please select your year group." |
| S-17 | Age confirmation not ticked | Fill all fields correctly, leave age checkbox unticked, submit | Error: "Please confirm you are 13 or older." |
| S-18 | Terms not accepted | Fill all fields correctly, tick age but not Terms, submit | Error: "Please accept the Terms & Conditions." |
| S-19 | Class code optional | Fill all required fields, leave Class code blank, submit | Form submits without error (class code is optional) |

---

### 1.3 — Password Visibility Toggle

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| S-20 | Show/hide password | Enter a password, click the eye icon | Password characters become visible; icon changes to strikethrough eye |
| S-21 | Hide password again | Click the eye icon again | Password returns to dots/asterisks |

---

### 1.4 — Google Sign-Up

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| S-22 | Google OAuth redirect | Click **Continue with Google** | Browser redirects to Google's OAuth consent screen; after authorising, user is redirected to `/dashboard.html` |
| S-23 | Google sign-up creates user in Supabase | Complete S-22 successfully | New user record appears in Supabase Auth dashboard with provider = `google` |

---

### 1.5 — Successful Student Registration

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| S-24 | Full valid registration | Click Student; fill in: First name = `Test`, Last name = `User`, Email = `[your test email]`, Password = `TestPass1!`, Confirm = `TestPass1!`, select AQA, GCSE, Year 10; tick both checkboxes; click **Create Free Account** | Button shows spinner and "Creating account…"; then verification screen appears: "Check your inbox — we've sent a verification link to [email]" |
| S-25 | Verification email received | Check the inbox used in S-24 | Email from FlashDeck.AI (via Resend) arrives within 60 seconds; subject line confirms it is a verification email |
| S-26 | Verification link activates account | Click the verification link in the email | Browser opens flashdeck.ai and redirects to `/dashboard.html` (or a confirmation page); user is now active in Supabase |
| S-27 | User record in Supabase | Check Supabase Auth dashboard after S-26 | User record exists with `email_confirmed_at` populated; `user_metadata` contains `role: student`, `exam_board`, `exam_level`, `year_group` |
| S-28 | Resend verification email | On the verification screen, click **Resend verification email** | Button shows "Sending…" then "Sent! Check your inbox."; button re-enables after 5 seconds; a second verification email arrives |

---

### 1.6 — Duplicate Registration

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| S-29 | Register with existing email | Repeat S-24 using the same email address | Error message displayed: "User already registered" or equivalent Supabase error message; form does not proceed to verification screen |

---

## Section 2 — Login Page (`/login.html`)

### 2.1 — Form Validation

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| L-01 | Submit empty form | Click **Log In →** without entering anything | Error: "Please enter your email address." |
| L-02 | Submit with email only | Enter a valid email, leave password blank, click Log In | Error: "Please enter your password." |
| L-03 | Invalid email format | Enter `notanemail`, click Log In | Error: "Please enter a valid email address (e.g. you@example.com)." |
| L-04 | Password too short | Enter valid email, enter `abc` (3 chars), click Log In | Error: "Your password must be at least 8 characters." |

---

### 2.2 — Email/Password Login

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| L-05 | Correct credentials — verified account | Enter email and password from S-24 (after S-26 verification), click Log In | Button shows "Logging in…"; user is redirected to `/dashboard.html` |
| L-06 | Wrong password | Enter correct email, wrong password, click Log In | Error: "Incorrect email or password. Please try again." |
| L-07 | Non-existent email | Enter an email not in Supabase, any password, click Log In | Error: "Incorrect email or password. Please try again." |
| L-08 | Unverified account | Register a new account (S-24) but do NOT click the verification link; then try to log in | Error: "Please verify your email address before logging in. Check your inbox for the verification link." |
| L-09 | Password visibility toggle | Enter a password, click the eye icon | Password becomes visible; clicking again hides it |

---

### 2.3 — Google Login

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| L-10 | Google OAuth login | Click **Continue with Google** | Redirects to Google consent screen; after authorising, redirects to `/dashboard.html` |
| L-11 | Google login with existing Google account | Use the same Google account as S-22 | Logs in to the existing account (does not create a duplicate) |

---

### 2.4 — Navigation Links

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| L-12 | Forgot password link | Click **Forgot your password?** | Navigates to `/forgot-password.html` |
| L-13 | Sign up link | Click **Create a free account** | Navigates to `/signup.html` |

---

## Section 3 — Forgot Password Page (`/forgot-password.html`)

### 3.1 — Form Validation

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| FP-01 | Submit empty form | Click **Send Reset Link** without entering anything | Error: "Please enter your email address." |
| FP-02 | Invalid email format | Enter `notanemail`, click Send Reset Link | Error: "Please enter a valid email address." |

---

### 3.2 — Reset Email Flow

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| FP-03 | Valid registered email | Enter the email from S-24, click **Send Reset Link** | Button shows "Sending…"; success state appears: "Check your inbox — we've sent a reset link to [email]" |
| FP-04 | Reset email received | Check the inbox | Password reset email from FlashDeck.AI arrives within 60 seconds |
| FP-05 | Non-existent email | Enter an email not registered in Supabase, click Send Reset Link | Success state still shown (Supabase does not reveal whether an email exists — this is correct security behaviour) |
| FP-06 | Try again link | On the success state, click **Try a different email** | Form resets to initial state; email field is cleared |

---

## Section 4 — Reset Password Page (`/reset-password.html`)

> **Note:** This page is only accessible via the link in the password reset email. It cannot be navigated to directly in a meaningful way.

### 4.1 — Valid Reset Flow

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| RP-01 | Open reset link from email | Click the password reset link from FP-04 | Browser opens `/reset-password.html` with a valid token in the URL hash; form is displayed |
| RP-02 | Submit new password | Enter `NewPass1!` in New password, `NewPass1!` in Confirm, click **Set New Password** | Success message shown; user redirected to `/login.html` |
| RP-03 | Login with new password | On login page, enter email + `NewPass1!` | Login succeeds; user redirected to `/dashboard.html` |
| RP-04 | Old password no longer works | Attempt login with the original password from S-24 | Error: "Incorrect email or password." |

---

### 4.2 — Reset Page Validation

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| RP-05 | Password too short | Enter `abc` in New password, click Set New Password | Error: "Password must be at least 8 characters." |
| RP-06 | Passwords do not match | Enter `NewPass1!` and `Different1!`, click Set New Password | Error: "Passwords do not match." |
| RP-07 | Expired reset link | Wait 24 hours after FP-04, then click the same reset link | Error message shown: "Reset link has expired. Please request a new one." |
| RP-08 | Reset link used twice | Complete RP-02, then click the same reset link again | Error message shown: link is invalid or already used |

---

## Section 5 — Cross-Cutting Checks

### 5.1 — Navigation & Layout

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| X-01 | Logo link | Click the FlashDeck.AI logo on any auth page | Navigates to `/index.html` (homepage) |
| X-02 | Sign in / Sign up links | Check nav links on signup.html and login.html | signup.html shows "Sign in" link → `/login.html`; login.html shows "Create a free account" link → `/signup.html` |
| X-03 | Mobile responsiveness | Open each auth page on a mobile device or using DevTools mobile emulation (375px width) | All forms are usable; no horizontal overflow; buttons are full width; text is legible |
| X-04 | Page titles | Check browser tab title on each page | signup.html: "Sign Up Free — FlashDeck.AI"; login.html: "Log In — FlashDeck.AI"; forgot-password.html: "Reset Password — FlashDeck.AI"; reset-password.html: "Set New Password — FlashDeck.AI" |

---

### 5.2 — Security Checks

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| X-05 | config.js does not expose secrets in GitHub | Check https://github.com/FlashdeckAI/flashdeck-ai | `config.js` is not present in the repository; `.gitignore` lists it |
| X-06 | config.js is served on live site | Open https://www.flashdeck.ai/config.js | File returns HTTP 200 with `window.FLASHDECK_CONFIG` containing the Supabase URL and anon key |
| X-07 | Anon key is publishable (not secret) | Check the key value in config.js against Supabase dashboard | Key begins with `sb_publishable_` — this is correct; it is safe to expose in a browser |
| X-08 | No sensitive data in page source | View source of signup.html, login.html | No hardcoded credentials; only a `<script src="/config.js">` reference |

---

### 5.3 — Console Error Check

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| X-09 | No JS errors on load | Open each auth page; open DevTools Console (F12) | No red errors in the console; config.js loads with HTTP 200; Supabase JS loads from CDN |
| X-10 | No 404 errors | Check DevTools Network tab on each page load | All resources return 200; no 404s other than favicon (acceptable if not yet set) |

---

## Test Results Log

Use the table below to record pass/fail status during testing.

| Test ID | Tester | Date | Result | Notes |
|---------|--------|------|--------|-------|
| S-01 | | | | |
| S-02 | | | | |
| S-03 | | | | |
| S-04 | | | | |
| S-05 | | | | |
| S-06 | | | | |
| S-07 | | | | |
| S-08 | | | | |
| S-09 | | | | |
| S-10 | | | | |
| S-11 | | | | |
| S-12 | | | | |
| S-13 | | | | |
| S-14 | | | | |
| S-15 | | | | |
| S-16 | | | | |
| S-17 | | | | |
| S-18 | | | | |
| S-19 | | | | |
| S-20 | | | | |
| S-21 | | | | |
| S-22 | | | | |
| S-23 | | | | |
| S-24 | | | | |
| S-25 | | | | |
| S-26 | | | | |
| S-27 | | | | |
| S-28 | | | | |
| S-29 | | | | |
| L-01 | | | | |
| L-02 | | | | |
| L-03 | | | | |
| L-04 | | | | |
| L-05 | | | | |
| L-06 | | | | |
| L-07 | | | | |
| L-08 | | | | |
| L-09 | | | | |
| L-10 | | | | |
| L-11 | | | | |
| L-12 | | | | |
| L-13 | | | | |
| FP-01 | | | | |
| FP-02 | | | | |
| FP-03 | | | | |
| FP-04 | | | | |
| FP-05 | | | | |
| FP-06 | | | | |
| RP-01 | | | | |
| RP-02 | | | | |
| RP-03 | | | | |
| RP-04 | | | | |
| RP-05 | | | | |
| RP-06 | | | | |
| RP-07 | | | | |
| RP-08 | | | | |
| X-01 | | | | |
| X-02 | | | | |
| X-03 | | | | |
| X-04 | | | | |
| X-05 | | | | |
| X-06 | | | | |
| X-07 | | | | |
| X-08 | | | | |
| X-09 | | | | |
| X-10 | | | | |

---

## Known Limitations at Time of Testing

The following items are intentional and should **not** be raised as bugs:

1. **Dashboard page does not exist yet** — after a successful login or email verification, the browser will land on a 404 for `/dashboard.html`. This is expected; the dashboard is the next development milestone.
2. **Google OAuth redirect** — after Google login, the user is redirected to `/dashboard.html` which does not yet exist. This is expected.
3. **"About Us" page is incomplete** — intentional; company name is pending confirmation.
4. **IB Diploma not in exam board selector** — intentional; listed as "coming soon" on the homepage only.

---

*Document maintained in: `/docs/AUTH_TEST_SCENARIOS.md` in the FlashDeck.AI GitHub repository.*
