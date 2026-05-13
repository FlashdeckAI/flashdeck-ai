# FlashDeck.AI — EmailJS Setup Instructions

**Prepared for:** FlashDeck Support Team  
**Prepared by:** FlashDeck.AI Development  
**Date:** May 2026  
**Estimated time to complete:** 15–20 minutes  
**Cost:** Free (no credit card required)

---

## Purpose

This document guides you through setting up EmailJS for FlashDeck.AI. EmailJS will power two contact forms on the website:

1. **Schools Partnership Enquiry Form** — on the `/schools` page, for head teachers and school staff enquiring about a pilot or partnership
2. **General Contact Form** — replacing the current `hello@flashdeck.ai` mailto link on the main website

When someone submits either form, two emails are triggered automatically:
- An **instant auto-reply** to the person who submitted the form, confirming receipt
- A **notification email** to `hello@flashdeck.ai` with all the details of the enquiry

---

## Step 1 — Create the EmailJS Account

1. Go to **https://www.emailjs.com**
2. Click **Sign Up**
3. Create a free account using the email address: `hello@flashdeck.ai`
4. Check the inbox for a verification email and confirm the account

---

## Step 2 — Connect the Email Service

1. In the EmailJS dashboard, click **Email Services** in the left sidebar
2. Click **Add New Service**
3. Select **Gmail** (if `hello@flashdeck.ai` is a Google Workspace / Gmail account)
4. Click **Connect Account** and sign in with the `hello@flashdeck.ai` Google account when prompted
5. In the **Service Name** field, enter: `flashdeck_contact`
6. Click **Create Service**
7. **Note down the Service ID** — it will look like `service_xxxxxxx`

---

## Step 3 — Create Email Template 1: Schools Enquiry Auto-Reply

This email is sent automatically to the person who submits the schools enquiry form.

1. In the EmailJS dashboard, click **Email Templates** → **Create New Template**
2. Fill in the template as follows:

| Field | Value |
|---|---|
| **Template Name** | `schools_enquiry_autoreply` |
| **To Email** | `{{to_email}}` |
| **From Name** | `FlashDeck.AI` |
| **Reply To** | `hello@flashdeck.ai` |
| **Subject** | `Thank you for your enquiry — FlashDeck.AI Schools Partnership` |

3. In the **Body** (HTML or plain text), paste the following:

```
Dear {{from_name}},

Thank you for getting in touch about FlashDeck.AI for your school.

We have received your enquiry and a member of our team will be in touch within 2 working days to discuss how FlashDeck can support your students.

In the meantime, you can learn more about our schools offering at:
https://www.flashdeck.ai/schools

Best regards,
The FlashDeck.AI Team
hello@flashdeck.ai
www.flashdeck.ai
```

4. Click **Save**
5. **Note down the Template ID** — it will look like `template_xxxxxxx`

---

## Step 4 — Create Email Template 2: Schools Enquiry Notification

This email is sent to `hello@flashdeck.ai` whenever a schools enquiry is submitted, containing all the details.

1. Click **Email Templates** → **Create New Template**
2. Fill in the template as follows:

| Field | Value |
|---|---|
| **Template Name** | `schools_enquiry_notification` |
| **To Email** | `hello@flashdeck.ai` |
| **From Name** | `FlashDeck.AI Website` |
| **Reply To** | `{{from_email}}` |
| **Subject** | `New Schools Enquiry — {{school_name}}` |

3. In the **Body**, paste the following:

```
New schools partnership enquiry received on FlashDeck.AI:

Name:               {{from_name}}
Email:              {{from_email}}
Role:               {{role}}
School / Institution: {{school_name}}
Country:            {{country}}
Number of Students: {{student_count}}

Message:
{{message}}

---
Submitted via: https://www.flashdeck.ai/schools
```

4. Click **Save**
5. **Note down the Template ID**

---

## Step 5 — Create Email Template 3: General Contact Auto-Reply

This email is sent automatically to anyone who submits the general contact form on the website.

1. Click **Email Templates** → **Create New Template**
2. Fill in the template as follows:

| Field | Value |
|---|---|
| **Template Name** | `general_contact_autoreply` |
| **To Email** | `{{to_email}}` |
| **From Name** | `FlashDeck.AI` |
| **Reply To** | `hello@flashdeck.ai` |
| **Subject** | `We've received your message — FlashDeck.AI` |

3. In the **Body**, paste the following:

```
Hi {{from_name}},

Thank you for getting in touch with FlashDeck.AI.

We have received your message and will get back to you within 2 working days.

Best regards,
The FlashDeck.AI Team
hello@flashdeck.ai
www.flashdeck.ai
```

4. Click **Save**
5. **Note down the Template ID**

---

## Step 6 — Create Email Template 4: General Contact Notification

This email is sent to `hello@flashdeck.ai` whenever someone submits the general contact form.

1. Click **Email Templates** → **Create New Template**
2. Fill in the template as follows:

| Field | Value |
|---|---|
| **Template Name** | `general_contact_notification` |
| **To Email** | `hello@flashdeck.ai` |
| **From Name** | `FlashDeck.AI Website` |
| **Reply To** | `{{from_email}}` |
| **Subject** | `New Contact Form Submission — {{from_name}}` |

3. In the **Body**, paste the following:

```
New contact form submission received on FlashDeck.AI:

Name:    {{from_name}}
Email:   {{from_email}}
Subject: {{subject}}

Message:
{{message}}

---
Submitted via: https://www.flashdeck.ai
```

4. Click **Save**
5. **Note down the Template ID**

---

## Step 7 — Get the Public Key

1. In the EmailJS dashboard, click **Account** in the top-right corner
2. Click **General**
3. Find the section labelled **Public Key**
4. **Note down the Public Key** — it will look like a string of random characters, e.g. `AbCdEfGhIjKlMnOpQrSt`

---

## Step 8 — Share the Details

Once all steps above are complete, please share the following information with Zulfi so the development team can integrate EmailJS into the website:

| Item | Your Value |
|---|---|
| **Service ID** | *(e.g. service_xxxxxxx)* |
| **Schools Enquiry Auto-Reply Template ID** | *(e.g. template_xxxxxxx)* |
| **Schools Enquiry Notification Template ID** | *(e.g. template_xxxxxxx)* |
| **General Contact Auto-Reply Template ID** | *(e.g. template_xxxxxxx)* |
| **General Contact Notification Template ID** | *(e.g. template_xxxxxxx)* |
| **Public Key** | *(e.g. AbCdEfGhIjKlMnOpQrSt)* |

---

## Notes

- The EmailJS **free tier** allows 200 emails per month, which is sufficient for the initial launch period. If enquiry volumes grow, the paid plan starts at $15/month for 1,000 emails.
- Do **not** share the Public Key publicly in any document other than the website code — it is safe to include in front-end code but should not be posted on social media or public forums.
- If `hello@flashdeck.ai` is not a Gmail/Google Workspace account, please let the development team know which email provider is used (e.g. Outlook, Zoho, custom SMTP) so the correct service type can be selected in Step 2.
