# ShareEat – Setup Guide

This document describes how to configure the project: Supabase migrations order and environment/config.

---

## 1. Supabase project

1. Create a project at [supabase.com](https://supabase.com).
2. In **Project Settings → API**, copy:
   - **Project URL**
   - **anon public** key
3. In **Authentication → Providers**, enable **Email** (and optionally **Google**).
4. In **js/supabase-config.js**, set `SUPABASE_URL` and `SUPABASE_ANON_KEY`.

> **Canonical project:** the app uses Supabase project ref **`mhtojnosardpvwhvpasx`** (matches `js/supabase-config.js`, `supabase/.temp/project-ref`, and the anon key's JWT `ref`). Run all SQL migrations and keep auth/storage on **this** project.
>
> **Do not use** any auto-created **Vercel ↔ Supabase integration** project (for example `supabase-bisque-dog` / `yxeagkihogbtnoujihpol`). It is empty/unused; pointing the app at it (via integration env vars) will break login and show empty listings. Remove or ignore that integration to avoid confusion.
>
> **Free-plan note:** Supabase pauses Free projects after about a week of inactivity. Open the dashboard and unpause **`mhtojnosardpvwhvpasx`** before any demo, or the app will appear broken.

---

## 2. Supabase SQL migrations (order)

Run these in the **Supabase → SQL Editor** in the order below. Comments inside each file may say “after X”; this list is the intended order.

For a maintained and complete run order, use:

- `docs/SUPABASE_MIGRATION_RUNBOOK.md`
- then run `supabase-setup-verification.sql`

### 2.1 Base (run first)

| Order | File | Purpose |
|-------|------|--------|
| 1 | `supabase-profiles-trigger.sql` | Creates `profiles` and trigger on signup |
| 2 | `supabase-seller-settings.sql` | Adds business/hours/notifications/bank to `profiles` |
| 3 | `supabase-seller-settings-defaults.sql` | Defaults for seller profile columns (if present) |
| 4 | `supabase-profiles-contact.sql` | Adds phone/contact_email to `profiles` (if not in seller-settings) |
| 5 | `supabase-profile-features.sql` | Favorites, saved addresses, payment methods, etc. |
| 6 | `supabase-profile-insert-missing.sql` | Backfill profiles for existing users (if needed) |
| 7 | `supabase-profile-impact.sql` | Impact stats (meals_saved, etc.) on profiles |

### 2.2 Listings

| Order | File | Purpose |
|-------|------|--------|
| 8 | `supabase-listings.sql` | Creates `listings` table and storage |
| 9 | `supabase-listings-migration-address.sql` | Address/lat/lng on listings |
| 10 | `supabase-listings-migration-category.sql` | Category and related fields |
| 11 | `supabase-listings-migration-store-name.sql` | Store name on listings |
| 12 | `supabase-listings-migration-location.sql` | Location-related fields |
| 13 | `supabase-listings-migration-tags.sql` | Tags (if used) |
| 14 | `supabase-listings-contact.sql` | store_phone, store_email on listings |
| 15 | `supabase-listings-inventory.sql` | Inventory/stock fields (if used) |
| 16 | `supabase-listing-images-policies.sql` | Storage RLS for listing images |

### 2.3 Orders and related

| Order | File | Purpose |
|-------|------|--------|
| 17 | `supabase-orders.sql` | `orders` and `order_items` |
| 18 | `supabase-orders-fix-rls-recursion.sql` | RLS fixes (if applicable) |
| 19 | `supabase-orders-pickup.sql` | Pickup-related columns |
| 20 | `supabase-order-stock-sync.sql` | Stock sync for orders |
| 21 | `supabase-order-rejection.sql` | Rejection flow |
| 22 | `supabase-order-messages.sql` | Order messages |
| 23 | `supabase-ratings-reviews.sql` | Ratings/reviews |

### 2.4 Optional / feature-specific

| File | Purpose |
|------|--------|
| `supabase-storage-policies.sql` | General storage policies |
| `supabase-promotions.sql` | Promotions table |
| `supabase-promo-tracking.sql` | Promo tracking |
| `supabase-seller-notifications.sql` | Seller notifications (new order, completed pickup, low stock, new listing, stock added, promotion, new message; run after `order_messages` for message notifications) |
| `supabase-buyer-notifications.sql` | Buyer notifications |
| `supabase-seller-revenue.sql` | Seller revenue |
| `supabase-ask-a-friend.sql` | Ask-a-friend feature |
| `supabase-pickup-reminders.sql` | Pickup reminders |
| `supabase-delete-test-orders.sql` | Utility to delete test orders |
| `supabase-seed-revenue-sample.sql` | Seed sample data for Revenue + Analytics dashboards (Today, This Week, charts, Recent Transactions, KPIs, Customer Rating) |
| `supabase-audit-log.sql` | Audit log table and RPC for sensitive actions (exports, bulk operations) |
| `supabase-chat-messages.sql` | AI chat messages (what users ask) for viewing why users ask |
| `supabase-admin-dashboard-rls.sql` | Admin role policies for cross-platform dashboard reads (users/sellers/listings/orders) |
| `supabase-admin-user-management.sql` | Admin user/seller management tables/policies and status updates |
| `supabase-admin-advancement.sql` | Advanced admin capabilities (audit digest, ops features, governance helpers) |
| `supabase-admin-communication.sql` | Admin communication helpers (broadcast/email workflow support) |
| `supabase-admin-payments.sql` | Admin payment/reconciliation data support |
| `supabase-profiles-rls-fix-500.sql` | Fix recursive `profiles` RLS policies that can block login/profile reads |
| `supabase-security-hardening.sql` | **Run last.** Enforce `profiles` own/admin-only read (protects phone/email/bank fields) and restrict listing-image storage update/delete to the uploader |

If a migration fails because a column or table already exists, you can often skip that file or run the parts that are still needed.

---

## 3. Front-end config

### 3.1 Supabase (required)

- **File:** `js/supabase-config.js` (copy from `js/supabase-config.example.js`)
- Set `SUPABASE_URL` and `SUPABASE_ANON_KEY` from your project.
- Do not commit real keys to a public repo; `js/supabase-config.js` is in `.gitignore`. Use the example file as a template.

### 3.2 Google Maps (optional)

- **File:** `js/map-config.local.js` (copy from `js/map-config.local.example.js`). It is listed in `.gitignore` — do not commit real keys.
- `map.html` loads `js/map-config.local.js` first, then `js/map-config.js` (fallback / Docker runtime).
- Set `window.GOOGLE_MAPS_API_KEY` in that local file for the Map page.
- **Security:** Restrict the key in Google Cloud Console:
  - **Application restrictions:** HTTP referrers (e.g. your domain, `http://localhost:*`).
  - **API restrictions:** Maps JavaScript API, Geocoding API.
- **`GOOGLE_MAPS_API_KEY` on Vercel:** `npm run build` writes `js/shareeat-runtime.generated.js` (see `scripts/write-shareeat-runtime.mjs`). Add the variable in Vercel → **Environment Variables** for **Production** (and **Preview** if needed), then redeploy. Do not commit a real key into `shareeat-runtime.generated.js` (keep the repo copy empty or placeholder-only).

---

## 4. Running the app

- Open `index.html` or `user-home.html` (or use a local server) so the app can load JS/CSS and call Supabase.
- For Maps, edit `js/map-config.local.js` with a valid key (or rely on Docker `GOOGLE_MAPS_API_KEY` → `__SHAREEAT_RUNTIME__`). Without a key, the map page shows a clear error instead of loading Google’s script.

---

## 5. Summary

1. Create Supabase project and set URL + anon key in `js/supabase-config.js`.
2. Run SQL migrations in the order in **`docs/SUPABASE_MIGRATION_RUNBOOK.md`**.
3. Run `supabase-setup-verification.sql` and fix any missing rows/policies.
4. Optionally set Google Maps key in `js/map-config.local.js` (see §3.2) and restrict it in Google Cloud.
5. For deployment, follow `docs/VERCEL_DEPLOYMENT.md`.
6. Optionally run the same static files in Docker (below).

---

## 6. Docker (optional — static site in a container)

The project has no compile step for the browser app: nginx serves `index.html`, `user-home.html`, `js/`, and `css/` as static files—same outcome as `./run.sh` or Vercel, but packaged for a VM or demo server.

### 6.1 Runtime configuration (recommended)

Secrets are applied **when the container starts**, not burned into Git:

1. Copy **`docker/sample.env`** to **`./.env`** in the repo root (**`.env` is gitignored**).
2. Set **`SUPABASE_URL`**, **`SUPABASE_ANON_KEY`**, and optionally **`GOOGLE_MAPS_API_KEY`**.
3. **`docker-entrypoint-wrapper.sh`** runs **`shareeat-merge-config.sh`** before the stock nginx entrypoint so config is merged **for every container start**, not only when the main process is literally `nginx` (see **`docker/docker-entrypoint-wrapper.sh`**).

**`docker compose`** passes `environment:` from the same variables—Compose reads project root **`.env`** automatically for interpolation.

```bash
cp docker/sample.env .env
# edit .env
docker compose up --build
```

For a one-off run without `.env`:

```bash
docker run --rm -p 8080:80 \
  -e SUPABASE_URL="https://YOUR_PROJECT.supabase.co" \
  -e SUPABASE_ANON_KEY="YOUR_KEY" \
  shareeat-web
```

**Caveat:** If a value ever contained a dollar sign (`$`), `envsubst` could mis-handle it; Supabase URLs and typical JWT anon keys do not.

### 6.2 Local dev without Docker

Use **`js/supabase-config.js`** (from **`js/supabase-config.example.js`**) as in §3. That file now also respects **`window.__SHAREEAT_RUNTIME__`** if you inject it for testing.

### 6.3 CI

**`.github/workflows/docker.yml`** runs **`docker compose build`** on push/PR so the image keeps building on clean clones (no committed `supabase-config.js` required).

### 6.4 Optional: mount a custom core file

Uncomment the **`volumes`** line in **`docker-compose.yml`** to mount **`./js/supabase-config.js`** over **`supabase-config.core.js`** inside the container. Restart after edits. Prefer **runtime env** for production so keys are not baked into layers.

### 6.5 Production notes

- **HTTPS:** Terminate TLS in front of Docker (reverse proxy, load balancer, or Cloudflare)—the container listens on plain HTTP internally.
- **Supabase redirects:** Configure **Site URL** and **Redirect URLs** in Supabase Auth for both your Vercel domain and any Docker-hosted URL (including `http://localhost:8080` during development).
- The image removes **`docker/`** and **`.github/`** from the web root after `COPY`; backend remains **Supabase in the cloud**—the container only serves the front end.
