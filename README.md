# Hotel Automations — GHP

Make.com automation flows for a hotel property, built with Gemini AI, WhatsApp Business API, Mailchimp, and Google Sheets. Each flow ships as an importable `blueprint.json` + a step-by-step `setup.md`.

---

## Roadmap

Flows are organized in three tiers by complexity. Each tier builds on the previous one — the Quick Wins create the data foundation that the intermediate and advanced flows consume.

### Tier 1 — Quick Wins

> Flows 1–2 require PMS→Mailchimp sync (already active at GHP via Acigrup). Flow 4 requires only a Claude browser extension.

| # | Flow | What it does | Stack | Status |
|---|------|-------------|-------|--------|
| 1 | [Bienvenida automática por WhatsApp](flujo1-bienvenida-whatsapp/) | When PMS Acigrup syncs a reservation to Mailchimp (subscribe or profile_update), instantly sends a personalized WhatsApp to the guest. Covers all OTAs connected to the PMS. Also handles modifications: re-sends only if check-in date, room type, or guest count changes. Writes every reservation to a central Sheets log that feeds Flow 2. | Make.com · Mailchimp · WhatsApp Business API · Sheets | **Ready** |
| 2 | [Upselling pre-llegada](flujo2-upselling-prellegada/) | 3 days before check-in, reads from the Sheets log populated by Flow 1 and uses Gemini to generate a personalized WhatsApp offering room upgrade, parking, spa, or dinner. Sector conversion rate: 8–12% on WhatsApp vs. 2–5% on email. Requires Flow 1 active for at least 1 week. | Make.com · Gemini · WhatsApp Business API · Sheets | **Ready** |
| 3 | Check-in online + registro policial | Sends the pre-check-in link 24h before arrival. When the guest completes the form, Make automatically formats the data for the Guardia Civil (Parte de Viajeros). Fewer queues at reception, zero missed filings. | Make.com · Google Forms / Typeform · Sheets | Planned |
| 4 | [Respuesta a reseñas con IA](flujo4-respuesta-resenas/) | Run the `/responder_reseñas` command in Claude (browser extension) on any review page (Google, TripAdvisor, Booking.com). The AI scans all unanswered reviews, analyses sentiment, and drafts a personalised ~120-word reply in the reviewer's language — ready to copy and publish. No Make.com required. | Claude (browser extension) | **Ready** |

---

### Tier 2 — Connect the PMS with Everything

> Requires read access to the PMS or OTA email/API feeds.

| # | Flow | What it does | Stack | Status |
|---|------|-------------|-------|--------|
| 5 | Centralizar reservas OTA en Sheets | Make automatically consolidates every new reservation from Booking.com, Expedia, and direct web into a single master sheet with occupancy, rates, and channel. Eliminates manual data entry. | Make.com · Booking.com API · Sheets / Excel | Planned |
| 6 | Gestión de no-shows y comisiones OTA | Detects cancellations and no-shows on Booking.com and auto-generates a report for the finance team listing reservations where OTA commission should not apply. Prevents direct revenue loss. | Make.com · Booking.com · Gmail / Sheets | Planned |
| 7 | Alerta y asignación de tareas de limpieza | On each check-out registration, Make notifies the housekeeping team via WhatsApp with room number and priority. When they finish, they mark the task as done. | Make.com · WhatsApp · Airtable / Notion | Planned |
| 8 | Análisis semanal de reseñas con IA | Every week, Make collects reviews from Booking and TripAdvisor, runs them through Gemini to detect patterns (recurring complaints, strengths), and sends a one-page report to the management team. | Make.com · Gemini API · Sheets | Planned |

---

### Tier 3 — AI as the Hotel's Engine

> Advanced flows. Require stable data pipelines from Tiers 1–2.

| # | Flow | What it does | Stack | Status |
|---|------|-------------|-------|--------|
| 9 | Chatbot 24/7 para web y WhatsApp | Virtual assistant trained on the hotel's full information (menus, hours, prices, policies). Answers FAQs in real time, frees reception from repetitive queries, and can close direct bookings. | Gemini API · Make.com · WhatsApp Business API | Planned |
| 10 | Emails VIP ultra-personalizados | Gemini analyzes a VIP guest's stay history and Make generates a fully personalized email before their next arrival, referencing their real preferences. | Gemini API · Make.com · CRM / Sheets | Planned |
| 11 | Encuesta post-estancia + win-back | On check-out, the system sends a short satisfaction survey. Low score → immediate alert to the manager. High score → a second flow automatically sends a loyalty offer for the next stay. | Make.com · Typeform · Gmail / WhatsApp | Planned |
| 12 | Dashboard de ocupación en tiempo real | Make aggregates data from all OTAs and the PMS into a dashboard (Google Looker Studio) with key metrics: occupancy, RevPAR, sales channels, trends. Auto-updates daily. | Make.com · Google Looker Studio · Sheets / Airtable | Planned |

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
