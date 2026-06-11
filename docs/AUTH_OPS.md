# Auth and operations (ShareEat)

## Login flows

- **Buyers:** `index.html` / `login.html` → `supabaseUserClient` (storage key `shareeat-user-auth`).
- **Sellers:** `seller-admin-login.html` → `supabaseSellerClient` (storage key `shareeat-seller-auth`).
- **Admins:** `admin-login.html` → `supabaseAdminClient` (storage key `shareeat-admin-auth`).

You can be logged in as user and seller (or admin) in different tabs; sessions are separate.

## Role and RLS

- **Role** is stored on `public.profiles` (`role`: `user`, `seller`, `admin`). After login, the app may upsert a profile from `user_metadata` if missing.
- **RLS** on `profiles`: users read own row; admins read all via `is_admin()` (see `supabase-profiles-rls-fix-500.sql`). There is no “anyone can read profiles” policy; remove it if present for stricter security.
- Run `supabase-profiles-backfill-role.sql` so existing users have `role = 'user'` and missing profiles are created from `auth.users`.

## Leaked-password protection

In **Supabase Dashboard → Authentication → Providers → Email**, enable **“Confirm email”** and **“Secure email change”**. Consider enabling **Leaked password protection** (Supabase will check credentials against known leak DBs).

## Client helper

Use `getClientForPage()` from `js/supabase-config.js` when you need the correct Supabase client for the current page (user / seller / admin). Role-guard and admin/seller scripts use it so logout and data calls use the right session.
