# Security — API keys and secrets

## What must NEVER be public

| Secret | Where it belongs |
|--------|------------------|
| Supabase **service_role** key | Supabase Edge Function secrets only (`admin_broadcast`, etc.) |
| Google OAuth **Client Secret** | Supabase Dashboard → Auth → Google only |
| **RESEND_API_KEY**, **BROADCAST_CRON_SECRET** | Supabase Edge Function secrets |
| **GOOGLE_MAPS_API_KEY** (production) | Vercel env vars → `npm run build`, or local `js/map-config.local.js` (gitignored) |

## What is OK in the browser (still protect the repo)

- Supabase **anon** key and project URL — required for the frontend, but protected by **Row Level Security (RLS)**. Do not treat them as “private”; treat them as **public client credentials** with strict RLS.

## Local files (gitignored — do not commit)

- `js/supabase-config.js` — copy from `js/supabase-config.example.js`
- `js/map-config.local.js` — copy from `js/map-config.local.example.js`
- `.env`, `.env.local`

## Before every push

```bash
npm run check-secrets
```

## If keys were ever committed to GitHub

1. **Rotate** the exposed key in Supabase / Google Cloud.
2. Remove the file from git history (or make the repo private).
3. Keep `js/supabase-config.js` **untracked** — only `js/supabase-config.example.js` with placeholders should be in git.

## Production (Vercel)

- Set `GOOGLE_MAPS_API_KEY` in Vercel → Environment Variables (not in source code).
- Restrict the Maps key: HTTP referrers for your domain only.
- Restrict the Supabase anon key is normal; ensure RLS policies are enabled on all tables.

## Browser / DevTools (`js/devtools-guard.js`)

On **production** hosts (not `localhost`), ShareEat loads a small guard that:

- Blocks common shortcuts (F12, Ctrl+Shift+I/J/C, view-source, etc.)
- Disables right-click “Inspect” on the page
- Shows a full-screen message if DevTools appears to be docked open

**Important:** This is a **deterrent only**. Anyone can still read network traffic, disable JavaScript, or use another browser profile. You **cannot** hide HTML/CSS/JS from a determined user.

**Real security:**

- Never put `service_role` or other secrets in frontend files
- Enforce access with **Supabase RLS** and auth
- Keep `js/supabase-config.js` out of git (use `.example` + env)

**Local dev:** the guard does **not** run on `localhost` / `127.0.0.1`.

**Production bypass (you only):** open any page once with `?debug=shareeat` in the URL (e.g. for testing on Vercel); that session skips the guard until the tab closes.
