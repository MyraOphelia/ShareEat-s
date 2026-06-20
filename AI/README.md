# ShareEat — AI Help Chat

Developer documentation for the **AI Help Chat** module on buyer-facing pages.

Main project overview → **[README.md](../README.md)**

---

## Start here

| Doc | Purpose |
|-----|---------|
| **[HOW-IT-WORKS.md](./HOW-IT-WORKS.md)** | Architecture, response pipeline, config, security, testing |
| **[AI-ASSISTANT.md](./AI-ASSISTANT.md)** | Feature reference, database schema, storage keys, page integration |
| **[AI-ARCHITECTURE-DIAGRAM.HTML](./AI-ARCHITECTURE-DIAGRAM.HTML)** | Visual layered architecture — open in browser |

---

## Quick reference

**Pipeline:** Rules → FAQ (`chat_knowledge`) → Live listings → Groq LLM (`ai_chat` Edge Function)

**Client:** `js/chat-widget.js` · `chat-widget.css`

**Secrets:** `LLM_API_KEY`, `LLM_BASE_URL`, `LLM_MODEL` on Supabase Edge Function (never in frontend)

**Admin:** `admin-chat-logs.html` — CSAT, escalations, unmatched queries

---

<p align="center"><a href="../README.md">← Back to main README</a></p>
