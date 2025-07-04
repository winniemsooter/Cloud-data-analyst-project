# Cloud-data-analyst-project
I built and cleaned a dataset and a visualization that helps a loan company.
# Cloud Data Analyst Capstone Project

# Project Overview
This capstone project showcases my end-to-end data analytics skills using cloud-based tools and SQL. The project involved analyzing loan data to deliver insights for stakeholders, create useful dashboards, and flag financial thresholds to ensure responsible lending.

---

# Objective
To build a data visualization that:
- Displays the total outstanding loan balance.
- Alerts stakeholders when the outstanding loan amount exceeds a $3,000,000,000 threshold.

---

# Tools & Technologies
- *Google BigQuery*: For querying datasets.
- *SQL*: For data extraction and transformation.
- *Looker Studio (or Data Studio)*: For building visual dashboards.
- *Cloud Training Demo Dataset*: Sourced from `cloud-training-demos.fintech.loan`

---

# Problem Statement
Trevor, a stakeholder, requested a dashboard visualization of key team members' outstanding loan balance. They also want to ensure the total balance never exceeds their responsible lending limit: **$3,000,000,000**.

---

# Data Exploration
To start, I queried the dataset to understand the structure and to calculate the outstanding loans:

```sql
LOAD DATA OVERWRITE fintech.state_region
(
state string,
subregion string,
region string
)
FROM FILES (
format = 'CSV',
uris = ['gs://sureskills-lab-dev/future-workforce/da-capstone/temp_35_us/state_region_mapping/state_region_*.csv']);

SELECT
lo.loan_id,
lo.loan_amount,
sr.region
FROM fintech.loan lo
INNER JOIN fintech.state_region sr
ON lo.state = sr.state;


CREATE OR REPLACE TABLE fintech.loan_with_region AS
SELECT
lo.loan_id,
lo.loan_amount,
sr.region
FROM fintech.loan lo
INNER JOIN fintech.state_region sr
ON lo.state = sr.state;

SELECT loan_id,application.purpose
FROM fintech.loan;

CREATE TABLE fintech.loan_purposes AS
SELECT DISTINCT application.purpose AS purpose
FROM fintech.loan;

SELECT issue_year, loan_amount
FROM fintech.loan
ORDER BY issue_year, issue_date;

SELECT issue_year, sum(loan_amount) AS total_amount
FROM fintech.loan
GROUP BY issue_year;


CREATE TABLE fintech.loan_count_by_year AS
SELECT
  issue_year,
  COUNT(loan_id) AS loan_count
FROM fintech.loan
GROUP BY issue_year
ORDER BY issue_year;


SELECT 
  COALESCE(SUM(CASE 
    WHEN (loan.loan_status <> 'Fully Paid' OR loan.loan_status IS NULL) 
    THEN loan.loan_amount 
    ELSE NULL 
  END), 0) AS loan_outstanding_loans_amount 
FROM `cloud-training-demos.fintech.loan` AS loan 
LIMIT 500;
```

This query calculates the sum of all loans that are **not fully paid**, treating NULLs as unpaid too.

---

# Visualization
*Dashboard Setup in Looker Studio:*
- Chart Type: **Scorecard or Gauge**
- Metric: Total Outstanding Loan Amount
- Added a **Threshold Line** at `$3,000,000,000`
- Color Indicators: Green (below threshold), Red (above threshold)

---

# Insights
- Visualization allows for real-time monitoring of lending limits.
- Easily highlights when the lending surpasses safe thresholds.

---

# Conclusion
This project demonstrates the power of cloud-based data querying combined with visualization tools to monitor financial health. It emphasizes:
- Real-time alerting
- Strategic decision-making support
- Clean, interactive reporting

---

# Final Steps
- I automated the dashboard to refresh intervals.
- I included team-level drilldowns.
- Added predictive analytics for upcoming loans.

---
