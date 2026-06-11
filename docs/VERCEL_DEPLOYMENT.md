# ShareEat Vercel Deployment (User + Seller + Admin)

This project is a static multi-page app, so you can deploy it directly on Vercel.

## 1) Push project to GitHub

If not already pushed:

```bash
git add .
git commit -m "prepare vercel deployment"
git push
```

## 2) Import to Vercel

1. Open [Vercel Dashboard](https://vercel.com/dashboard).
2. Click **Add New Project**.
3. Import this repository.
4. In project settings (must match `vercel.json` in the repo):
   - **Framework Preset**: `Other`
   - **Root Directory**: leave empty
   - **Build Command**: `npm run build` (or leave empty — repo defines it)
   - **Output Directory**: `.` (repo root — HTML/CSS/JS live here, not in `public/`)
   - **Install Command**: leave empty (repo skips install for static HTML)
5. Click **Deploy**.

If a deploy fails at “Installing dependencies”, open the **full** build log (scroll to the bottom). The repo skips `npm install` because ShareEat is static files only.

## 2.1 Environment variables (recommended)

If you use email/edge-function helpers, add these in Vercel -> Project Settings -> Environment Variables:

- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY` (server-side only)
- `RESEND_API_KEY`
- `EMAIL_FROM`
- `EMAIL_SUPPORT`
- **`GOOGLE_MAPS_API_KEY`** — `map.html` needs this on Vercel (not in git). `npm run build` generates `js/shareeat-runtime.generated.js` from this variable. Add it for **Production** and **Preview** if you use preview URLs. In Google Cloud, allow **HTTP referrers** such as `https://your-app.vercel.app/*` and `https://*.vercel.app/*` plus `http://localhost:*/*` for local dev.

## 3) Use role entry links

After deploy, these routes are available:

- `/` -> landing page (`index.html`)
- `/user` -> `user-home.html`
- `/seller` -> `seller-login.html`
- `/admin` -> `admin-login.html`

Example:

- `https://your-app.vercel.app/user`
- `https://your-app.vercel.app/seller`
- `https://your-app.vercel.app/admin`

## 4) Supabase Auth configuration (important)

In Supabase -> **Authentication** -> **URL Configuration**:

- **Site URL**:
  - `https://your-app.vercel.app`

- **Redirect URLs** (add all):
  - `https://your-app.vercel.app/*`
  - `https://your-app.vercel.app/login.html`
  - `https://your-app.vercel.app/seller-login.html`
  - `https://your-app.vercel.app/admin-login.html`
  - `https://your-app.vercel.app/reset-password.html`
  - `https://your-app.vercel.app/forgot-password.html`

If you use Vercel preview deployments, also add:

- `https://*.vercel.app/*`

## 5) Post-deploy smoke test

Check these flows:

1. User login -> browse listings -> bag -> checkout -> profile.
2. Seller login -> listings -> orders -> analytics.
3. Admin login -> dashboard -> users -> prep queue -> analytics -> settings.
4. Forgot password and reset password pages.

## 6) Post-migration auth/email checks (shareeat.my)

If you ran seller/admin email migrations to `@shareeat.my`, verify:

1. Seller login works with new addresses (example: `restauranttanoor@shareeat.my`).
2. Admin login works with current admin email (example: `admin@shareeat.my` if set).
3. In Supabase:
   - `profiles.contact_email` for sellers uses `@shareeat.my`
   - `listings.store_email` is synced from seller profile
4. Support/system sender values are set:
   - `EMAIL_FROM=noreply@shareeat.my`
   - `EMAIL_SUPPORT=support@shareeat.my`
5. Trigger one test email flow (e.g., support/contact or low-stock alert) and confirm delivery.

## Notes

- This frontend uses Supabase **anon** key in browser (`js/supabase-config.js`), which is expected.
- Never expose Supabase **service_role** key in frontend files.
- If you see auth redirect issues, re-check Supabase Redirect URLs first.
