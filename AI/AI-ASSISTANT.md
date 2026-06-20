# ShareEat AI Assistant – Documentation

**System prompt (for LLM / Cursor):** kept in the private source repository (`SHAREEAT_AI_SYSTEM_PROMPT.md`).

Floating chat widget (bottom-right) that helps users with ShareEat FAQs, support, and live listing data.

---

## Overview

| Item | Details |
|------|---------|
| **Files** | `js/chat-widget.js`, `chat-widget.css` |
| **Pages** | user-home, map, bag, profile, checkout, shop-detail, user-listing-detail, pickup |
| **Type** | Rule-based (no external AI API by default) |
| **Storage** | `localStorage` + `sessionStorage` for history; Supabase for messages & escalations |

---

## Extended Features (Post 1–20)

### Intelligence & Personalization

| Feature | Description |
|---------|-------------|
| **Order history awareness** | "You ordered from GreenBowl last week, want to try again?" when listing results include past stores |
| **Dietary preference memory** | `vegetarian`, `halal`, `vegan`, `nut-free` saved in `localStorage`; filters listings automatically |
| **Intent confidence** | Generic fallback shows 3 clarifying quick-reply buttons: "List free food", "Where's my order?", "I need help" |
| **Unmatched query logging** | Unmatched messages logged to `chat_unmatched` for knowledge base improvement |

### Transaction & Order Support

| Feature | Description |
|---------|-------------|
| **Real-time order status** | "Where's my order?" fetches live status from Supabase |
| **Reorder shortcut** | "Reorder my last pickup" adds last completed order to bag |
| **Cart assistant** | On bag page, "expiring soon" / "closing soon" triggers pickup time warnings |
| **Promo code checker** | User types a code; bot validates via `chat_check_promo_code` RPC |

### Discovery & Map Integration

| Feature | Description |
|---------|-------------|
| **Location-aware listings** | "Show food near me" uses geolocation + distance sort |
| **Filter combos** | "Show free halal food near Bangsar" — multi-filter in one message |
| **Store hours awareness** | Listings with pickup window closing within 1 hour show ⚠ Pickup closing soon! |

### UX & Accessibility

| Feature | Description |
|---------|-------------|
| **Voice input** | 🎤 button uses Web Speech API for hands-free queries |
| **Chat minimize memory** | `sessionStorage` remembers if user closed chat; doesn't re-pop |
| **Accessibility mode** | ♿ button toggles high contrast + larger text |
| **Slash commands** | `/free`, `/help`, `/escalate`, `/status`, `/reorder` |

### Analytics & Admin

| Feature | Description |
|---------|-------------|
| **Unmatched heatmap** | `chat_unmatched` table logs unanswered queries |
| **CSAT dashboard** | Admin Chat Logs → CSAT tab: thumbs up/down counts and satisfaction rate |
| **Escalation SLA** | Admin Chat Logs → Escalations tab: flags escalations older than 24h |
| **Unmatched view** | Admin Chat Logs → Unmatched tab: browse logged queries |

### AI Upgrade Path

| Feature | Description |
|---------|-------------|
| **Sentiment detection** | Negative/urgent keywords → auto-escalate with reference number |
| **AI API placeholder** | Set `window.SHAREEAT_AI_API_URL` POST `{ message, history, context, systemPrompt? }`. Optional `window.SHAREEAT_AI_SYSTEM_PROMPT` (see `SHAREEAT_AI_SYSTEM_PROMPT.md`) |
| **Settings panel** | ⚙ Text size S/M/L, high contrast, read-aloud (TTS), reduce motion, assistant language (STT + RTL for Arabic) |
| **Voice** | Live transcript above input while listening; auto-stop ~12s |
| **Thumbs down** | Triggers clarifying follow-up message in-thread |

---

## Features (1–20)

| # | Feature | Description |
|---|---------|-------------|
| 1 | **Typing delay** | Typing indicator appears only after ~300ms |
| 2 | **Sound / vibration** | Optional sound + vibration on new bot message (disable: `localStorage.setItem('shareeat_chat_sound','0')`) |
| 3 | **Unread badge** | Red dot/count on bubble when chat is closed and there are new messages |
| 4 | **Animation** | Smoother open/close transitions |
| 5 | **Mobile full-screen** | Full-screen chat on viewports ≤480px |
| 6 | **Context-aware** | Uses current page (bag, checkout, pickup) to tailor replies |
| 7 | **FAQ links** | Links to Profile → Orders in refund/order replies |
| 8 | **AI API placeholder** | Set `window.SHAREEAT_AI_API_URL` for future API integration |
| 9 | **Suggested follow-ups** | 1–2 quick buttons after each bot reply |
| 10 | **Multi-language** | Uses `ShareEatLang.getLang()` (EN, BM, ZH, TA) |
| 11 | **Reference storage** | Escalations saved to `chat_escalations` with reference numbers |
| 12 | **Satisfaction rating** | Thumbs up/down on bot messages; stored via `update_chat_satisfaction` RPC |
| 13 | **Admin view** | `admin-chat-logs.html` – browse chat logs (admin login required) |
| 14 | **Email transcript** | “Yes, email transcript” follow-up with support@shareeat.my instructions |
| 15 | **Proactive greeting** | Empty state shows “Hi! How can I help you today?” |
| 16 | **Store-specific** | “Ask about this store” quick prompt on shop/listing pages |
| 17 | **Post-order check-in** | Proactive “How was your pickup?” on pickup page when user has active order |
| 18 | **Offline fallback** | “Chat temporarily unavailable” when offline |
| 19 | **Session continuity** | `sessionStorage` + `localStorage` for history across navigations |
| 20 | **Rate limiting** | Max 10 messages per minute |

---

## Live Listing Queries

