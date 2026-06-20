# Visualize schema / ER diagram from SQL (Option C)

Ways to generate an ER diagram from the ShareEat Supabase (PostgreSQL) schema **without** running a full dump.

---

## 1. From a live connection (recommended)

Connect to your Supabase Postgres instance; the tool introspects the DB and draws the diagram.

### DBeaver CE (free)

1. Install [DBeaver Community](https://dbeaver.io/download/).
2. Create a connection: **Database → New Database Connection → PostgreSQL**.
3. Use your Supabase connection details:
   - Host: `db.<project-ref>.supabase.co`
   - Port: `5432`
   - Database: `postgres`
   - User / password: from Supabase **Settings → Database**.
4. **Right‑click the connection → View Diagram** (or **ER Diagram**), or right‑click a schema/table and add to diagram.
5. Arrange tables; export as image if needed.

### pgAdmin (step by step)

**Step 1 — Install pgAdmin**

1. Go to [pgAdmin download](https://www.pgadmin.org/download/).
2. Choose your Mac build: **arm64** for Apple Silicon (M1/M2/M3/M4), **x64** for Intel.
3. Download the `.dmg`, open it, and drag pgAdmin 4 into Applications.
4. Open pgAdmin 4 from Applications. Set a master password when asked (you’ll use it once per session).

---

**Step 2 — Get your Supabase connection details**

1. Open your [Supabase](https://supabase.com) project.
2. Go to **Project Settings** (gear icon in the left sidebar).
3. Click **Database** in the left menu.
4. Under **Connection string**, choose **URI** or note:
   - **Host:** `db.xxxxxxxxxxxxx.supabase.co` (your project ref in the middle).
   - **Port:** `5432`
   - **Database:** `postgres`
   - **User:** `postgres` (or the user shown).
   - **Password:** click **Reveal** and copy it (or use “Reset database password” if you forgot).

Keep this tab open or copy the values somewhere temporary.

---

**Step 3 — Add the server in pgAdmin**

1. In pgAdmin’s left **Browser** panel, right‑click **Servers**.
2. Click **Register → Server**.
3. **General** tab:
   - **Name:** e.g. `ShareEat Supabase` (any name you like).
4. **Connection** tab:
   - **Host name/address:** paste the host (e.g. `db.xxxxxxxxxxxxx.supabase.co`).
   - **Port:** `5432`
   - **Maintenance database:** `postgres`
   - **Username:** `postgres`
   - **Password:** paste the Supabase database password; optionally check **Save password**.
5. Click **Save**. If it connects, you’ll see your server and under it **Databases**.

---

**Step 4 — Browse your schema (tables)**

1. In the left tree, expand: **Servers → ShareEat Supabase → Databases**.
2. Expand **postgres**.
3. Expand **Schemas**.
4. Expand **public**.
5. Click **Tables**. You’ll see all your Supabase tables (e.g. `profiles`, `listings`, `orders`). Click a table to see columns, indexes, etc. in the right-hand panels.

---

**Step 5 — Run SQL (Query Tool)**

1. Right‑click **postgres** (the database, not the server).
2. Click **Query Tool**.
3. Type or paste SQL, e.g. `SELECT * FROM profiles LIMIT 5;`.
4. Press **F5** or click the play (▶) button to run.

---

**Step 6 — ER diagram in pgAdmin**

pgAdmin 4 does not include a built‑in “Generate ER diagram” for all versions. You can:

- **Option A:** Use the **Dashboard** (right‑click the database → **Dashboard**) for a high‑level overview.
- **Option B:** Install the **ERD Forge** extension if available for your pgAdmin version (Help → or check [pgAdmin docs](https://www.pgadmin.org/docs/)).
- **Option C:** Use **DBeaver** (see above) for the diagram: connect with the same Supabase details, then right‑click the connection → **View Diagram** to get a full ER diagram from the same database.

---

## 2. From SQL files (no live DB)

When you only have SQL (e.g. project `.sql` files), use DBeaver’s reverse‑engineer from script.

### DBeaver

1. **Database → SQL Editor → Open SQL Script**, or **File → New → SQL Script**.
2. Paste or open one or more of the project’s schema SQL files (see below).
3. **Right‑click in the script → Parse SQL** (or use the parser so DBeaver builds a model).
4. Alternatively: **Database → Reverse Engineer → From SQL script**, then point at your `.sql` file(s). DBeaver will try to build a project/diagram from the DDL.

Best results if the SQL is clean DDL (CREATE TABLE, ALTER TABLE, etc.). Migrations and small scripts can be combined into one script for a fuller picture.

---

## 3. Online tools (avoid for sensitive data)

- **[dbdiagram.io](https://dbdiagram.io)**  
  Define schema in their DSL or paste/convert SQL. Good for quick diagrams; keep schema only (no real data) and avoid production credentials.

---

## Main schema SQL files in this project

Use these for “from SQL file” workflows or as a checklist for what’s in the DB:

| File | Purpose |
|------|--------|
| `supabase-profiles-trigger.sql` | Profiles + trigger |
| `supabase-listings.sql` | Listings table + RLS |
| `supabase-orders.sql` | Orders + order_items |
| `supabase-promotions.sql` | Promotions |
| `supabase-chat-messages.sql` | Chat messages |
| `supabase-chat-extras.sql` | Escalations, satisfaction |
| `supabase-chat-unmatched.sql` | Unmatched chat queries |
| `supabase-chat-promo-check.sql` | Promo check RPC |
| `supabase-buyer-notifications.sql` | Buyer notifications |
| `supabase-seller-notifications.sql` | Seller notifications |
| `supabase-notifications-cascade.sql` | Notification cascade |
| `supabase-ratings-reviews.sql` | Ratings/reviews |
| `supabase-listings-contact.sql` | Listings contact |
| `supabase-listings-migration-*.sql` | Address, store_name, tags, category, location |
| `supabase-storage-policies.sql` | Storage buckets/policies |
| `supabase-user-features.sql` | User features |
| `supabase-profile-features.sql` | Profile features |
| `supabase-audit-log.sql` | Audit log |

Running order for a fresh DB usually follows the names (profiles first, then listings, orders, etc.); check comments and dependencies inside each file.

---

## Quick reference

- **Live Supabase DB** → DBeaver or pgAdmin: connect and generate ER diagram from the connection.
- **Only SQL files** → DBeaver: reverse‑engineer from SQL script / open script and parse.
- **Quick shareable diagram** → dbdiagram.io with schema-only (no sensitive data).
