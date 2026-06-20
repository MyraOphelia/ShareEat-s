# ShareEat FYP feature pack — run order

Apply SQL in the Supabase SQL editor in this order:

1. Core app schema you already use (profiles, orders, listings, etc.).
2. Admin helpers that define `public.is_admin()` and related RLS (e.g. `supabase-admin-dashboard-rls.sql` or your equivalent).
3. **`supabase-fyp-feature-pack.sql`** — adds `user_saved_shops`, `order_disputes`, and `platform_feature_flags`.  
   If **Report an issue** fails with `order_disputes` / schema cache, run **`supabase-order-disputes.sql`** alone (same table + RLS).
4. **`supabase-user-preferred-lang.sql`** — adds `profiles.preferred_lang` (en / bm / zh / ta) for buyer UI language sync across devices.

**One-shot fix for buyer console 400/404 errors:** run **`supabase-buyer-schema-bundle.sql`** (preferred_lang + order_disputes + platform_feature_flags).

**Admin reply on disputes:** run **`supabase-order-disputes-reply.sql`** so Support can **Send to customer** (buyer sees reply on Profile → Orders + Notifications).

Frontend pieces that depend on the pack:

- Profile: saved shops list, dispute form & status in order detail, optional pickup reminders (controlled by flag `buyer_pickup_reminders_ui`).
- Shop detail: **Save shop** button.
- Admin Support: disputes table (`js/admin-disputes-panel.js`).
- Admin Communication: **`buyers_only`** broadcast segment (Edge Function `admin_broadcast`).

Tests: `npm test` runs checkout math assertions (`tests/checkout-math.test.mjs`).
