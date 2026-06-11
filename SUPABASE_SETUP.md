# ShareEat – Supabase Setup Guide

Your login and register pages are now connected to Supabase for authentication.

## Step 1: Create a Supabase Project

1. Go to [supabase.com](https://supabase.com) and sign up (free).
2. Click **New Project**.
3. Choose organization, name your project (e.g. `shareeat`), set a database password.
4. Wait for the project to be created.

## Step 2: Get Your API Keys

1. In your Supabase project, go to **Project Settings** (gear icon in sidebar).
2. Click **API** in the left menu.
3. Copy:
   - **Project URL** (e.g. `https://xxxxx.supabase.co`)
   - **anon public** key (under "Project API keys")

## Step 3: Add Keys to Your Project

1. Open `js/supabase-config.js` in your project.
2. Replace the placeholders:

```javascript
const SUPABASE_URL = 'https://YOUR-PROJECT-ID.supabase.co';
const SUPABASE_ANON_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...';
```

## Step 4: Store user registration data (profiles table)

1. In Supabase, go to **SQL Editor**.
2. Open the file `supabase-profiles-trigger.sql` from your project.
3. Copy its full contents and paste into the SQL Editor, then click **Run**.
4. This will:
   - Create the **profiles** table (`id`, `full_name`, `role`) linked to auth users
   - Add a trigger so every new sign-up automatically gets a profile row
   - Set RLS policies so users can only insert/update their own profile

After this, when someone registers on `register.html`, their **email** and auth data go to **Authentication → Users**, and their **full name** and **role** are stored in **Table Editor → profiles**.

## Step 5: Seller listings (food items)

1. In Supabase, go to **SQL Editor**.
2. Open the file `supabase-listings.sql` from your project.
3. Copy its full contents and paste into the SQL Editor, then click **Run**.
4. This creates the **listings** table and RLS policies. Active listings will appear on the user page (index.html).
5. **Image uploads (required for seller food images):**
   - Go to **Storage** → **New bucket** → Name: `listing-images`, set **Public** to **Yes** (required!) → Create.
   - If images still show placeholder: In Supabase Storage, open your folder, right‑click an image → Copy image address. In Edit listing, paste that URL into "Or paste image URL".
   - Then go to **SQL Editor** and run the Storage policy section from `supabase-listings.sql` (the `CREATE POLICY` statements for `storage.objects`). Or in Dashboard: Storage → listing-images → Policies → Add policy for INSERT (authenticated) and SELECT (public).
   - Without this, uploaded images will fail and listings will show a placeholder.
6. **Category tags (optional):** Run `supabase-listings-migration-category.sql` in SQL Editor to add `category` and `collect_when` columns. This lets sellers tag listings (Meals, Bread & Pastries, Vegan, etc.) and filter by collect time.
7. **Inventory (seller):** Run `supabase-listings-inventory.sql` in SQL Editor to add `min_quantity` to listings. This powers the seller **Inventory** page (low-stock alerts, Add/Remove stock, Update min quantity).
9. **Orders (for checkout):** Run `supabase-orders.sql` in SQL Editor to create `orders` and `order_items` tables. Then run **`supabase-order-stock-sync.sql`** so that when a customer places an order, listing stock is reduced automatically (and restored when a seller rejects the order). This keeps the user browse page and seller Inventory in sync. If checkout fails with *"infinite recursion detected in policy for relation orders"*, run `supabase-orders-fix-rls-recursion.sql` in SQL Editor to fix RLS.
10. **Store address (per-listing pickup):** Run `supabase-listings-migration-address.sql` to add `address` column. Each listing can then have its own store pickup address.
11. **Map coordinates (for faster map loading):** Run `supabase-listings-migration-location.sql` to add `latitude` and `longitude` columns. Sellers can enter coordinates directly in the listing form, making map markers appear instantly without geocoding delays.
12. **Platform promos (first-time, weekend, loyalty):** Run `supabase-promo-tracking.sql` in SQL Editor to create `promo_redemptions` and `user_shares` tables. This tracks FIRST3 (first order), WEEKEND10, and LOYAL5 (share) code usage so each offer is shown or blocked correctly.

## Step 6: Enable Email Auth

1. In Supabase, go to **Authentication** → **Providers**.
2. Ensure **Email** is enabled (it is by default).
3. (Optional) Under **Auth** → **URL Configuration**, add your site URL for redirects.

## Step 7 (Optional): Enable Google Sign-In

1. Go to **Authentication** → **Providers** → **Google**.
2. Enable Google.
3. Create OAuth credentials in [Google Cloud Console](https://console.cloud.google.com).
4. Add the Client ID and Client Secret to Supabase.

## Testing

1. Open `register.html` and create an account with email, password, and full name.
2. In Supabase, check **Authentication** → **Users** for the new user.
3. In Supabase, check **Table Editor** → **profiles** — you should see a row with that user’s `id`, `full_name`, and `role`.
4. Open `login.html` and sign in with the same credentials.
5. On success, you’ll be redirected to `index.html`.
6. **Listings:** Register or log in as a **seller** (seller-register.html / seller-login.html), go to **Listings**, click **Create Listing** to add food. Active listings appear on **index.html** (user page).

## Notes

- The **anon** key is safe to use in the browser.
- Never commit your **service_role** key or expose it in frontend code.
- For production, configure **Redirect URLs** in Supabase if using OAuth.
