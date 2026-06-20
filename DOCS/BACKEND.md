# ShareEat – Backend Architecture

This document explains the backend design and technology choices for ShareEat.

---

## 1. Backend Stack

| Component   | Technology                         | Purpose                              |
|-------------|------------------------------------|--------------------------------------|
| **Database**| Supabase (PostgreSQL)              | Tables, RLS, triggers, RPCs          |
| **Auth**    | Supabase Auth                     | Email/password, Google OAuth         |
| **Storage** | Supabase Storage                  | Listing images                       |
| **Serverless** | Supabase Edge Functions (optional) | Future webhooks, cron, Stripe, etc.  |

**No custom API server.** The frontend talks to Supabase directly from the browser.

---

## 2. Why Supabase?

- **Single platform** – Auth, DB, Storage, and Edge Functions in one place  
- **Row Level Security (RLS)** – Access control in the database, not in app code  
- **No hosting needed** – No separate Node/Python server to deploy or maintain  
- **Good for FYP** – Fast to build and easy to demo  

---

## 3. Data Flow

```
Browser (HTML/JS)
       │
       │  Supabase JS client
       ▼
┌──────────────────────────────────────┐
│  Supabase                            │
│  ├── Auth (login, sessions)          │
│  ├── Postgres (profiles, listings,   │
│  │             orders, etc.)         │
│  ├── RLS policies (who can read/     │
│  │   write what)                     │
│  ├── Storage (images)                │
│  └── Edge Functions (optional)       │
└──────────────────────────────────────┘
```

---

## 4. Supabase Edge Functions (Optional)

Edge Functions are Deno-based serverless functions hosted by Supabase. Use them for:

- Payment webhooks (Stripe)
- Scheduled jobs (cron)
- External API calls that need server-side secrets
- Logic that doesn’t fit well in RLS or RPCs

### Included Template: `health`

The project includes a simple health check function at `supabase/functions/health/`.

**Deploy when needed:**

1. Install Supabase CLI: `npm install -g supabase`
2. Link project: `supabase link`
3. Deploy: `supabase functions deploy health`
4. Test: `curl https://YOUR_PROJECT.supabase.co/functions/v1/health`

**Call from frontend (optional):**

```javascript
const res = await fetch(
  SUPABASE_URL + '/functions/v1/health',
  { headers: { Authorization: 'Bearer ' + SUPABASE_ANON_KEY } }
);
const data = await res.json();
```

---

## 5. When to Add More Backend

| Need | Use |
|------|-----|
| Stripe payments | Edge Function + webhook |
| Send emails | Edge Function or Supabase Auth (e.g. password reset) |
| Cron / scheduled tasks | Edge Function + Supabase Cron |
| Complex or slow queries | PostgreSQL RPC (Stored Procedure) |
| Full control, custom API | Node/Express or Python API (hosted separately) |

---

## 6. Security Notes

- Use the **anon** key in the browser only; never expose the **service_role** key.
- Use RLS policies so users can only access their own data.
- Use Edge Functions for any logic that needs the service role or other secrets.
- For production, configure **Redirect URLs** and **Site URL** in Supabase.
- **Password reset:** Add your reset page to Redirect URLs in Supabase (Authentication → URL Configuration), e.g. `http://localhost:3000/reset-password.html` and your production URL, so “Forgot password?” emails work.

---

## 7. Files Reference

| File / folder | Purpose |
|---------------|---------|
| `js/supabase-config.js` | Supabase URL, keys, client setup |
| `supabase-*.sql` | Schema, RLS, triggers – run in Supabase SQL Editor |
| `supabase/functions/health/` | Edge Function template (optional) |
