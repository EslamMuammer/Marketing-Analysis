# Data Documentation

## Source

**`TODO`** — The original provider/source of the raw Food Mart dataset was not included with the project files supplied, so it is not stated here to avoid misattribution. Common sources for grocery-retail sample datasets of this shape (multi-year sales, returns, customers, products, stores, region files) include Microsoft's classic **FoodMart** demo database and various public "Data Playground" style practice datasets. **Please confirm the exact source/license and update this section before publishing the repo** — recruiters and reviewers will look for this.

Once confirmed, replace this section with something like:
```
Source: <provider name>
URL: <link>
License: <license type>
Retrieved: <date>
```

## Raw files expected in `data/raw/`

The cleaning notebook (`notebooks/01_data_cleaning_and_preparation.ipynb`) expects the following seven files in `data/raw/`. **These files are not committed to this repository** (see `.gitignore`) — add your own copies before running the notebook.

| File | Loaded as | Description |
|---|---|---|
| `Sales 2017.csv` | `sales2017` | Transaction-level sales records for calendar year 1997 (see note below on year naming) |
| `Sales 2018.csv` | `sales2018` | Transaction-level sales records for calendar year 1998 |
| `Customers.csv` | `customers` | Customer master data (demographics, account info) |
| `Products.csv` | `products` | Product master data (brand, cost, price, weight, attributes) |
| `Region.csv` | `regions` | Sales district / region reference data |
| `Returns.csv` | `returns` | Product return transactions |
| `Stores.csv` | `stores` | Store master data (location, size, format, open/remodel dates) |

> **Note on file naming vs. content:** the files are named "Sales 2017" / "Sales 2018" but the dashboard's Revenue Trend visual labels the two years as **1997** and **1998**. `TODO`: confirm which labeling is correct and reconcile the filenames/notebook comments if needed.

## Processed files produced in `data/processed/`

Running the notebook end-to-end writes the following cleaned, de-duplicated, type-cast Parquet files (not committed — regenerate locally):

| File | Contents |
|---|---|
| `Sales.parquet` | 1997 + 1998 sales concatenated and de-duplicated |
| `customers.parquet` | Cleaned customer dimension (dates cast to datetime) |
| `products.parquet` | Cleaned product dimension (`recyclable`, `low_fat` cast to int flags) |
| `regions.parquet` | Cleaned region/district reference data (typo `"Marida"` corrected to `"Merida"`) |
| `returns.parquet` | Cleaned returns fact data |
| `stores.parquet` | Cleaned store dimension (dates cast to datetime) |

These Parquet files are what the Power BI model was ultimately built on top of (via Power Query, pointing at `data/processed/`).

## Data quality notes surfaced during cleaning

- All seven raw tables were checked for exact duplicate rows and de-duplicated (`.drop_duplicates()`).
- Date columns across all tables were explicitly cast from string to `datetime64` (transaction/stock dates in sales, birthdate/account-open date in customers, return date in returns, first-opened/last-remodel dates in stores).
- `products.recyclable` and `products.low_fat` had missing values, which were filled with `0` and cast to integer flags (i.e., missing was treated as "No").
- Cross-checking store cities against region sales districts revealed a one-off spelling mismatch — `"Marida"` in the region reference data was corrected to `"Merida"` to match the store city name, ensuring the two datasets join cleanly.

## Privacy / PII note

The `customers` table contains address-level fields (`customer_address`, `customer_city`, `customer_postal_code`, `customer_state_province`, `customer_country`) and an account number. **`TODO`:** confirm whether this is synthetic/sample data safe for a public portfolio repo before publishing raw customer-level files anywhere, even locally in a public repo's history.
