# ShareEat – Admin Communication (full stack)

Broadcast email to buyers/sellers from **Admin → Communication**, with **server-side audience counts**, **database history**, **test send**, **scheduling**, and optional **cron** processing.

## 1) SQL (run in Supabase SQL Editor)

After `supabase-audit-log.sql` and `supabase-admin-dashboard-rls.sql`:

```text
supabase-admin-communication.sql
```

Creates:

- **`admin_broadcast_log`** – one row per broadcast or test send (inserted by the Edge Function).
- **`scheduled_broadcasts`** – queued sends; processed when due.
- **`email_provider_events`** – optional storage for **Resend webhooks** (open/click KPIs later).

## 2) CORS / “preflight doesn’t pass access control”

Browsers send an **OPTIONS** request before `POST`. If Supabase verifies JWT **at the edge** for your function, **OPTIONS can fail** (no `Authorization` header) → Chrome reports *Response to preflight request doesn't pass access control check*.

**Fix (do all that apply):**

1. **Project config:** Repo includes **`supabase/config.toml`** with `[functions.admin_broadcast] verify_jwt = false` — this is what the CLI uses when you deploy from the project root.
2. **Redeploy from the folder that contains `supabase/config.toml`:**  
   `supabase functions deploy admin_broadcast --no-verify-jwt`
3. **Dashboard (if you deploy only from the UI):** Supabase Dashboard → **Edge Functions** → **admin_broadcast** → disable **Verify JWT** / **Enforce JWT** (wording varies). The function still checks the admin JWT in code.

After that, the browser’s **OPTIONS** request should succeed (e.g. **200** with CORS headers), then **POST** runs.

**Verify:** DevTools → **Network** → filter `admin_broadcast` → click **OPTIONS** → status should be **200** (not 401/404).

*`chrome-extension://invalid/` errors are from a browser extension, not ShareEat.*

## 3) Edge Function secrets

Deploy:

```bash
supabase functions deploy admin_broadcast --no-verify-jwt
```

(If `config.toml` sets `verify_jwt = false`, the flag is redundant but harmless.)

Set **required** secrets:

| Secret | Purpose |
|--------|---------|
| `RESEND_API_KEY` | Send via Resend |
| `SUPABASE_URL` | Project URL |
| `SUPABASE_SERVICE_ROLE_KEY` | Service client in function |
| `SUPABASE_ANON_KEY` | Validate admin JWT |
| `EMAIL_FROM` | From address (verified in Resend) |
| `EMAIL_SUPPORT` | Reply-To |

**Optional:**

| Secret | Purpose |
|--------|---------|
| `BROADCAST_CRON_SECRET` | Authorize `action: process_scheduled` from cron (no user login) |
| `BROADCAST_SEND_DELAY_MS` | Delay between each Resend call (default **120** ms; max **2500**) — light rate limiting |

**`count` response** includes `domain_hints`: up to **5** masked domain labels (e.g. `g***.com`) for audience sanity checks.

**Outgoing HTML** includes a short **footer** with support contact (`EMAIL_SUPPORT`) for compliance.

## 4) Actions (`POST` body JSON)

All actions except `process_scheduled` need an **admin session JWT** (or service role key).

| `action` | Description |
|----------|-------------|
| *(omit)* or `send` | Broadcast email (`segment`, `subject`, `body_text` or `html`, `maxRecipients`) |
| `count` | `totalUnique` emails matching segment (no send) |
| `test_send` | Sends **one** email to the admin’s **profile `contact_email`** or Auth email, subject prefixed `[TEST]` |
| `schedule` | Queue row (`scheduled_at` ISO, must be **≥ 1 min** in future) |
| `list_logs` | Recent `admin_broadcast_log` rows |
| `list_scheduled` | Scheduled jobs |
| `cancel_schedule` | `schedule_id` – pending only |
| `process_scheduled_now` | Admin trigger: run due jobs (same processor as cron) |
| `process_scheduled` | Cron only: requires `BROADCAST_CRON_SECRET` as `Authorization: Bearer …`, header `x-broadcast-cron`, or body `cron_secret` |

### Cron example (every 5 minutes)

Call your project function URL:

```bash
curl -sS -X POST 'https://<PROJECT_REF>.supabase.co/functions/v1/admin_broadcast' \
  -H "Authorization: Bearer $BROADCAST_CRON_SECRET" \
  -H "Content-Type: application/json" \
  -d '{"action":"process_scheduled"}'
```

Use **Supabase Scheduled Functions**, **GitHub Actions**, or **pg_cron + pg_net** to hit this URL.

## 5) Honest KPIs

- **Emails sent (this month)** – summed from **`admin_broadcast_log`** (`sent` field), loaded via `list_logs`.
- **Notifications / open rate / click rate** – not inferred in the UI. To track opens/clicks, add a **Resend webhook** Edge Function that inserts into `email_provider_events` and aggregate later (see Resend docs for signing).

## 6) Admin UI

- **Show email preview**, **Send now** (confirmation modal with summary).
- **Send test to me** – validates copy before a full broadcast.
- **Schedule send** – datetime + save; use **Run due sends now** or cron to deliver.
- **Message history** – server log (falls back to empty if SQL not applied).
- **Scheduled** tab – list + cancel pending + process due.

## 7) Troubleshooting

| Issue | Check |
|-------|--------|
| Count / history empty | Run `supabase-admin-communication.sql`; redeploy function |
| Test send fails | Admin must have **`contact_email`** on `profiles` or a valid Auth email |
| Scheduled never sends | Click **Run due sends now** or set up cron + `BROADCAST_CRON_SECRET` |
| 401 on invoke | Log in via `admin-login.html` (admin JWT) |
