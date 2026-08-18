# Data Dictionary

This dictionary covers the tables as they exist in the **final Power BI data model** (star schema), cross-referenced with the raw/source columns seen in the cleaning notebook and the model relationship diagram. Columns confirmed directly from the `.pbix` model diagram or the notebook are marked accordingly; anything inferred or unconfirmed is marked `TODO`.

Legend: **Confirmed (diagram)** = visible in the Power BI model relationship view · **Confirmed (notebook)** = referenced in the Python cleaning code · **TODO** = not directly observed, needs verification.

---

## Fact Tables

### `fact sales`
Transaction-level grain: one row per product sold in a transaction.

| Column | Type | Source | Description |
|---|---|---|---|
| `customer_id` | Integer (FK) | Confirmed (diagram) | Links to `dim customers.customer_id` |
| `product_id` | Integer (FK) | Confirmed (diagram) | Links to `dim products.product_id` |
| `quantity` | Numeric (Σ) | Confirmed (diagram) | Units sold in the transaction line |
| `stock_date` | Date | Confirmed (diagram, notebook) | Date the stock was recorded/received |
| `store_id` | Integer (FK) | Confirmed (diagram) | Links to `dim stores.store_id` |
| `transaction_date` | Date | Confirmed (diagram, notebook) | Date of the sale transaction; primary join to `dim date.Date` |

> `TODO`: confirm whether `fact sales` also carries a price/revenue column directly, or whether revenue is derived entirely via DAX from `product_retail_price × quantity` (the latter appears to be the case based on the measure list — see `powerbi/model_documentation.md`).

### `fact returns`
Transaction-level grain: one row per returned product line.

| Column | Type | Source | Description |
|---|---|---|---|
| `product_id` | Integer (FK) | Confirmed (diagram) | Links to `dim products.product_id` |
| `quantity` | Numeric (Σ) | Confirmed (diagram) | Units returned |
| `return_date` | Date | Confirmed (diagram, notebook) | Date of the return |
| `store_id` | Integer (FK) | Confirmed (diagram) | Links to `dim stores.store_id` |

---

## Dimension Tables

### `dim date`
Standard calendar dimension.

| Column | Type | Description |
|---|---|---|
| `Date` | Date | Primary key; one row per calendar day |
| `Day` | Integer | Day of month |
| `Day Name` | Text | Abbreviated weekday name |
| `Day Name Long` | Text | Full weekday name |
| `Month` | Integer | Month number (1–12) |
| `Month Name` | Text | Month name (used in Overview monthly trend chart) |
| `Quarter` | Text/Integer | Calendar quarter (used in Overview quarterly trend chart) |
| `Year` | Integer | Calendar year (1997 / 1998 in this dataset — see `data/README.md` note on year labeling) |

### `dim products`
| Column | Type | Description |
|---|---|---|
| `product_id` | Integer | Primary key |
| `product_name` | Text | Product display name (e.g., "Rye Bread", "Thai Rice") |
| `product_brand` | Text | Brand (e.g., "Hermanos", "Tell Tale", "Ebony") |
| `product_cost` | Numeric (Σ) | Unit cost to the business |
| `product_retail_price` | Numeric (Σ) | Unit retail/selling price |
| `product_weight` | Numeric (Σ) | Product weight |
| `low_fat` | Integer flag (0/1) | 1 = low-fat product; cleaned from raw nulls → 0 |
| `recyclable` | Integer flag (0/1) | 1 = recyclable packaging; cleaned from raw nulls → 0 |

### `dim stores`
| Column | Type | Description |
|---|---|---|
| `store_id` | Integer | Primary key |
| `store_name` | Text | Store display name (e.g., "Store 13") — `TODO`: confirm exact column name, inferred from visuals |
| `store_type` | Text | Format: Supermarket, Deluxe Supermarket, Gourmet Supermarket, Mid-Size Grocery, Small Grocery |
| `store_city` | Text | City the store is located in |
| `store_country` | Text | Country the store is located in |
| `sales_region` | Text | Region label used in dashboard slicers/charts (e.g., "North West", "Mexico Central") |
| `region_id` | Integer (FK) | `TODO`: confirm whether this links to a separate region table or is a flat attribute |
| `grocery_sqft` | Numeric (Σ) | Grocery floor area — used as a size proxy in the "Sales by Store Size" scatter chart |
| `total_sqft` | Numeric (Σ) | Total store floor area — `TODO`: confirm exact usage vs. `grocery_sqft` |
| `first_opened_date` | Date | Store opening date (cleaned to datetime in notebook) |
| `last_remodel_date` | Date | Most recent remodel date (cleaned to datetime in notebook) |

### `dim customers`
| Column | Type | Description |
|---|---|---|
| `customer_id` | Integer | Primary key |
| `customer_acct_num` | Numeric | Account number |
| `customer_address` | Text | Street address |
| `customer_city` | Text | City |
| `customer_state_province` | Text | State/province |
| `customer_country` | Text | Country |
| `customer_postal_code` | Text | Postal/ZIP code |
| `gender` | Text | `F` / `M` — used in Customer & Store Analytics page |
| `marital_status` | Text | `S` (single) / `M` (married) |
| `education` | Text | Highest education level (Partial High School, High School, Partial College, Bachelors Degree, Graduate Degree) |
| `occupation` | Text | Occupation category (Professional, Skilled Manual, Manual, Management, Clerical) |
| `yearly_income` | Text/Numeric (binned) | Income band (e.g., "$30K–$50K") shown in "Customer Income vs Sales" chart |
| `birthdate` | Date | Cleaned to datetime in notebook |
| `acct_open_date` | Date | Cleaned to datetime in notebook |

### `regions` (raw / cleaning stage only — see TODO)
| Column | Type | Description |
|---|---|---|
| `sales_district` | Text | Confirmed (notebook) — corrected `"Marida"` → `"Merida"` to match `store_city` values |
| *(other columns)* | — | `TODO`: not directly observed; inspect `Region.csv` once available to complete this row set |

> **Open question:** the final Power BI model diagram does not show a standalone `dim region` table — only `dim stores`, `dim date`, `dim products`, `dim customers`, and the two fact tables. `sales_region`/`region_id` live directly on `dim stores`. It's likely the `regions` table was used only during Python-stage cleaning/validation (to catch the city/district mismatch) and its useful attributes were folded into `dim stores`, rather than loaded into the model as its own table. **`TODO`**: confirm this in Power BI Desktop before finalizing documentation.

---

## Derived / Calculated Fields (DAX Measures)

All KPI cards and value axes in the dashboard are powered by measures in a dedicated `_measures` table rather than raw columns. See **[`powerbi/model_documentation.md`](../powerbi/model_documentation.md)** for the full measure inventory and (once completed) formula definitions.
