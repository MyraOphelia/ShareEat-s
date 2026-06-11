# Supabase Migration Runbook

Use this runbook for fresh environments (local, staging, production) to avoid out-of-order SQL and RLS regressions.

## Phase 1: Identity + Profiles (required first)

1. `supabase-profiles-trigger.sql`
2. `supabase-profiles-contact.sql`
3. `supabase-seller-settings.sql`
4. `supabase-seller-settings-defaults.sql`
5. `supabase-profiles-backfill-role.sql`
6. `supabase-profile-insert-missing.sql`
7. `supabase-profile-impact.sql`
8. `supabase-profiles-rls-fix-500.sql`

## Phase 2: Core Marketplace (required)

1. `supabase-listings.sql`
2. `supabase-listings-migration-address.sql`
3. `supabase-listings-migration-store-name.sql`
4. `supabase-listings-migration-location.sql`
5. `supabase-listings-migration-category.sql`
6. `supabase-listings-migration-tags.sql`
7. `supabase-listings-contact.sql`
8. `supabase-listings-inventory.sql`
9. `supabase-listing-images-policies.sql`
10. `supabase-orders.sql`
11. `supabase-orders-fix-rls-recursion.sql`
12. `supabase-orders-pickup.sql`
13. `supabase-order-stock-sync.sql`
14. `supabase-order-rejection.sql`
15. `supabase-order-messages.sql`
16. `supabase-ratings-reviews.sql`

## Phase 3: User/Seller Features (recommended)

1. `supabase-profile-features.sql`
2. `supabase-user-features.sql`
3. `supabase-user-feedback.sql`
4. `supabase-buyer-notifications.sql`
5. `supabase-seller-notifications.sql`
6. `supabase-pickup-reminders.sql`
7. `supabase-promotions.sql`
8. `supabase-promo-tracking.sql`
9. `supabase-chat-messages.sql`
10. `supabase-chat-extras.sql`
11. `supabase-chat-unmatched.sql`
12. `supabase-chat-promo-check.sql`
13. `supabase-chat-knowledge.sql` (trainable assistant Q&A; admins add rows to "train" the bot)
14. `supabase-seller-revenue.sql`

## Phase 4: Admin Features (run after core + identity)

1. `supabase-audit-log.sql`
2. `supabase-admin-dashboard-rls.sql`
3. `supabase-admin-user-management.sql`
4. `supabase-admin-advancement.sql`
5. `supabase-admin-communication.sql`
6. `supabase-admin-payments.sql`
7. `supabase-listing-reports.sql`
8. `supabase-orders-missed-pickup.sql`
9. `supabase-support-tickets.sql`
10. `supabase-site-content.sql`

## Phase 5: Hardening + Optional Seeds

1. `supabase-security-audit-fixes.sql`
2. `supabase-seed-revenue-sample.sql`
3. `supabase-seed-reports-support.sql`

## Post-Migration Verification

Run:

- `supabase-setup-verification.sql`

Then manually validate:

1. Buyer can sign in and load profile.
2. Seller can create/edit listing.
3. Admin can open dashboard/users/sellers/reports.
4. Checkout -> order -> pickup flow still works.

## Phase 6: FYP buyer pack (after admin RLS exists)

1. `supabase-fyp-feature-pack.sql` — saved shops, disputes, feature flags (or use bundle below)
2. `supabase-buyer-schema-bundle.sql` — one-shot: `preferred_lang`, `order_disputes`, `platform_feature_flags`
3. `supabase-user-preferred-lang.sql` — if not using the bundle
4. `supabase-order-disputes-reply.sql` — admin reply columns + notification policy for **Send to customer**

See also [FYP-RUNBOOK.md](FYP-RUNBOOK.md) and the hub [MIGRATIONS.md](MIGRATIONS.md).

## Phase 7: Security hardening (run LAST)

1. `supabase-security-audit-fixes.sql` — function `search_path` + chat insert validation
2. `supabase-security-hardening.sql` — enforce profiles own/admin-only read (no public read; protects phone/email/bank fields) and restrict listing-image storage UPDATE/DELETE to the uploader

Verify profiles is not world-readable:

```sql
SELECT policyname, qual FROM pg_policies
WHERE tablename = 'profiles' AND cmd = 'SELECT';
```

No SELECT policy should have `qual = true`.

## Notes

- If a migration says a column/table already exists, keep going unless it creates/updates policies you still need.
- Do not skip RLS fixes in fresh production setups.
- Always run SQL in the same role (project owner/admin) for consistency.
