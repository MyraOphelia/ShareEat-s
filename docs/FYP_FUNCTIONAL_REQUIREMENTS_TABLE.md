# Functional requirements — table copy (ShareEat)

Use these rows in your FYP document. Adjust section numbers (e.g. 3.3.1.17) to match your chapter outline.

---

## Fix: heading must match the table (was mismatched)

The section titled **“Verify Pickup Codes”** had been paired with a table that actually described **sales and revenue**. Use the heading below with that table.

### 3.3.1.17 Sales and Revenue Monitoring

**Table caption (example):** Table X: Functional requirement — Sales and revenue monitoring (seller)

| Field | Value |
|--------|--------|
| **Function** | Sales and Revenue Monitoring |
| **Area** | Functional (Seller) |
| **Description** | Sellers can view transaction performance, completed orders, and revenue-related insights from the platform dashboards. |
| **Implementation Status** | Completed |

**Implementation note (optional in thesis):** e.g. `seller-analytics.html` and related seller dashboard flows after Supabase is configured.

---

## Separate requirement: pickup verification (optional extra row)

If your specification still needs **“Verify Pickup Codes”** as its own item, give it **its own number** — do not reuse the sales/revenue row.

### Example: Verify pickup / collection (wording aligned with ShareEat)

| Field | Value |
|--------|--------|
| **Function** | Verify Pickup / Collection |
| **Area** | Functional (Buyer & Seller) |
| **Description** | Buyers complete pickup using order-linked secure links and on-device confirmation (e.g. swipe to complete). Sellers manage fulfilment via order workflow (accept, ready, complete). Optional friend-pickup links use a token in the URL for controlled access. |
| **Implementation Status** | Completed *(or “Partial” if your examiner requires a dedicated numeric code entry at the counter)* |

---

## Quick checklist before submission

- [ ] Section title for **#17** reads **Sales and Revenue Monitoring** (not “Verify Pickup Codes”) if the table is about dashboards and revenue.
- [ ] **Verify Pickup Codes** appears only as a **separate** numbered requirement, with a description that matches the app (tokens + workflow), if your template requires it.
