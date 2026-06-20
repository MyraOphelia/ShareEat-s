# ShareEat – How Seller Earnings Work

This document explains **how much sellers earn** when customers buy, and how the Revenue dashboard tracks it. All data is saved in the database for admin tracking.

---

## 1. The Earnings Flow (When User Buys)

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Buyer pays    │     │  Order placed   │     │ Seller earns    │
│  (at checkout)  │ ──► │  (order_items   │ ──► │ (when completed)│
│                 │     │   created)      │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
       │                          │                        │
       │                          │                        │
  total = subtotal            Each item has:          Your share =
  + service_fee               quantity × unit_price   sum of your items
  (buyer pays all)            per seller              (completed only)
```

### Step-by-step

1. **Buyer adds items to bag** → goes to checkout  
2. **Checkout** → Creates `orders` row + `order_items` rows. Each `order_item` has:
   - `seller_id` (who gets paid)
   - `quantity` × `unit_price` = **gross sale** for that item  
3. **Order lifecycle** → Pending → Seller Accepts → Ready → **Completed** (seller marks “Complete Order” or buyer completes pickup)  
4. **Revenue counts** → Only when the order/item is **completed**. Pending, rejected, or cancelled orders do **not** count as earnings.

---

## 2. What the Seller Earns

| Term | Meaning |
|------|---------|
| **Gross sales** | Sum of `(quantity × unit_price)` for all your completed order items |
| **Platform fee** | ShareEat takes 40% of gross sales. |
| **Promotion discount** | If buyer used a promo code, the discount may reduce seller payout (configurable) |
| **Net revenue** | Gross sales − Platform fee (40%) = **what you actually earn** (60% of gross) |

For the current setup:

- **Platform fee**: 40% of gross sales (deducted by ShareEat)  
- **Seller gets**: 60% of gross sales (`quantity × unit_price` for their items, minus 40% fee)  
- **Service fee**: Paid by buyer; platform keeps it (seller is not charged)  
- **Promotions**: Discount is applied at checkout; for simplicity, seller still receives full item price (promo cost can be tracked separately for future use)

---

## 3. Dashboard Metrics (What You See)

| Metric | How it’s calculated |
|--------|----------------------|
| **Today’s revenue** | Sum of your completed order items where `completed_at` or order date is today |
| **This week** | Same logic, for current week |
| **This month** | Same logic, for current month |
| **Recent transactions** | Last 10–20 completed orders with your items |
| **Revenue breakdown** | Gross sales, any fees, net revenue |

---

## 4. Database Tables (Admin Tracking)

| Table | Purpose |
|-------|---------|
| **orders** | One row per checkout; `completed_at` marks when order is done |
| **order_items** | One row per item; `seller_id`, `quantity`, `unit_price`, `status` |
| **seller_revenue_snapshots** | (Optional) Daily/monthly aggregates so admin can query totals without recomputing |

Admin can:

- Query `order_items` + `orders` to get total revenue per seller  
- Use `seller_revenue_snapshots` (if created) for fast reporting  
- Filter by date range, seller, or status  

---

## 5. Summary

- **When** a buyer pays and the order is **completed**, the seller earns `quantity × unit_price` for each of their items.  
- The **Revenue** dashboard shows today, this week, this month, and recent transactions.  
- All values come from `orders` and `order_items` and can be stored in `seller_revenue_snapshots` for admin tracking.
