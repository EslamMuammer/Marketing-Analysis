# Project Methodology

This document outlines the step-by-step methodology and technical approach applied to build the Marketing & Customer Journey Analytics solution.

## Phase 1: Data Understanding & Extraction
* **Source Identification:** Identified the core datasets required for analysis, including `customers`, `geography`, `products`, `customer_journey`, `customer_reviews`, and `engagement_data`.
* **Exploratory Data Analysis (EDA):** Evaluated raw data in SQL to identify data quality issues, missing values, and formatting inconsistencies before modeling.

## Phase 2: Data Cleaning & Transformation (SQL)
All pre-processing was executed using SQL to ensure the data was clean, structured, and ready for analytical modeling. Key operations included:
* **String Manipulation:** Extracted distinct `Views` and `Clicks` values from a combined `ViewsClicksCombined` string using `CHARINDEX`, `LEFT`, and `RIGHT` functions.
* **Text Cleansing:** Sanitized the `ReviewText` column in the reviews table by standardizing spacing (replacing double spaces with single spaces).
* **Missing Value Imputation:** Handled missing `Duration` values in the customer journey table by applying Window Functions (`AVG() OVER()`) to calculate and insert the average duration per `VisitDate`.
* **Deduplication:** Applied `ROW_NUMBER()` partitioned by key journey attributes to filter out duplicate event logs.
* **Feature Engineering:** Created a new `PriceCategory` column using `CASE WHEN` statements to segment products into 'Low', 'Medium', and 'High' tiers.

## Phase 3: Data Modeling (Power BI)
* **Schema Design:** Structured the cleaned tables into a **Star Schema** optimized for Power BI's VertiPaq engine.
* **Dimension Tables:** Built `Dim_Customers`, `Dim_Products`, and a dedicated `Calendar` table for time-intelligence.
* **Fact Tables:** Established `Fact_CustomerJourney`, `Fact_CustomerReviews`, and `Fact_EngagementData`.
* **Relationships:** Configured one-to-many, single-direction relationships from the dimension tables to the fact tables to ensure accurate cross-filtering.

## Phase 4: DAX & Metric Calculations
* **Measure Centralization:** Created a dedicated `_measures` table to organize all DAX calculations logically.
* **Core KPIs:** Developed aggregations for `Views`, `Clicks`, and `Likes`.
* **Advanced Metrics:** Calculated the `Conversion Rate` and `Avg Rating` to evaluate funnel efficiency and brand sentiment dynamically based on filter contexts.

## Phase 5: Visualization & UI Design
Designed a 4-page interactive dashboard tailored for different analytical needs:
* **Overview:** High-level executive summary combining all key metrics.
* **Social Media Details:** Top-of-funnel engagement tracking by content type.
* **Conversion Details:** Funnel drop-off visualization and product conversion ranking.
* **Customer Review Details:** Post-purchase sentiment analysis and qualitative feedback tracking.
* **UX Consistency:** Applied a uniform filter pane (Year, Month, Product Name) across all pages to provide a seamless drill-down experience.