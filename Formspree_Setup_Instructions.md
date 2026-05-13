# FlashDeck.AI — Formspree Setup Instructions

**Prepared for:** FlashDeck Support Team  
**Prepared by:** FlashDeck.AI Development  
**Date:** May 2026  
**Estimated time to complete:** 10 minutes  
**Cost:** Free (no credit card required)

---

## Purpose

This document guides you through setting up Formspree for FlashDeck.AI. Formspree will power two contact forms on the website:

1. **Schools Partnership Enquiry Form** — on the `/schools` page, for head teachers and school staff enquiring about a pilot or partnership
2. **General Contact Form** — replacing the current `hello@flashdeck.ai` mailto link on the main website

When someone submits either form, Formspree automatically sends an email notification to `hello@flashdeck.ai` with all the details of the submission.

---

## Step 1 — Create the Formspree Account

1. Go to **https://formspree.io**
2. Click **Get Started** or **Sign Up**
3. Create a free account using the email address: `hello@flashdeck.ai`
4. Verify the email address when prompted

---

## Step 2 — Create Form 1: Schools Partnership Enquiry

1. Once logged in, click **+ New Form**
2. In the **Form Name** field, enter: `FlashDeck Schools Partnership Enquiry`
3. In the **Email** field, confirm it shows: `hello@flashdeck.ai`
4. Click **Create Form**
5. Formspree will generate a unique **Form Endpoint URL** — it will look like:
   `https://formspree.io/f/xxxxxxxx`
6. **Note down this URL** — this is the Schools Enquiry Endpoint

---

## Step 3 — Configure Form 1 Settings

1. In the form dashboard, click on **Settings** for the Schools Partnership Enquiry form
2. Under **Email Notifications**, confirm the notification email is set to `hello@flashdeck.ai`
3. Under **Submission Subject**, enter: `New Schools Partnership Enquiry — FlashDeck.AI`
4. Click **Save**

---

## Step 4 — Create Form 2: General Contact

1. Click **+ New Form** again
2. In the **Form Name** field, enter: `FlashDeck General Contact`
3. In the **Email** field, confirm it shows: `hello@flashdeck.ai`
4. Click **Create Form**
5. Formspree will generate another unique **Form Endpoint URL** — it will look like:
   `https://formspree.io/f/yyyyyyyy`
6. **Note down this URL** — this is the General Contact Endpoint

---

## Step 5 — Configure Form 2 Settings

1. In the form dashboard, click on **Settings** for the General Contact form
2. Under **Email Notifications**, confirm the notification email is set to `hello@flashdeck.ai`
3. Under **Submission Subject**, enter: `New Contact Form Submission — FlashDeck.AI`
4. Click **Save**

---

## Step 6 — Share the Details

Once all steps above are complete, please share the following two URLs with Zulfi so the development team can integrate Formspree into the website:

| Form | Endpoint URL |
|---|---|
| **Schools Partnership Enquiry** | *(e.g. https://formspree.io/f/xxxxxxxx)* |
| **General Contact** | *(e.g. https://formspree.io/f/yyyyyyyy)* |

---

## Notes

- The Formspree **free tier** allows **50 submissions per month** across all forms, which is more than sufficient for the initial launch period.
- Formspree will send an email notification to `hello@flashdeck.ai` for every form submission, containing all the details entered by the user.
- **Auto-reply to the submitter** (an automatic confirmation email sent to the person who filled in the form) is available on Formspree's paid plan at **$10/month**. This can be upgraded at any time — the development team will be notified when this is activated so the auto-reply message can be configured.
- No API keys or passwords are required — Formspree works using only the endpoint URLs above.
- If submission volumes grow beyond 50/month, the paid plan can be activated without any changes to the website code.
