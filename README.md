# Automations — Gran Hotel Peñíscola

Make.com automation flows for Gran Hotel Peñíscola, built and maintained by César Beltran Miralles.

## Flows

| # | Folder | Description | Status |
|---|--------|-------------|--------|
| 1 | [`flujo1-bienvenida-whatsapp/`](flujo1-bienvenida-whatsapp/) | Automatic WhatsApp welcome message on Booking.com reservation confirmation | Ready to deploy |
| 2 | [`flujo2-upselling-prellegada/`](flujo2-upselling-prellegada/) | AI-personalized WhatsApp upselling 3 days before check-in | Ready to deploy |
| 3 | _(coming soon)_ | Post-checkout survey + win-back | Planned |

## How to use

Each folder contains:
- `blueprint.json` — import directly into Make.com (Scenarios → `...` → Import Blueprint)
- `setup.md` — step-by-step activation guide
- Additional files specific to each flow (templates, schemas, etc.)

## Stack

- **Orchestration:** Make.com (Core plan)
- **AI extraction:** Gemini 1.5 Flash (Google AI Studio)
- **Messaging:** WhatsApp Business API via 360dialog
- **Data storage:** Google Sheets
- **Email:** Gmail (hotel account)
