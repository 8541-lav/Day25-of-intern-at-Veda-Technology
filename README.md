# Task 25 – Multi-Table Sales Analysis (Global Superstore)

Combine orders, customers and returns for end-to-end relational analysis, with validated joins and no double counting.

**Tools:** SQL (SQLite dialect) · Power BI
**Dataset:** `GlobalSuperstore.xlsx` – sheets `Orders` (51,290 rows), `Customer` (1,590), `Returns` (1,173), `People` (13)

## Deliverables

| File | Purpose |
|---|---|
| `sales_analysis.sql` | Control totals, join validation, clean view `v_sales`, reconciliation, analysis queries |
| `Multi_Table_Sales_Analysis_Report.docx` | Full report: data model, join validation, 5 insights, dashboard design, interview answers |
| `README.md` | This file |

The Power BI `.pbix` is not included; the report (section 6) gives the layout and build steps.

## How to run

1. Load the four sheets into a SQLite database as tables named `orders`, `customers`, `returns`, `people`. Replace spaces and hyphens in column names with underscores (e.g. `Order ID` → `Order_ID`). Convert `Date of First Purchase` from Unix seconds to a date.
2. Open `sales_analysis.sql` in DB Browser for SQLite (or any SQLite client) and run it top to bottom.
3. For SQL Server or MySQL, swap `substr`/`GROUP_CONCAT` for `SUBSTRING`/`STRING_AGG` (or `GROUP_CONCAT` in MySQL); the rest is standard SQL.

## Join validation: what was found

| Check | Finding | Decision |
|---|---|---|
| Orders → Customer | Key unique, 0 orphans | Safe 1:1 join |
| Orders → Returns (Order ID) | 1,173 rows, 1,172 distinct IDs → join gives 51,295 rows, sales +$748 | Use `EXISTS` flag, not a join |
| Returns Market vs Orders Market | "United States" vs "US" → 296 return rows unmatched | Do not join on Market |
| Order ID across markets | 37 IDs appear in several markets | Order ID alone is not a perfect key |
| Orders → People | EMEA has no match (People says "AMEA"); Central/North/South reused across markets | People not joined |

**Reconciliation:** raw Orders and `v_sales` both give 51,290 rows, $12,642,501.91 sales, $1,467,457.29 profit.

## Five insights

1. Sales grew 90% (2016 $2.26M → 2019 $4.30M) with a steady 11–12% margin.
2. Discounts over 20% are loss-making (−17% and −74% margins); 11,328 such lines lost $814,682.
3. Tables is the only loss-making sub-category (−$64K); Copiers earn the most profit ($259K).
4. EMEA margin is 5.4% versus 11.6% overall.
5. 4.68% of orders (1,172) were returned, worth $819K of sales.

## Notes

- Order IDs embed a year exactly five years earlier than `Order Date`, so dates look shifted. `Order Date` was used as given.
- Returns are handled at order level because the sheet has no line, date or amount detail.
