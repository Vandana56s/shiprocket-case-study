# Architecture Design: Daily Automated Pipeline & Self-Serve Agent

+---------------------------------------------------------------------------------+

| DATA SOURCES |

| GA4 (BigQuery Export) | Salesforce (Orders) | Ad Platforms (Spend APIs)

+---------------------------------------+-----------------------------------------+

| Daily Ingestion (Airbyte / Fivetran)

v
+---------------------------------+

| Raw S3 Data Lake (Bronze) |

+----------------+----------------+

| dbt Transformations + Great Expectations

v
+---------------------------------+

| Snowflake/BigQuery (Gold Marts) |

+----------------+----------------+|

| ---------------------------------------
|

v---------------------------------------- v

+-----------------------+ +---------------------------+

| Looker Studio / BI |--------------------- | Text-to-SQL Slack Bot |

| Executive Dashboard |------------------ | Semantic Layer Router |

+-----------------------+ +------------------------

### A. Automated Pipeline (AWS / GCP)

- **Storage & Warehouse:** Extract raw daily data via Airbyte or Cloud Functions into **AWS S3 / GCS Bronze**. Transform and model data into **BigQuery / Snowflake** star schemas (`fct_orders`, `dim_customers`, `mart_marketing_roi`) using **dbt**.
- **Scheduling:** Orchestrated via **Apache Airflow (Astronomer)** or **GCP Cloud Composer** at 02:00 UTC daily.
- **Automated Data Quality:** dbt tests and Great Expectations run post-ingestion:
  - Filter/quarantine orders with `order_value <= 0` or invalid order statuses.
  - Impute missing channel values to `'Unknown'` (preventing the silent exclusion of ₹88,070 in revenue observed in Part 1).
  - Validate uniqueness of `user_id` and `order_id`.
- **Security & Failure Alerts:** Ad platform API tokens and DB credentials reside in **AWS Secrets Manager / GCP Secret Manager** accessed via IAM roles (no hardcoded credentials). Pipeline failures or schema drift trigger instant webhooks to Slack and PagerDuty.

### B. Self-Serve Conversational Agent (Slack / Streamlit)

- **Scope & Tables:** A Slack bot queries a consolidated semantic mart (`mart_marketing_roi`) containing pre-aggregated metrics (Spend, New Customers, CAC, Delivered Revenue, LTV).
- **Step-by-Step Flow:**
  1. CMO asks: _"What was Meta's CAC last month?"_
  2. The LLM identifies the metric (`CAC`), channel (`Meta Ads`), and time range (`last month`), referencing a locked semantic YAML schema.
  3. The service generates deterministic, read-only SQL:
     ```sql
     SELECT channel, ROUND(SUM(spend_inr) / COUNT(DISTINCT new_customer_id), 0) AS cac
     FROM mart_marketing_roi
     WHERE channel = 'Meta Ads' AND month = DATE_TRUNC('month', CURRENT_DATE - INTERVAL '1 month')
     GROUP BY 1;
     ```
  4. The engine executes the query directly in the data warehouse and posts the verified number with a drill-down link back to Slack.
- **Preventing Hallucinations:** The LLM **never calculates or extrapolates numbers**. It acts purely as a semantic-to-SQL parser against allowlisted schema tables; all math is computed deterministically in the warehouse.
- **Testing Protocol:** Validated against a benchmark test suite of 50 canonical SQL query-answer pairs across date filters, channel synonyms, and metric definitions. Requires 100% test pass-rate before deployment.
