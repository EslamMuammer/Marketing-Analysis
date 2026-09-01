# 🛒 E-Commerce Marketing & Customer Journey Analytics

![Power BI](https://img.shields.io/badge/PowerBI-F2C811?style=for-the-badge&logo=Power%20BI&logoColor=black)
![SQL](https://img.shields.io/badge/SQL-4479A1?style=for-the-badge&logo=postgresql&logoColor=white)
![Data Modeling](https://img.shields.io/badge/Data_Modeling-Star_Schema-brightgreen?style=for-the-badge)
---

## 📝 Project Overview
This project provides an end-to-end business intelligence solution to analyze marketing campaign performance, track customer engagement, and evaluate conversion funnels for an e-commerce platform. By integrating and processing raw data using **SQL** and visualizing it in **Power BI**, this project delivers actionable insights into customer behavior and product performance.

## 🎯 Business Questions Answered
* How are different content types (Blog, Video, Social Media) driving user engagement?
* What is the overall conversion rate, and at which stage do most customers drop off?
* Which products are driving the highest conversion rates and customer satisfaction?
* How does customer sentiment (derived from reviews) fluctuate over time?

## 🛠️ Tech Stack & Tools
* **Data Processing & Transformation:** SQL (Window Functions, CTEs, String Manipulation, Data Cleansing)
* **Data Modeling:** Star Schema (Fact and Dimension tables)
* **Data Visualization & Analytics:** Power BI, DAX (Data Analysis Expressions)

## 🗄️ Data Architecture & Modeling
To ensure optimal performance and scalable reporting, the data was structured using a **Star Schema** approach.
---

## 🖥️ Dashboard Preview

### Overview
![Overview](assets/screenshots/overview.png)

### Social Media Details
![Social Media Details](assets/screenshots/social_media_details.png)

### Conversion Details
![Conversion Details](assets/screenshots/conversion_details.png)

### Customer Review Details
![Customer Review Details](assets/screenshots/customer_review_details.png)

Full page-by-page walkthrough (visuals, filters, and how to read each chart) is documented in **[`docs/dashboard_guide.md`](docs/dashboard_guide.md)**.

---

## 📈 Dashboard & Key Insights

### 1️⃣ Social Media Performance
**Objective:** Evaluate top-of-funnel marketing metrics.
* **Key Metrics:** 9.079M Views, 1.785M Clicks, 414.1K Likes.
* **Insights:** Analyzed engagement across Blogs, Social Media, and Video content. Revealed monthly trends to identify peak interaction periods and high-performing products.

### 2️⃣ Conversion Funnel Analytics
**Objective:** Track the customer journey and identify drop-off points.
* **Key Metrics:** Overall Conversion Rate of **9.57%**.
* **Insights:** Visualized the complete funnel (View -> Click -> Drop-off -> Purchase). Highlighted top-converting products (e.g., Hockey Stick at 15.46%, Ski Boots at 14.61%) and tracked conversion rates month-over-month.

### 3️⃣ Brand Sentiment & Customer Reviews
**Objective:** Understand customer satisfaction and product feedback.
* **Key Metrics:** Average Rating of **3.69 / 5.0**.
* **Insights:** Created a distribution of 1-5 star ratings Monitored average rating fluctuations over time and ranked products by average satisfaction score

> Full metric definitions and business interpretation are in **[`docs/kpis_and_insights.md`](docs/kpis_and_insights.md)**.


---

## 🧱 Data Model

Star schema with **3 fact tables** and **3 dimension tables**

- **Fact tables:** `fact customer journey`, `fact customer engagement`, `fact customer reviews`
- **Dimension tables:** `dim date`, `dim products`, `dim customers`

![Data Model](assets/screenshots/data_model.png)

Full relationship cardinalities, keys, and measure list: **[`powerbi/model_documentation.md`](powerbi/model_documentation.md)**.


## 📂 Documentation Index

| Document | Purpose |
|---|---|
| [`docs/data_dictionary.md`](docs/data_dictionary.md) | Every table and column, source, type, and description |
| [`docs/methodology.md`](docs/methodology.md) | Step-by-step analytical workflow from raw data to dashboard |
| [`docs/kpis_and_insights.md`](docs/kpis_and_insights.md) | KPI formulas and what each one means for the business |
| [`docs/dashboard_guide.md`](docs/dashboard_guide.md) | How to read and use each dashboard page |
| [`powerbi/model_documentation.md`](powerbi/model_documentation.md) | Data model schema and DAX measure inventory |
| [`data/README.md`](data/README.md) | Data source, provenance, and licensing notes |