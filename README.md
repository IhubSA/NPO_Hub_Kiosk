# CIMP — Community Integrated MEL Platform (Demo)

*People • Evidence • Greater Impact*

A monitoring, evaluation and learning (MEL) web app for NPO Community Hubs: tablet kiosk sign-in, simple service recording for Community Liaison Officers, visitor feedback, management dashboards, donor impact reports and PDF/Excel exports. It is multi-organisation (SaaS-ready) and designed around POPIA.

Hosting: **GitHub Pages** (static files) + **Supabase** — its own stand-alone project **NPO Community Hub** (`psyzuhxkrzbgpxlqfpdl`, eu-west-1), schema `npo_hub`. It has its own logins and emails, separate from every other app.
Built by iHubSA.

## Files

| File | Purpose |
|---|---|
| `index.html` | Staff, management, donor and admin app (sign-in required) |
| `kiosk.html` | Tablet kiosk for visitors (no sign-in; uses a secret per-Hub kiosk link) |
| `config.js` | Supabase URL, publishable key and schema name |
| `kiosk.webmanifest` | Lets the tablet install the kiosk as a full-screen app from its home screen |
| `cimp-logo.png`, `cimp-mark.png` | CIMP logo (sign-in page) and emblem (sidebar, PDF reports, app icon) |
| `sql/01_schema.sql` | Tables, roles, Row Level Security, audit log, reporting view |
| `sql/02_functions.sql` | Kiosk, registration, donor-impact and POPIA functions |
| `sql/03_demo_seed.sql` | Fictional demo data (re-runnable; replaces the demo organisation) |
| `sql/06_towns.sql` | Towns per province; the kiosk always offers the Hub's own town (run after 05) |
| `sql/05_feedback_by_mobile.sql` | Lets visitors find their visit for feedback using just their mobile number (run after 04) |
| `sql/04_hub_town_logo.sql` | Hub towns, kiosk logo storage bucket and logo support in the kiosk (run after 03) |

The SQL has already been applied to the NPO Community Hub Supabase project. The files are kept here as a record and for setting up a new project.

## One-time Supabase setting

Supabase dashboard → **Project Settings → Data API → Exposed schemas** → add `npo_hub` → Save.
Without this the app shows "schema not exposed" / "Invalid schema".

## Deploy to GitHub Pages

1. Create a new repository (e.g. `npo-hub-demo`).
2. Upload `index.html`, `kiosk.html`, `config.js`, `kiosk.webmanifest`, `cimp-logo.png`, `cimp-mark.png` and `README.md` (the `sql` folder is optional).
3. Repository → Settings → Pages → Source: *Deploy from a branch* → `main` / root → Save.
4. Open `https://<owner>.github.io/npo-hub-demo/`.
5. Supabase → Authentication → URL Configuration → add the Pages URL to **Redirect URLs** (needed for sign-up confirmation and password-reset emails).

## First sign-in

The Super Administrator (Brent) is already invited by email. Open the app, choose **Create your account** with that email, confirm via the email link, then sign in.
Further users are added under **Administration → Users & access**, and then they create their account the same way.

## Provinces, towns, Hubs and communities

- **Administration → Provinces & towns** — where the Hubs are: Province → Town → Hub. Add towns and Hubs per province; used to filter the dashboard and reports.
- **Administration → Communities** — where visitors live: the quick-tap answers to "Where do you live?" on each Hub's kiosk, grouped by Hub (or shown on all Hubs). Each kiosk automatically includes its own Hub's town. These feed the *Community reach* figures.

## Privacy notice per Hub

The privacy notice (Administration → Settings & POPIA) is written once for the organisation, with placeholders that each kiosk fills in for its own Hub: `{hub}` Hub name, `{town}` Hub town, `{organisation}` organisation name and `{retention}` retention period. The settings page shows a live preview for any Hub. Raise the consent version whenever the wording changes.

## Hub logos

Administration → Hubs & kiosks → **Edit** → **Upload logo** (PNG, JPG or WebP, max 1 MB, square works best). The logo replaces the Hub's initials on its kiosk. Logos are stored in the public Supabase Storage bucket `hub-logos`; only Super Administrators and Management can upload or remove them.

## Setting up a Hub tablet

A kiosk remembers the last Hub link opened on that device. To check or change it, open `kiosk.html?setup` (the *kiosk set-up page* link on the sign-in screen), or tap the Hub logo on the kiosk 5 times. To view a particular Hub's kiosk from a computer, use **Administration → Hubs & kiosks → Open kiosk**.


1. In the app: **Administration → Hubs & kiosks → Copy link** for the Hub.
2. Open that link once on the tablet (Chrome on Android or Safari on iPad). The tablet remembers the Hub and removes the code from the address bar.
3. Add the page to the home screen (Chrome: ⋮ → *Add to Home screen* / *Install app*; iPad Safari: Share → *Add to Home Screen*). It then opens full-screen like an app. Use a tablet stand in landscape. For a locked-down kiosk, use Android *Screen pinning* or iPad *Guided Access*.
4. Hidden staff menu: tap the Hub logo (top left) 5 times quickly to change or remove the kiosk.
5. Lost tablet? Click **New link** for that Hub. The old link stops working.

## How the visitor journey works

Welcome → privacy notice & consent → name + mobile → (new visitors only) age group, gender, community, email → services (multi-select, plus "not offered here" options for unmet needs) → "You're signed in" → screen clears automatically.
The visitor's name appears under **Today's Visitors**. The Community Liaison Officer clicks the name, marks each service as **Provided**, **Referred** (to which organisation), **Follow-up needed** or **Couldn't help**, can add other services they provided and a note, and saves. The visitor then leaves the list (recorded visits stay under *Recorded today*; follow-ups are listed separately). Feedback is then collected by either:
- **Open feedback on this device** (hands the staff device to the visitor), or
- on the kiosk: *Give feedback* → the mobile number they signed in with. This opens their most recent visit to that Hub in the last 3 days that has no feedback yet. For privacy, the services they received are not shown on screen. *Ticket number + last 4 digits* is still available as a fallback.

## Roles

| Role | Access |
|---|---|
| Super Administrator | Everything, including settings & retention |
| Management | All Hubs, dashboards, reports, admin |
| Hub Manager | Their Hubs: today's visitors, visitor records, dashboards, reports, kiosk links |
| Hub Staff | Today's Visitors and visitor records at their Hubs |
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

Organisation **Siyakhula Community Trust (Demo)** with 4 hubs in 4 towns across 2 provinces (all 9 provinces are set up), 9 service categories, 8 programmes, 3 donors with 6 funding allocations, ~540 fictional visitors, ~1,000 visits and ~470 feedback responses over the last 6 months. All names and numbers are invented (mobiles `0710000001`–`0710000540`).
To remove it: `delete from npo_hub.organisations where slug = 'demo-npo';`

## Not yet included (next phases)

- Email notifications to staff (Resend + Supabase Edge Function)
- Self-service onboarding for new NPOs (SaaS sign-up and billing)
- Separate branded donor portal domain, long-term outcome tracking, AI narrative reports
- Dark mode
