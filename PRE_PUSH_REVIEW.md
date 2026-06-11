# Pre-push code review – ShareEat

Use this checklist **before you push to GitHub**. Fix or note each item so your repo stays clean and safe.

---

## 1. Secrets & config

| Item | Status | Action |
|------|--------|--------|
| **Supabase keys in `js/supabase-config.js`** | ⚠️ | Anon key is in repo. For **public** repo: consider using placeholders (`YOUR_SUPABASE_URL`, `YOUR_ANON_KEY`) and tell users to add their own in README. For **private** repo: OK to keep. |
| **Google Maps API key in `js/map.js`** | ✅ | Already placeholder `YOUR_API_KEY` – good. |
| **No `service_role` key** | ✅ | Only anon key used – correct. |
| **`.gitignore` exists** | ✅ | Added – ignores `.env`, `.cursor/`, `node_modules/`, logs. |

---

## 2. Server (`server.py`)

| Item | Status | Action |
|------|--------|--------|
| **Debug log path** | ⚠️ | Hardcoded path to `.cursor/debug-f5fc88.log` and `_log()` calls. On other machines this path may not exist. **Option:** Remove the `# #region agent log` block before push, or make path relative / optional. |
| **Python 3.13 `guess_type`** | ✅ | Fixed (returns single value). |
| **Port / no-cache** | ✅ | Fine for dev. |

---

## 3. Auth (`js/auth.js`, login/register pages)

| Item | Status | Action |
|------|--------|--------|
| **Role checks (seller/admin)** | ✅ | Correct redirect and error messages. |
| **Password handling** | ✅ | Sent to Supabase only; no storage in your code. |
| **`console.log` in auth** | ⚠️ | One `console.log('SignUp result:', ...)` – remove or guard with dev flag before push if you want clean console. |

---

## 4. Seller revenue (`seller-revenue.*`, `js/seller-revenue.js`)

| Item | Status | Action |
|------|--------|--------|
| **Charts (Feb 2025 – Feb 2026)** | ✅ | Date range and launch constants in place. |
| **Print (graphs + full table)** | ✅ | `print-color-adjust`, full weekly table in print. |
| **Explainer design** | ✅ | Redesigned. |
| **Weekly table scroll** | ✅ | Scrollable on screen, full in print. |

---

## 5. Other JS / pages

| Item | Status | Action |
|------|--------|--------|
| **`js/map.js`** | ⚠️ | Many `console.log` calls (debug). Optional: remove or wrap in `if (window.DEBUG)` before push. |
| **`js/pickup.js`** | ⚠️ | A few `console.log` debug lines – optional to remove. |
| **Redirects (login, seller-login)** | ✅ | Consistent. |
| **No obvious XSS** | ✅ | User input not written raw to DOM in reviewed files; Supabase/forms normal. |

---

## 6. Docs & repo hygiene

| Item | Status | Action |
|------|--------|--------|
| **README** | ✅ | Clear setup and backend link. |
| **BACKEND.md** | ✅ | Architecture documented. |
| **SUPABASE_SETUP.md** | ✅ | Setup steps. |
| **Unwanted files** | ⚠️ | Ensure no `.env`, real API keys, or `node_modules` are committed. `.gitignore` added to help. |

---

## Quick “before push” commands

```bash
# See what will be committed
git status

# Ensure .gitignore is respected (no .env, .cursor, etc.)
git check-ignore -v .env .cursor 2>/dev/null || true

# Optional: remove debug logging from server.py (see Section 2)
```

---

## Summary

- **Must fix for a clean push:** Add/use `.gitignore` (done). Decide: real Supabase URL/key in repo (private OK, public = consider placeholders).  
- **Recommended:** Remove or relax the debug log block in `server.py` so it doesn’t depend on your machine’s path.  
- **Optional:** Strip or guard `console.log` in auth, map, pickup for production-style console.

When these are done, you’re good to push. If you want, I can apply the server debug-log change or a placeholder config next.
