# Database migrations hub

ShareEat stores schema and RLS policies in **`supabase-*.sql`** files at the repository root. Apply them in **Supabase → SQL Editor** on your project—never assume the repo alone creates tables in the cloud.

## Where to start

| Goal | Document |
|------|----------|
| **Fresh project (full order)** | [SUPABASE_MIGRATION_RUNBOOK.md](SUPABASE_MIGRATION_RUNBOOK.md) — phased runbook (identity → marketplace → features → admin → hardening) |
| **Shorter checklist** | [SUPABASE_SETUP_ORDER.md](SUPABASE_SETUP_ORDER.md) — minimal bootstrap sequence |
| **FYP feature pack** (disputes, flags, saved shops) | [FYP-RUNBOOK.md](FYP-RUNBOOK.md) |
| **Buyer 400/404 fixes (one shot)** | Run `supabase-buyer-schema-bundle.sql` at repo root |
| **Admin replies on disputes** | Run `supabase-order-disputes-reply.sql` after disputes table exists |
| **Admin replies on listing reports** | Run `supabase-listing-reports-reply.sql` after `supabase-listing-reports.sql` |
| **Admin prep queue (read/update)** | Run `supabase-admin-preparation-rls.sql` after `supabase-admin-dashboard-rls.sql` |
| **After any batch** | `supabase-setup-verification.sql` |

## Phases (summary)

1. **Identity & profiles** — triggers, contact fields, seller settings, RLS fixes  
2. **Marketplace core** — listings, orders, pickup, stock sync  
3. **User & seller features** — notifications, chat, promotions, revenue  
4. **Admin** — dashboard RLS, users, audit log, support tickets, payments  
5. **FYP / buyer extras** — `supabase-fyp-feature-pack.sql` or `supabase-buyer-schema-bundle.sql`, `supabase-user-preferred-lang.sql`  
6. **Hardening** — `supabase-security-audit-fixes.sql`, optional seeds  

Full file lists and order: **[SUPABASE_MIGRATION_RUNBOOK.md](SUPABASE_MIGRATION_RUNBOOK.md)**.

## Rules of thumb

- Run **`supabase-admin-dashboard-rls.sql`** (or equivalent with `is_admin()`) before admin-only policies.  
- If a script reports “already exists”, continue unless it only adds policies you still need.  
- Do not skip RLS fix scripts on production.  
- Keep **`SUPABASE_SERVICE_ROLE_KEY`** out of the frontend; use Edge Functions or local scripts only.

## Related setup

- App config: [SUPABASE_SETUP.md](../SUPABASE_SETUP.md)  
- Local env: [SETUP.md](../SETUP.md)  
- Security: [SECURITY.md](../SECURITY.md)
