This document outlines the key performance indicators (KPIs), metric definitions, and actionable business insights derived from the Marketing analytics dashboard.

## KPI Reference

Values below reflect the overall totals shown on the dashboard (default filter state: all years, all months, all products). Formula logic represents the expected DAX pattern based on the data model — **`TODO`: paste in the exact DAX from Power BI Desktop's Measure pane to confirm/replace the "Expected logic" column below.**

| Measure | Value shown | Expected logic | Business meaning |
| --- | --- | --- | --- |
| **Views** | 9.079M | `SUM(Fact_EngagementData[Views])` | Total number of times marketing content or product pages were viewed. |
| **Clicks** | 1.785M | `SUM(Fact_EngagementData[Clicks])` | Total number of clicks on marketing content or links. |
| **Likes** | 414.12K | `SUM(Fact_EngagementData[Likes])` | Total number of social media and content likes. |
| **Conversion Rate** | 9.57% | `DIVIDE([Total Purchases], [Total Views])` | The percentage of initial views that successfully resulted in a completed purchase. |
| **Avg Rating** | 3.69 | `AVERAGE(Fact_CustomerReviews[Rating])` | The average customer satisfaction score (out of 5 stars) across all submitted reviews. |

## Page-Level KPI Cards

| Page | KPI Cards Shown |
| --- | --- |
| **Overview** | Conversion Rate · Views · Clicks · Likes · Avg Rating |
| **Social Media Details** | Views · Clicks · Likes |
| **Conversion Details** | Conversion Rate |
| **Customer Review Details** | Avg Rating |

---

## Business Insights (Observed from Dashboard)

### 1. High top-of-funnel reach, but steep drop-off to clicks

The brand generates massive visibility with **9.079M Views**, but only **1.785M Clicks** and **414.12K Likes**. The funnel visual confirms a severe drop-off immediately after the "View" stage.

> **Next step:** Investigate content quality and call-to-action (CTA) placement. High views with low clicks suggest that while reach is broad, the content may not be compelling enough to drive users to the next step.

### 2. Conversion rates are highly seasonal

The conversion rate peaks sharply in **January (17.31%)** and experiences another smaller spike in **September (12.20%)**, but dips as low as 6.15% in October.

> **Next step:** Analyze the specific campaigns, discounts, or product launches that occurred in January and September to replicate their success during lower-performing months.

### 3. Product performance is uneven across the funnel

The **Hockey Stick** (15.46%) and **Ski Boots** (14.61%) are top performers in converting views to purchases. Conversely, **Swim Goggles** severely lag at a 5.62% conversion rate.

> **Next step:** Compare the customer journey duration for low-converting products versus high-converting ones to see if users are spending too much time searching for information on poorly performing product pages.

### 4. Customer satisfaction skews positive, but leaves room for improvement

The overall average rating is **3.69**, with the majority of reviews falling into the 4-star (431) and 5-star (409) buckets. However, there is a noticeable volume of 2-star and 3-star reviews pulling the average down.

> **Next step:** Drill through the "Review Details" table specifically filtering for 1-star and 2-star reviews on products like **Golf Clubs** and **Yoga Mat** (both at a 3.5 average) to identify recurring complaints (e.g., "Not worth the money", "Disappointed with the performance").

### 5. Engagement steadily declines throughout the year

The "Views, Clicks and Likes by Month" chart shows a clear downward trajectory from January to December across all three metrics, with Views dropping from 980K in January to 510K in December.

> **Next step:** Review the marketing budget allocation and campaign scheduling. If ad spend was front-loaded early in the year, this explains the drop; if spend was consistent, it indicates severe ad fatigue.
