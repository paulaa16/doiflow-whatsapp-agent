# doiflow-whatsapp-agent

A WhatsApp assistant for small tourism businesses. It answers availability questions and FAQs 24/7, using the business's own availability sheet as the source of truth. 

> **Status:** working prototype, used for demos and demand validation. Not production-hardened.

## Demo



https://github.com/user-attachments/assets/cc868929-8564-4967-9c66-0ac8d4b0c1c0



## How it works

```
Customer message on WhatsApp
        |
WhatsApp Cloud API -> webhook (Make)
        |
Only text messages continue
        |
Load this customer's conversation history (data store, keyed by phone number)
        |
Read availability from Google Sheets -> format as text
        |
LLM call (Anthropic API) with business data + history
        |
LLM returns JSON: { in_scope, category, answer }
        |
in_scope = true   -> send the answer on WhatsApp + save history
in_scope = false  -> send a handoff message ("I'll pass this to the team")
```

**Design choices**
- **Grounded answers.** The prompt forbids inventing prices, schedules or availability that are not in the sheet.
- **Scope flag.** When the model is unsure, it returns `in_scope: false` and a human takes over instead of the bot guessing.
- **Per-customer history.** Each exchange is appended to the customer's history so replies keep context.
- **Date handling.** The current date is injected into the prompt so "tomorrow" or "this weekend" resolve correctly.

The system prompt is written in Spanish.

## Expected Google Sheet columns (A to L)

Fecha, Categoría, Actividad, Horario, Plazas Totales, Plazas Ocupadas, Plazas Disponibles, Precio, Lugar, Época, URL reserva online, Nivel

## Setup

1. Import the blueprint into Make.
2. Replace the placeholders: `YOUR_WHATSAPP_ACCESS_TOKEN`, `YOUR_PHONE_NUMBER_ID`, `YOUR_ANTHROPIC_API_KEY`, `YOUR_GOOGLE_SHEET_ID`, and `[BUSINESS NAME]` in the system prompt.
3. Reconnect your Google account, create the data store (key: phone number, field: `history`) and point the WhatsApp webhook in Meta for Developers to your Make webhook.
4. Never commit real keys. Credentials were removed from this blueprint.

## Roadmap

- Migrate from Make to self-hosted n8n to cut per-operation costs.
- Phase 2: automatic appointment reminders.
