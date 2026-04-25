# Hotel Automations — GHP

Make.com automation flows for a hotel property, built with Gemini AI, WhatsApp Business API, Mailchimp, and Google Sheets. Each flow ships as an importable `blueprint.json` + a step-by-step `setup.md`.

> **Context — April 2026:** Dani (Departamento Comercial, GHP) confirmed the existing tech stack in place. Flows marked **✅ In PMS** or **✅ In Mailchimp** are already covered by existing tools and do not need to be built. The flows marked **Ready** or **Planned** are the automation opportunities.

---

## What GHP already has in place

| Capability | Tool | Notes from Dani |
|------------|------|-----------------|
| Reservation consolidation (all OTAs) | PMS Acigrup | All OTAs centralised in PMS — no external sheet needed |
| No-show & commission reports | PMS Acigrup | Handled via PMS reports |
| Online check-in (email link) | PMS Acigrup | Automated from the PMS directly |
| Guardia Civil registration (Parte de Viajeros) | PMS Acigrup | Automated from the PMS directly |
| Occupancy dashboard (RevPAR, channels) | PMS Acigrup | Available in PMS — no external dashboard needed |
| Post-stay satisfaction survey + win-back | Mailchimp | 3-face sentiment widget (😠 / 😐 / 😊). Low or neutral → redirects to internal survey. Positive → redirects to Google review |
| Pre-arrival upselling — **email** | Mailchimp | Email upselling already running via Mailchimp campaigns |
| Mailchimp audience segments | Mailchimp | Segments by age, occupation type, etc. (seniors, young couples, families) — used for targeted campaigns |
| Chatbot (web + booking engine) | External provider | External chatbot connected to the booking engine; WhatsApp connection available. Quality improving as provider adds AI to multi-response flows |
| PMS → Mailchimp sync | Acigrup + Mailchimp | Every new reservation and modification is synced automatically — this is the data bridge used by Flows 1 & 2 |

---

## Roadmap

Flows are organised in three tiers by complexity. Each tier builds on the previous one — the Quick Wins create the data foundation that the intermediate and advanced flows consume.

### Tier 1 — Quick Wins

> Flows 1–2 require PMS→Mailchimp sync (already active at GHP via Acigrup). Flow 4 requires only a Claude browser extension.

| # | Flow | What it does | Stack | Status |
|---|------|-------------|-------|--------|
| 1 | [Bienvenida automática por WhatsApp](flujo1-bienvenida-whatsapp/) | When PMS Acigrup syncs a reservation to Mailchimp (subscribe or profile_update), instantly sends a personalized WhatsApp to the guest. Covers all OTAs connected to the PMS. Also handles modifications: re-sends only if check-in date, room type, or guest count changes. Writes every reservation to a central Sheets log that feeds Flow 2. | Make.com · Mailchimp · WhatsApp Business API · Sheets | **Ready** |
| 2 | [Upselling pre-llegada](flujo2-upselling-prellegada/) | 3 days before check-in, reads from the Sheets log populated by Flow 1 and uses Gemini to generate a personalized WhatsApp offering room upgrade, parking, spa, or dinner. **Email upselling already runs via Mailchimp** — this flow adds the WhatsApp channel (sector conversion 8–12% vs. 2–5% on email). Requires Flow 1 active for at least 1 week. | Make.com · Gemini · WhatsApp Business API · Sheets | **Ready** |
| 3 | Check-in online + registro policial | ~~Sends the pre-check-in link 24h before arrival and formats data for the Guardia Civil.~~ | — | **✅ In PMS** — online check-in (email) and Guardia Civil registration (Parte de Viajeros) are both fully automated from PMS Acigrup |
| 4 | [Respuesta a reseñas con IA](flujo4-respuesta-resenas/) | Run the `/responder_reseñas` command in Claude (browser extension) on any review page (Google, TripAdvisor, Booking.com). The AI scans all unanswered reviews, analyses sentiment, and drafts a personalised ~120-word reply in the reviewer's language — ready to copy and publish. Replaces the current manual workflow (staff copy reviews into ChatGPT). | Claude (browser extension) | **Ready** |

---

### Tier 2 — Connect the PMS with Everything

> Requires read access to the PMS or OTA email/API feeds.

