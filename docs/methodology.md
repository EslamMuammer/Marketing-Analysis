# Methodology & Analytical Workflow

This document describes the end-to-end process from raw source files to the final interactive dashboard, matching what is implemented in `notebooks/01_data_cleaning_and_preparation.ipynb` and `powerbi/Food_Mart_Retail_Performance.pbix`.

## Workflow Overview

```
Raw CSVs (7 files)
      │
      ▼
[1] Ingestion (Pandas)
      │
      ▼
[2] Structural profiling  (shape, dtypes, column inventory)
      │
      ▼
[3] Deduplication  (.drop_duplicates() on every table)
      │
      ▼
[4] Type correction  (string → datetime for all date columns)
      │
      ▼
[5] Fact table consolidation  (Sales 2017 + Sales 2018 → single Sales table)
      │
      ▼
[6] Categorical/value profiling  (city, state, country, brand distributions)
      │
      ▼
[7] Null handling  (recyclable / low_fat → filled with 0, cast to int flags)
      │
      ▼
[8] Cross-source consistency check  (store cities vs. region sales districts)
      │
      ▼
[9] Reference data correction  ("Marida" → "Merida" typo fix)
      │
      ▼
[10] Export to Parquet  (data/processed/*.parquet)
      │
      ▼
[11] Power BI: Load Parquet → build star-schema model → relationships
      │
      ▼
[12] Power BI: DAX measures  (_measures table)
      │
      ▼
[13] Power BI: 4-page report build  (Overview / Sales & Product / Customer & Store / Returns & Quality)
```

## Step Detail

### 1–2. Ingestion & Profiling
Each of the seven raw CSVs (`Sales 2017`, `Sales 2018`, `Customers`, `Products`, `Region`, `Returns`, `Stores`) is loaded independently with Pandas. Before any transformation, each table's shape, column list, and a data preview (`.head()`) are inspected to understand structure and catch obvious issues early.

### 3. Deduplication
Every table is copied and passed through `.drop_duplicates()`. This is a conservative, table-wide exact-duplicate check — it does not attempt fuzzy or business-key-level deduplication (e.g., it would not catch a re-inserted row with a different transaction ID). **`TODO`**: if business-key-level duplicates are a concern, add a follow-up check (e.g., duplicate `(customer_id, product_id, transaction_date)` combinations in `fact sales`).

### 4. Type Correction
Date fields arrive as strings from CSV and are explicitly cast to `datetime64`:
- `fact sales`: `transaction_date`, `stock_date`
- `dim customers`: `birthdate`, `acct_open_date`
- `fact returns`: `return_date`
- `dim stores`: `first_opened_date`, `last_remodel_date`

This is necessary for correct time-intelligence behavior later in Power BI (year/quarter/month trend charts, date-range slicers).

### 5. Fact Table Consolidation
`Sales 2017` and `Sales 2018` are structurally identical extracts (same columns) and are concatenated into a single `Sales` fact table, then de-duplicated again post-concatenation to catch any overlap between the two source files.

### 6. Categorical Profiling
Value counts are run on key categorical fields (`customer_city`, `customer_state_province`, `customer_country`) to sanity-check cardinality and spot obvious data entry issues before they reach the model.

### 7. Null Handling
`dim products.recyclable` and `dim products.low_fat` contain missing values in the raw data. These are treated as **"No"** (filled with `0`) and cast to integer flags — a defensible default for boolean-style attributes, but one that should be flagged to stakeholders since "unknown" and "no" are being treated identically. **`TODO`**: confirm with the business/data owner whether this assumption is acceptable, or whether nulls should instead be tracked separately (e.g., "Unknown" category).

### 8–9. Cross-Source Consistency Check & Correction
Store cities (`dim stores.store_city`) and region sales districts (`regions.sales_district`) are compared as sets to confirm every store maps cleanly to a valid region for reporting. This surfaced one mismatch — `"Marida"` in the region file, which should have read `"Merida"` to match the corresponding store city. The region file is corrected in place before being used downstream. This is a good example of **defensive, cross-table data validation** rather than assuming referential integrity.

### 10. Export
All cleaned tables are written to columnar **Parquet** format in `data/processed/`, which:
- preserves the datetime types corrected in step 4 (unlike CSV round-trips),
- is significantly faster and smaller to load into Power BI than re-parsing raw CSVs,
- gives a stable, versionable "clean data" checkpoint independent of the BI layer.

### 11–13. Power BI Modeling & Reporting
The cleaned Parquet files are loaded into Power BI and connected via a **star schema**: two fact tables (`fact sales`, `fact returns`) each many-to-one to four shared dimension tables (`dim date`, `dim products`, `dim stores`, `dim customers`). All dashboard KPIs and chart values are computed through DAX measures centralized in a `_measures` table (see `powerbi/model_documentation.md`) rather than being calculated ad hoc per visual — this keeps metric definitions consistent across all four report pages. The report itself is organized into four purpose-built pages, each answering one of the business objectives listed in the main `README.md`.

## Tooling Rationale

| Choice | Why |
|---|---|
| Pandas for cleaning, not Power Query alone | Easier to unit-test, version-control, and document transformation logic in a notebook than in Power Query's UI-driven steps; also faster to iterate on large multi-file joins/profiling |
| Parquet as the hand-off format | Preserves types, compresses well, loads fast — better fit than re-exporting to CSV |
| Centralized DAX measures (`_measures` table) | Single source of truth for KPI logic across 4 report pages — avoids metric drift between pages |
| Star schema (vs. one flat table) | Keeps fact tables at transaction grain, avoids fan-out/duplication, and lets slicers (region, store type, product, brand) filter both sales and returns consistently |