The assistant fetches real listings from Supabase when users ask:

| Query type | Trigger keywords | Behavior |
|------------|------------------|----------|
| **Free** | free, gratis, free food, list free | Shows listings where `is_donation` or price = 0 |
| **Cheap** | cheap, cheapest, affordable, budget | Shows listings sorted by price (lowest first) |
| **All** | list, show, find, browse, recommend, suggest | Shows up to 8 active listings |

**Quick prompts:** “List free food”, “Show cheap options”

**Output format:** Store name, title, price, and a **View** link to `user-listing-detail.html?id=...`

---

## Support Topics (Rule-Based)

| Topic | Example triggers | Response |
|-------|------------------|----------|
| Refunds | refund, money back, compensate | Asks for order number; links to Profile → Orders |
| Missed/wrong order | missed order, never got, missing item | Asks for order number + store name |
| Account / login | account, login, forgot password | Suggests Forgot password; asks for error details |
| Store complaint | store complaint, bad store | Asks for store name + order number; escalates |
| App issues | app broken, error, bug | Asks which page and what happens |
| Escalation | escalate, human, real person | Logs case; returns reference number; offers email transcript |

---

## Knowledge Base (ShareEat)

| Question | Example triggers |
|----------|------------------|
| What is ShareEat? | what is shareeat, what's shareeat |
| Why does it exist? | why shareeat, why does it exist |
| When can I collect? | when can i collect, when do users |
| How does it work? | how does it work, how do i order |
| How much does it cost? | how much, cost, price |
| Discounts / promos | discount, promo, promotion, code |
| Orders / bag / checkout | order, buy, bag, checkout |
| Pickup | pickup, collect, when, where |
| Surprise bags | waste, surplus, surprise bag |

---

## Database Schema

### `chat_messages` (Supabase)

| Column | Type | Description |
|--------|------|-------------|
| id | uuid | Primary key |
| user_id | uuid | Optional; from auth |
| role | text | `user` \| `assistant` |
| message | text | Message content |
| page_url | text | Page where message was sent |
| client_ref | text | Client-generated ref for satisfaction |
| satisfaction | text | `up` \| `down` (thumbs) |
| created_at | timestamptz | Timestamp |

### `chat_escalations` (Supabase)

| Column | Type | Description |
|--------|------|-------------|
| id | uuid | Primary key |
| reference_number | text | e.g. `SE2K9XZ` |
| user_id | uuid | Optional |
| user_message | text | User message at escalation |
| page_url | text | Page URL |
| created_at | timestamptz | Timestamp |

### RPC: `update_chat_satisfaction(p_client_ref, p_satisfaction)`

Updates `satisfaction` on `chat_messages` where `client_ref` matches and row belongs to caller.

### `chat_unmatched` (Supabase)

| Column | Type | Description |
|--------|------|-------------|
| id | uuid | Primary key |
| user_id | uuid | Optional |
| user_message | text | Unmatched user query |
| page_url | text | Page where query was sent |
| created_at | timestamptz | Timestamp |

**SQL:** `supabase-chat-unmatched.sql`

### RPC: `chat_check_promo_code(p_code text)`

Returns `{ valid, code, title, discount_type, discount_value, message }` for promo validation. **SQL:** `supabase-chat-promo-check.sql`

---

## Context (`window.shareeatChatContext`)

Pages can set context for tailored replies:

| Property | Set by | Use |
|----------|--------|-----|
| `storeName` | shop-detail.js, user-listing-detail.js | Store-specific help; “Ask about this store” |
| `listingId` | user-listing-detail.js | Listing context |
| `hasActiveOrder` | pickup.js | Post-order check-in; proactive “How was your pickup?” |
| `page` | Auto from pathname | Context-aware replies (bag, checkout, etc.) |

---

## Storage Keys

| Key | Purpose |
|-----|---------|
| `shareeat_chat_history` | Message history (localStorage) |
| `shareeat_chat_session` | Session history (sessionStorage) |
| `shareeat_chat_unread` | Unread count (sessionStorage) |
| `shareeat_chat_sound` | `0` = sound off |
| `shareeat_chat_rate` | Rate limit state (sessionStorage) |
| `shareeat_chat_dietary` | Dietary prefs (localStorage): `["halal","vegan"]` |
| `shareeat_chat_minimized` | User closed chat (sessionStorage) |
| `shareeat_chat_access` | Accessibility mode on (sessionStorage) |

---

## Adding a Page

1. Include CSS: `<link rel="stylesheet" href="chat-widget.css" />`
2. Include JS (after Supabase, i18n): `<script src="js/chat-widget.js"></script>`
3. Optional: set `window.shareeatChatContext` before chat loads for context-aware replies.

---

## Admin Chat Logs

- **URL:** `admin-chat-logs.html`
- **Access:** Admin login required (`profiles.role = 'admin'`)
- **RLS:** Policy “Admins can read all chat messages” in `supabase-chat-extras.sql`

---

## Future AI API

To plug in an external AI API:

1. Set `window.SHAREEAT_AI_API_URL` to your endpoint.
2. Implement `getAiReplyFromApi()` in `chat-widget.js` to call it.
3. API should accept `{ message, history, context }` and return `{ reply }`.

---

## i18n Keys (js/i18n.js)

| Key | Purpose |
|-----|---------|
| chat_assistant | Header title |
| chat_clear | Clear button |
| chat_placeholder | Input placeholder |
| chat_empty_greeting | Empty state text |
| chat_unavailable | Offline message |
| chat_ask_store | “Ask about this store” |
| chat_post_order | Post-order check-in |
| chat_rate_helpful | Satisfaction label |
| chat_profile_orders | Profile → Orders link text |
| chat_email_transcript | Email transcript option |
