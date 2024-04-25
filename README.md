# CLV-Cohort-Analysis
## The aim of the project
To perform CLV 12 weeks cohort analysis with a given data set from "turing_data_analytics" database.
## Tools used
Big Query, Google Spreadsheets.
## Data Processing
SQL code snippet to calculate cohorts.
``` sql
-- Data is taken from Turing Data Analytics database, raw_events table.
-- Here, I perform CLV (Customer Lifetime Value) cohort analyses.
-- The start of the week - Sunday.

WITH
  start_week AS (
  SELECT
    *,
    DATE_TRUNC(PARSE_DATE('%Y%m%d', event_date), WEEK) AS week,
    DATE_TRUNC(PARSE_DATE('%Y%m%d', MIN(event_date) OVER(PARTITION BY user_pseudo_id)), WEEK) AS first_visit_week
  FROM
    tc-da-1.turing_data_analytics.raw_events),

  revenue_0 AS (
  SELECT
    first_visit_week,
    COUNT(DISTINCT user_pseudo_id) AS new_customers,
    SUM(purchase_revenue_in_usd) AS rev_0
  FROM
    start_week
  WHERE
    week = first_visit_week
  GROUP BY
    first_visit_week),
  
  revenue_12 AS (
  SELECT
    first_visit_week,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 1 WEEK) THEN purchase_revenue_in_usd END) AS rev_1,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 2 WEEK) THEN purchase_revenue_in_usd END) AS rev_2,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 3 WEEK) THEN purchase_revenue_in_usd END) AS rev_3,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 4 WEEK) THEN purchase_revenue_in_usd END) AS rev_4,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 5 WEEK) THEN purchase_revenue_in_usd END) AS rev_5,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 6 WEEK) THEN purchase_revenue_in_usd END) AS rev_6,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 7 WEEK) THEN purchase_revenue_in_usd END) AS rev_7,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 8 WEEK) THEN purchase_revenue_in_usd END) AS rev_8,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 9 WEEK) THEN purchase_revenue_in_usd END) AS rev_9,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 10 WEEK) THEN purchase_revenue_in_usd END) AS rev_10,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 11 WEEK) THEN purchase_revenue_in_usd END) AS rev_11,
    SUM (CASE WHEN week = DATE_ADD(first_visit_week, INTERVAL 12 WEEK) THEN purchase_revenue_in_usd END) AS rev_12
  FROM
    start_week
  GROUP BY
    first_visit_week)

SELECT
  r0.first_visit_week AS week,
  r0.rev_0 / r0.new_customers AS week_0,
  r12.rev_1 / r0.new_customers AS week_1,
  r12.rev_2 / r0.new_customers AS week_2,
  r12.rev_3 / r0.new_customers AS week_3,
  r12.rev_4 / r0.new_customers AS week_4,
  r12.rev_5 / r0.new_customers AS week_5,
  r12.rev_6 / r0.new_customers AS week_6,
  r12.rev_7 / r0.new_customers AS week_7,
  r12.rev_8 / r0.new_customers AS week_8,
  r12.rev_9 / r0.new_customers AS week_9,
  r12.rev_10 / r0.new_customers AS week_10,
  r12.rev_11 / r0.new_customers AS week_11,
  r12.rev_12 / r0.new_customers AS week_12,
FROM
  revenue_0 AS r0
LEFT JOIN
  revenue_12 AS r12
USING
  (first_visit_week)
WHERE
  r0.first_visit_week <= '2021-01-24'
ORDER BY
  week;
```
