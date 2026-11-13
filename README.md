# NPO Community Hub — Visitor Management & Impact Measurement (Demo)

A web app for NPO Community Hubs: tablet kiosk sign-in, live service queue, visitor feedback, management dashboards, donor impact reports and PDF/Excel exports. It is multi-organisation (SaaS-ready) and designed around POPIA.

Hosting: **GitHub Pages** (static files) + **Supabase** (Angels Resource Centres project, schema `npo_hub`).
Built by iHubSA.

## Files

| File | Purpose |
|---|---|
| `index.html` | Staff, management, donor and admin app (sign-in required) |
| `kiosk.html` | Tablet kiosk for visitors (no sign-in; uses a secret per-Hub kiosk link) |
| `config.js` | Supabase URL, publishable key and schema name |
| `kiosk.webmanifest`, `kiosk-icon.svg` | Let the tablet install the kiosk as a full-screen app from its home screen |
| `sql/01_schema.sql` | Tables, roles, Row Level Security, audit log, reporting view |
| `sql/02_functions.sql` | Kiosk, registration, donor-impact and POPIA functions |
| `sql/03_demo_seed.sql` | Fictional demo data (re-runnable; replaces the demo organisation) |

The SQL has already been applied to the Supabase project. The files are kept here as a record and for setting up a new project.

## One-time Supabase setting

Supabase dashboard → **Project Settings → Data API → Exposed schemas** → add `npo_hub` → Save.
Without this the app shows "schema not exposed" / "Invalid schema".

## Deploy to GitHub Pages

1. Create a new repository (e.g. `npo-hub-demo`).
2. Upload `index.html`, `kiosk.html`, `config.js`, `kiosk.webmanifest`, `kiosk-icon.svg` and `README.md` (the `sql` folder is optional).
3. Repository → Settings → Pages → Source: *Deploy from a branch* → `main` / root → Save.
4. Open `https://<owner>.github.io/npo-hub-demo/`.
5. Supabase → Authentication → URL Configuration → add the Pages URL to **Redirect URLs** (needed for sign-up confirmation and password-reset emails).

## First sign-in

A Super Administrator is linked by email. Open the app, choose **Create your account** with the invited email, confirm via the email link, then sign in.
Further users are added under **Administration → Users & access**, and then they create their account the same way.

## Setting up a Hub tablet

1. In the app: **Administration → Hubs & kiosks → Copy link** for the Hub.
2. Open that link once on the tablet (Chrome on Android or Safari on iPad). The tablet remembers the Hub and removes the code from the address bar.
3. Add the page to the home screen (Chrome: ⋮ → *Add to Home screen* / *Install app*; iPad Safari: Share → *Add to Home Screen*). It then opens full-screen like an app. Use a tablet stand in landscape. For a locked-down kiosk, use Android *Screen pinning* or iPad *Guided Access*.
4. Hidden staff menu: tap the Hub logo (top left) 5 times quickly to change or remove the kiosk.
5. Lost tablet? Click **New link** for that Hub. The old link stops working.

## How the visitor journey works

Welcome → privacy notice & consent → name + mobile → (new visitors only) age group, gender, community, email → services (multi-select, plus "not offered here" options for unmet needs) → ticket number → screen clears automatically.
Staff see the visitor in the **Service Queue**, update each service (status, programme, staff member, service provided, referral, resolved, notes), then collect feedback by either:
- **Open feedback on this device** (hands the staff device to the visitor), or
- on the kiosk: *Give feedback* → ticket number + last 4 digits of their mobile.

## Roles

| Role | Access |
|---|---|
| Super Administrator | Everything, including settings & retention |
| Management | All Hubs, dashboards, reports, admin |
| Hub Manager | Their Hubs: queue, visitors, dashboards, reports, kiosk links |
| Hub Staff | Queue and visitors at their Hubs |
| Programme Manager | Data for their programmes, dashboards, reports |
| Donor Viewer | Only their own donor impact report — no beneficiary data |

## POPIA features

- Privacy notice and recorded consent (with version) for every registration
- Guardian consent required for under-18s; no email collected from minors
- Kiosk never shows stored personal data; auto-clears after each visitor and when idle
- Row Level Security limits beneficiary records to staff of the Hubs a visitor attended
- Donor and management reports are aggregates only
- Audit log of changes (personal fields are masked in the log)
- Anonymise a visitor on request; retention period with one-click anonymisation
- Offline kiosk queue stores registrations on the tablet only until they upload

## Demo data

Organisation **Siyakhula Community Trust (Demo)** with 4 hubs in 3 regions, 9 service categories, 8 programmes, 3 donors with 6 funding allocations, ~540 fictional visitors, ~1,000 visits and ~470 feedback responses over the last 6 months. All names and numbers are invented (mobiles `0710000001`–`0710000540`).
To remove it: `delete from npo_hub.organisations where slug = 'demo-npo';`

## Not yet included (next phases)

- Email notifications to staff (Resend + Supabase Edge Function)
- Self-service onboarding for new NPOs (SaaS sign-up and billing)
- Separate branded donor portal domain, long-term outcome tracking, AI narrative reports
- Dark mode
