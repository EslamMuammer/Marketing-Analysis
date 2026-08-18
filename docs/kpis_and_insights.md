# KPIs, Definitions & Business Insights

## KPI Reference

Values below are the totals shown on the dashboards (default filter state: all regions, all store types, full date range, no slicer selections). Formula logic is the standard/expected DAX pattern for each metric name — **`TODO`: paste in the exact DAX from Power BI Desktop's Measure pane to confirm/replace the "Expected logic" column below.**

| Measure (`_measures` table) | Value shown | Expected logic | Business meaning |
|---|---|---|---|
| **Units Sold** | 825K | `SUM(fact sales[quantity])` | Total product units sold across all transactions |
| **Sales Value / Net Sales Value** ("Revenue") | 1.7M | `SUMX(fact sales, fact sales[quantity] * RELATED(dim products[product_retail_price]))` | Total gross revenue from sales |
| **Gross Profit** | 1.1M | `SUMX(fact sales, fact sales[quantity] * (RELATED(dim products[product_retail_price]) - RELATED(dim products[product_cost])))` | Revenue minus cost of goods sold |
| **Gross Margin %** | 59.7% | `DIVIDE([Gross Profit], [Net Sales Value])` | Profitability efficiency — how much of every revenue dollar is retained after product cost |
| **Active Customers / # Customers** | 8.84K | `DISTINCTCOUNT(fact sales[customer_id])` | Number of unique customers who made ≥1 purchase in the filtered period |
| **Return Rate %** | 0.99% | `DIVIDE([Returned Units], [Units Sold])` | Share of sold units that come back as returns — a proxy for product/quality issues |
| **Returned Units** | 8.3K | `SUM(fact returns[quantity])` | Total units returned |
| **Returned Value** | — | `SUMX(fact returns, fact returns[quantity] * RELATED(dim products[product_retail_price]))` | Revenue-equivalent value of returned goods |
| **Return Records** | — | `COUNTROWS(fact returns)` | Number of distinct return transactions/lines |
| **Sales Value per Customer** | 199.56 | `DIVIDE([Net Sales Value], [Active Customers])` | Average revenue generated per customer |
| **Units per Customer** | 94.26 | `DIVIDE([Units Sold], [Active Customers])` | Average purchase volume per customer |
| **Net Units** | — | `TODO` — likely units sold net of returns; confirm exact formula | Net product movement after accounting for returns |

> These 13 measures were confirmed directly by inspecting the Power BI model's field references (`_measures.<name>`) embedded in the report layout — the **names are exact**, but the **formula text** above is the standard/expected pattern for a measure with that name, not a copy of the literal DAX. Please verify and replace with the real formulas from Power BI Desktop → Modeling → Measures.

## Page-Level KPI Cards

| Page | KPI Cards Shown |
|---|---|
| Overview | Units Sold · Revenue · Gross Profit · Gross Margin % · # Customers · Return Rate % |
| Sales & Product Performance | Units Sold · Revenue · Gross Profit · Gross Margin % · Returned Units |
| Customer & Store Analytics | # Customers · Sales/Customer · Qty/Customer · Revenue · Units Sold · Gross Profit |
| Returns & Quality | Returned Units · Returned Value · Return Rate % · Revenue · Units Sold · # Return Records |

---

## Business Insights (Observed from Dashboard)

The insights below describe **what the charts show**. They are intentionally kept close to the data (no unverified causal claims); each is paired with a suggested next step for deeper investigation.

### 1. Profitability is strong but concentrated by format
Revenue is dominated by **Supermarket (44.75%)** and **Deluxe Supermarket (37.94%)** store formats, with Gourmet, Mid-Size, and Small Grocery formats together contributing under 20% of revenue.
> **Next step:** compare *margin %*, not just revenue share, by store type — a smaller format could still be more profitable per square foot. `grocery_sqft` is available in `dim stores` to build a revenue/profit-per-sqft view.

### 2. Regional demand is highly uneven
**North West** leads all regions at 839.5K in sales — more than 2.6x the second-place region (**South West**, 317.7K). **Central West** trails far behind at just 9.3K.
> **Next step:** investigate whether this reflects store count/density differences (more stores in North West) or genuinely higher demand per store — normalize by store count or `grocery_sqft` before concluding North West is the strongest market per-store.

### 3. Returns are concentrated, not evenly spread
While the overall return rate is low (0.99%), specific stores (**Store 17**, **Store 13**, **Store 11**) and specific brands (**Hermanos**, **Horatio**, **Tri-State**) account for a disproportionate share of return volume.
> **Next step:** cross-reference top-returned brands against their sales volume — a brand can look "high-return" simply because it also sells the most. Return **rate** by brand (not raw volume) is the fairer comparison; the current "Top Returns Brands" chart uses raw counts.

### 4. Revenue grows steadily through the year, peaking in Q4
Both 1997 and 1998 show a consistent step-up from Q1 to Q4, with **Q4 the strongest quarter both years** (151.2K in the trend chart) and November/December the strongest individual months (165K / 175K).
> **Next step:** confirm whether this is genuine seasonal demand (holiday grocery shopping) or a store-count/expansion effect (more stores open by year-end) — the store `first_opened_date` field can help separate the two.

### 5. Customer base is demographically balanced
Gender split is **49.45% F / 50.55% M**, marital status is **49.59% S / 50.41% M** — there's no strong demographic skew driving the revenue concentration seen in points 1–2. The **"Professional"** occupation segment is the largest revenue contributor (33.18%), followed by **"Skilled Manual"** (24%).
> **Next step:** pair occupation/income segments with product category preferences (e.g., low-fat, recyclable-packaged items) to see if there's a targetable segment for premium/health-oriented product lines.

### 6. Price and profit scale together, with a few high-value outliers
The "Revenue vs Gross Profit by Product Price" scatter shows a tight, near-linear relationship for most products, with one clear outlier point at a much higher price/profit level than the rest of the cluster.
> **Next step:** identify that outlier product specifically and confirm whether it's a data entry issue (e.g., wrong unit of price) or a genuine premium SKU worth featuring/promoting.

---

## Suggested Follow-Up Analysis (Not Yet Done — `TODO`)
- Profit margin normalized by store size (`grocery_sqft`) and by region.
- Return **rate** (not raw count) by brand and by store, to separate "sells a lot" from "has a quality problem."
- Cohort/retention view of customers using `acct_open_date` (currently loaded but not visualized).
- Statistical significance check on the Q4 seasonality pattern once more years of data are available (only 1997–1998 currently in scope).
