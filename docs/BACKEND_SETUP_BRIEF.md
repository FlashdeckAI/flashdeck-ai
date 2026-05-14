# FlashDeck.AI — Backend Services Setup Brief

**Prepared for:** Support Team  
**Prepared by:** FlashDeck Development  
**Date:** May 2026  
**Priority:** High — required before authentication can go live

---

## Overview

This document covers the three services that need to be set up and configured before FlashDeck.AI's user registration, login, and password reset flows can go live. All three are free to set up at the scale we need for the MVP and pilot phase.

Once each service is configured, the support team needs to collect the credentials listed at the end of each section and pass them back to the developer. **Do not share credentials over WhatsApp or email in plain text** — use a secure method such as a shared password manager entry or an encrypted note.

---

## Service 1 — Supabase (Database & Authentication)

**What it is:** Supabase is the backend database and authentication platform for FlashDeck.AI. It handles user accounts, sessions, email verification, and password resets. It is free for up to 50,000 monthly active users.

**Website:** [https://supabase.com](https://supabase.com)

### Steps

**1. Create an account**

Go to [https://supabase.com](https://supabase.com) and sign up using the company Google account or a dedicated `dev@flashdeck.ai` email address. Verify the email address when prompted.

**2. Create a new project**

Once logged in, click **New Project**. Fill in the following:

| Field | Value |
|---|---|
| Organisation | Create new → name it `FlashDeck` |
| Project name | `flashdeck-production` |
| Database password | Generate a strong password (save it securely — this is the master database password) |
| Region | **Europe West (London)** — this is important for UK GDPR compliance; student data must stay in the UK/EEA |
| Pricing plan | Free |

Click **Create new project** and wait approximately 2 minutes for provisioning to complete.

**3. Collect the API keys**

Once the project is ready, go to **Project Settings** (gear icon, bottom left) → **API**.

You will see two values that the developer needs:

| Key name | Where to find it | What it looks like |
|---|---|---|
| **Project URL** | Under "Project URL" | `https://xxxxxxxxxxxx.supabase.co` |
| **Anon (public) key** | Under "Project API keys" → `anon` `public` | A long string starting with `eyJ...` |

> **Important:** Do **not** share the `service_role` key — that is a secret server key and must never be exposed in a website. Only the `anon` key is needed at this stage.

**4. Enable Email Auth**

Go to **Authentication** (left sidebar) → **Providers** → **Email**. Ensure the following settings are enabled:

- **Enable Email provider:** ON
- **Confirm email:** ON (this triggers the verification email on sign-up)
- **Secure email change:** ON

Click **Save**.

**5. Set the Site URL and Redirect URLs**

Go to **Authentication** → **URL Configuration**. Set:

| Field | Value |
|---|---|
| **Site URL** | `https://flashdeck.ai` |
| **Redirect URLs** | Add each of the following on a separate line: `https://flashdeck.ai/reset-password.html` and `https://flashdeck.ai/dashboard.html` |

Click **Save**.

---

## Service 2 — Resend (Transactional Email)

**What it is:** Resend is the email delivery service that sends verification emails, password reset links, and invite notifications on behalf of FlashDeck.AI. It is free for up to 3,000 emails per month (100 per day), which is more than sufficient for the MVP and pilot phase.

**Website:** [https://resend.com](https://resend.com)

### Steps

**1. Create an account**

Go to [https://resend.com](https://resend.com) and sign up using `dev@flashdeck.ai` or the company Google account. Verify the email address.

**2. Add and verify the sending domain**

FlashDeck.AI emails should be sent from `noreply@flashdeck.ai` (or `hello@flashdeck.ai`). To do this, Resend needs to verify that FlashDeck owns the `flashdeck.ai` domain.

Go to **Domains** → **Add Domain** → enter `flashdeck.ai`.

Resend will provide a set of **DNS records** (typically 3–4 entries: SPF, DKIM, and optionally DMARC). These records need to be added to the `flashdeck.ai` domain's DNS settings in Vercel (where the domain is managed).

> **Action required:** Share the DNS records Resend provides with the person who manages the `flashdeck.ai` domain in Vercel. Once the records are added, click **Verify** in Resend. Verification typically takes 5–30 minutes.

**3. Create an API key**

Go to **API Keys** → **Create API Key**. Set:

| Field | Value |
|---|---|
| Name | `flashdeck-production` |
| Permission | **Sending access** (not full access) |
| Domain | Select `flashdeck.ai` |

Click **Add**. The key will be shown **once only** — copy it immediately and store it securely.

**4. Configure Resend as the SMTP provider in Supabase**

Go back to Supabase → **Project Settings** → **Authentication** → **SMTP Settings**. Enable **Custom SMTP** and fill in:

| Field | Value |
|---|---|
| Sender name | `FlashDeck.AI` |
| Sender email | `noreply@flashdeck.ai` |
| Host | `smtp.resend.com` |
| Port | `465` |
| Username | `resend` |
| Password | *(paste the Resend API key from step 3)* |

Click **Save**. Supabase will now use Resend to deliver all authentication emails.

**5. Customise the email templates (optional but recommended)**

In Supabase → **Authentication** → **Email Templates**, you can customise the subject lines and body of the verification and password reset emails. Suggested subject lines:

- **Confirm signup:** `Verify your FlashDeck.AI account`
- **Reset password:** `Reset your FlashDeck.AI password`
- **Magic link:** `Your FlashDeck.AI login link`

The developer can provide branded HTML templates for these if needed — flag this when handing back credentials.

---

## Service 3 — Google OAuth (Sign in with Google)

**What it is:** This allows users to register and log in to FlashDeck.AI using their existing Google account, without needing to create a separate password. It uses Google's official OAuth 2.0 system.

**What you need:** Access to a Google account that will own the OAuth credentials. This should be a company Google account (e.g. `dev@flashdeck.ai` or `zulfi@cutlassgroup.com`), not a personal account.

### Steps

**1. Go to Google Cloud Console**

Go to [https://console.cloud.google.com](https://console.cloud.google.com) and sign in with the company Google account.

**2. Create a new project**

Click the project dropdown at the top → **New Project**. Set:

| Field | Value |
|---|---|
| Project name | `FlashDeck AI` |
| Organisation | Leave as default (or select Cutlass Group if available) |

Click **Create**.

**3. Configure the OAuth consent screen**

In the left sidebar, go to **APIs & Services** → **OAuth consent screen**.

Select **External** (this allows any Google account to sign in, not just accounts within your organisation). Click **Create**.

Fill in:

| Field | Value |
|---|---|
| App name | `FlashDeck.AI` |
| User support email | `hello@flashdeck.ai` |
| App logo | Upload the FlashDeck logo (optional but recommended) |
| App domain → Homepage | `https://flashdeck.ai` |
| App domain → Privacy policy | `https://flashdeck.ai/privacy.html` |
| App domain → Terms of service | `https://flashdeck.ai/terms.html` |
| Authorised domains | `flashdeck.ai` |
| Developer contact email | `dev@flashdeck.ai` |

Click **Save and Continue** through the Scopes screen (no additional scopes needed — just the default `email` and `profile`). On the Test Users screen, add your own email for testing. Click **Save and Continue**.

**4. Create OAuth credentials**

Go to **APIs & Services** → **Credentials** → **Create Credentials** → **OAuth client ID**.

Set:

| Field | Value |
|---|---|
| Application type | **Web application** |
| Name | `FlashDeck Web` |
| Authorised JavaScript origins | `https://flashdeck.ai` |
| Authorised redirect URIs | Copy this exactly from Supabase: go to **Authentication** → **Providers** → **Google** → copy the **Callback URL** shown there (it will look like `https://xxxxxxxxxxxx.supabase.co/auth/v1/callback`) |

Click **Create**. You will be shown two values:

| Key name | What it looks like |
|---|---|
| **Client ID** | `xxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.apps.googleusercontent.com` |
| **Client Secret** | A short alphanumeric string |

Copy both immediately and store them securely.

**5. Enable Google as a provider in Supabase**

Go to Supabase → **Authentication** → **Providers** → **Google**. Enable it and paste in the **Client ID** and **Client Secret** from step 4. Click **Save**.

**6. Publish the OAuth app (when ready for production)**

While in testing mode, only the test users you added in step 3 can use Google sign-in. When FlashDeck.AI is ready to launch publicly, go back to **OAuth consent screen** → **Publishing status** → **Publish App**. Google may request a brief review for apps requesting sensitive scopes — since FlashDeck only uses `email` and `profile`, this review is typically waived or completed within a few days.

---

## Credentials Handback Checklist

Once all three services are set up, please provide the following to the developer securely:

| # | Item | Service | Notes |
|---|---|---|---|
| 1 | **Supabase Project URL** | Supabase | e.g. `https://xxxx.supabase.co` |
| 2 | **Supabase Anon Key** | Supabase | Starts with `eyJ...` — public key only |
| 3 | **Supabase Database Password** | Supabase | Master DB password — store securely, developer may not need this immediately |
| 4 | **Resend API Key** | Resend | Shown once only at creation |
| 5 | **Google OAuth Client ID** | Google Cloud | Ends in `.apps.googleusercontent.com` |
| 6 | **Google OAuth Client Secret** | Google Cloud | Short alphanumeric string |
| 7 | **Confirmation that DNS records are added** | Resend / Vercel | Resend domain verification must be complete |

---

## Estimated Time

| Task | Estimated time |
|---|---|
| Supabase setup | 20–30 minutes |
| Resend setup + DNS verification | 30–60 minutes (DNS propagation can take up to 1 hour) |
| Google OAuth setup | 20–30 minutes |
| **Total** | **~1.5–2 hours** |

---

## Questions?

If anything is unclear during setup, contact the developer before proceeding — it is better to ask than to create duplicate projects or misconfigure settings that are difficult to undo.

All credentials should be shared via a secure channel. Do not send API keys or secrets in plain-text email or WhatsApp messages.