| # | Flow | What it does | Stack | Status |
|---|------|-------------|-------|--------|
| 5 | Centralizar reservas OTA en Sheets | ~~Make consolidates every new reservation from Booking.com, Expedia, and direct web into a single master sheet.~~ | — | **✅ In PMS** — all OTA reservations already consolidated in PMS Acigrup |
| 6 | Gestión de no-shows y comisiones OTA | ~~Detects cancellations and no-shows and auto-generates a finance report.~~ | — | **✅ In PMS** — covered by existing PMS reports |
| 7 | Alerta y asignación de tareas de limpieza | Real-time room-by-room checkout alerts to housekeeping via WhatsApp. Note: GHP does a **mass checkout each morning** (443 rooms) and housekeeping teams monitor hallways directly — per-room WhatsApp alerts are not compatible with this workflow as currently structured. | Make.com · WhatsApp · Airtable / Notion | **Not applicable** — workflow incompatible with 443-room mass checkout |
| 8 | Análisis semanal de reseñas con IA | Every week, Make collects reviews from Booking and TripAdvisor, runs them through Gemini to detect patterns (recurring complaints, strengths), and sends a one-page report to the management team. Currently done manually: staff copy reviews into ChatGPT to generate the report. | Make.com · Gemini API · Sheets | Planned |

---

### Tier 3 — AI as the Hotel's Engine

> Advanced flows. Require stable data pipelines from Tiers 1–2.

| # | Flow | What it does | Stack | Status |
|---|------|-------------|-------|--------|
| 9 | Chatbot 24/7 para web y WhatsApp | Virtual assistant that answers FAQs and can close direct bookings. GHP already has an **external chatbot connected to the booking engine** with a WhatsApp integration option. Quality was initially limited but is improving as the provider adds AI to multi-response flows. Evaluate upgrading existing solution before building from scratch. | External provider (already contracted) · WhatsApp Business API | **✅ Partial** — existing chatbot; reassess WhatsApp quality in Q3 2026 |
| 10 | Emails VIP ultra-personalizados | Gemini analyses a VIP guest's stay history and Make generates a fully personalised email. **Data limitation:** only room type, regime, and dates are tracked per guest — bar and restaurant consumption is aggregated, not itemised. Personalisation depth is therefore limited to stay pattern and segment. GHP has Mailchimp segments by age and occupation type (seniors, young couples, families) which can be used as a proxy. | Gemini API · Make.com · Mailchimp (segments) | Planned — limited by available guest data |
| 11 | Encuesta post-estancia + win-back | Post-checkout satisfaction survey with automated follow-up. **Already in place via Mailchimp:** 3-face sentiment widget (angry / neutral / happy). Low or neutral score → redirects to internal survey. Positive score → redirects to Google review. | — | **✅ In Mailchimp** — fully operational |
| 12 | Dashboard de ocupación en tiempo real | Make aggregates data from all OTAs and the PMS into a dashboard (Google Looker Studio) with key metrics: occupancy, RevPAR, sales channels, trends. | — | **✅ In PMS** — occupancy dashboard already available in PMS Acigrup |

---

## How to use

Each flow folder contains:
- `blueprint.json` — import directly into Make.com via Scenarios → `...` → **Import Blueprint**
- `setup.md` — step-by-step activation guide (credential setup, testing, production checklist)
- Additional files per flow (WhatsApp templates, Sheets schemas, prompt files)

Flow 1 must be activated first — it populates the Sheets log that Flow 2 reads. Flows 3–12 build incrementally on top — activate in order for the smoothest rollout.

---

## Stack

| Layer | Tool |
|-------|------|
| Orchestration | Make.com (Core plan, ~10 €/month) |
| Reservation data source | Mailchimp (PMS Acigrup syncs via webhook) |
| AI — message generation | Gemini 1.5 Flash via Google AI Studio |
| Messaging | WhatsApp Business API via 360dialog |
| Data storage | Google Sheets (source of truth for all flows) |
| Email | Gmail (hotel account) |
| Forms | Google Forms / Typeform |
| Dashboard | Google Looker Studio |
| Task management | Airtable / Notion (Tier 2+) |

---

*Built by César Beltran Miralles · April 2026*
