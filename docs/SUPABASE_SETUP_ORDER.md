# Supabase SQL – run order for ShareEat

Run these in **Supabase → SQL Editor** in this order for a new project. Existing projects: run only what you haven’t yet.

| Order | File | Purpose |
|-------|------|--------|
| 1 | `supabase-profiles-trigger.sql` | `profiles` table, trigger to create profile on signup, base RLS |
| 2 | `supabase-profiles-contact.sql` | `profiles.contact_email` |
| 3 | `supabase-profile-features.sql` | `profiles.phone`, favorites, addresses, etc. |
| 4 | `supabase-seller-settings.sql` | Seller fields on `profiles` (business_name, etc.) |
| 5 | `supabase-orders.sql` | `orders`, `order_items`, RLS |
| 6 | `supabase-listings.sql` | `listings`, RLS |
| 7 | `supabase-admin-dashboard-rls.sql` | Admin read access (listings, profiles, orders, order_items) |
| 8 | `supabase-admin-user-management.sql` | `profiles.created_at`, `profiles.status`, admin update profile |
| 9 | `supabase-profiles-backfill-role.sql` | Set `role = 'user'` where null, create missing profiles from `auth.users` |
| 10 | `supabase-profiles-rls-fix-500.sql` | Fix 500 on profiles (is_admin(), own profile read, no recursion) |
| 11 | `supabase-admin-order-console.sql` | (Optional) Admin can read/update all orders for ops |
| 12 | `supabase-chat-messages.sql` | Chat tables if you use AI chat |
| 13 | `supabase-chat-extras.sql` | Escalations, satisfaction |
| 14 | `supabase-chat-unmatched.sql` | Unmatched queries log |
| 15 | `supabase-security-audit-fixes.sql` | (Optional) RLS “always true” fixes, function `search_path` |
| 16 | `supabase-audit-log.sql` | `audit_log` + `log_audit_event()` RPC |
| 17 | `supabase-admin-advancement.sql` | Admin read audit log, `platform_flags` + public read, `v_admin_orders_daily` view |
| 18 | `supabase-low-stock-alerts.sql` | (If used) Stock alerts + `platform_settings` |

After step 9, **User Management** in admin can list buyers. After step 10, **login** and **profile fetch** should not return 500.
