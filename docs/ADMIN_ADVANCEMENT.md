# ShareEat – Admin advancement (items 1–8)

This doc matches the **advanced admin dashboard** roadmap: what was added in the repo and what you must run in Supabase / deploy.

## 1) Ops & health strip

- **UI:** `admin-dashboard.html` → “Ops & health” (stock alerts, sold-out, low stock, system status).
- **Logic:** `js/admin-dashboard.js` → `loadOpsHealth()`.
- **Optional hide:** Settings → Platform flags → uncheck “Show Ops & health strip” (`platform_settings.platform_flags.show_admin_ops_dashboard`).

## 2) KPI definitions

- **Metric cards** use `title` tooltips and a small ⓘ hint so admins know what each number means (e.g. revenue = completed orders today, gross).

## 3) Audit trail

- **SQL:** `supabase-audit-log.sql` (table + `log_audit_event` RPC) and **`supabase-admin-advancement.sql`** (policy so **admins can SELECT** `audit_log`).
- **Client:** `js/audit-log-client.js` → `ShareEatAudit.log(action, resource, details)`.
- **Wired:** Admin Users (export CSV, bulk suspend, status toggle), Admin Analytics (CSV export), Communication (after broadcast).

## 4) Search, export, bulk actions

- **Users:** `admin-users.html` / `js/admin-users.js` — select visible rows, **Export CSV**, **Suspend selected** (with audit).

## 5) Communication (email)

- **UI:** `admin-communication.html`, `js/admin-communication.js` — server counts, DB history, test send, schedule, confirmation modals.
- **SQL:** `supabase-admin-communication.sql` — `admin_broadcast_log`, `scheduled_broadcasts`, optional `email_provider_events`.
- **Edge Function:** `supabase/functions/admin_broadcast/index.ts` (Resend; actions: `send`, `count`, `test_send`, `schedule`, `list_logs`, `list_scheduled`, `cancel_schedule`, `process_scheduled_now`, `process_scheduled` + cron secret).
- **Deploy:**  
  `supabase functions deploy admin_broadcast --no-verify-jwt`  
  Set secrets: `RESEND_API_KEY`, `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `SUPABASE_ANON_KEY`, `EMAIL_FROM`, `EMAIL_SUPPORT`. Optional: `BROADCAST_CRON_SECRET` for cron.
- **Full guide:** `docs/ADMIN_COMMUNICATION.md`.
- Recipients use **`profiles.contact_email`**. Sends are **capped per request** (max 50).

## 6) Safer admin sessions

- Prefer **`window.supabaseAdminClient`** on admin pages (`shareeat-admin-auth` storage). Log in via **`admin-login.html`** only.
- `js/role-guard.js` still enforces `profiles.role = admin` on guarded pages.

## 7) Performance / rollups

- **View:** `v_admin_orders_daily` in `supabase-admin-advancement.sql` — daily order count + gross revenue for BI or future server-side charts.
- Heavy dashboards can query this view (or materialize later) instead of scanning all rows in the browser.

## 8) Feature flags & maintenance

- **Row:** `platform_settings.key = 'platform_flags'` (seeded in `supabase-admin-advancement.sql`).
- **Admin UI:** `admin-settings.html` → Platform flags form; **`js/admin-settings.js`** loads/saves.
- **Public read:** same SQL adds **`Public read platform_flags`** so **anon** can read only that row (maintenance banner).
- **Storefront:** `js/platform-flags.js` on `user-home.html` — if `maintenance_mode` is true, shows top banner (`user-home.css`).

## One-shot SQL

Run in order (if not already):

1. `supabase-audit-log.sql`
2. `supabase-admin-advancement.sql`
3. `supabase-admin-communication.sql` (broadcast log + scheduled sends)

Then redeploy **`admin_broadcast`** if you use Communication → Send now / schedule / server history.
