# Customer Data Pipeline — Reliability & Validation Report

**Project:** mini-databricks-bronze-pipeline  
**Platform:** Databricks Free Edition  
**Workflow:** customer-data-pipeline-prod-poc  
**Test Date:** October 22, 2026  
**Status:** Initial reliability and repeatability tests passed

---

## 1. Project Overview

This project implements a customer data pipeline using the Medallion Architecture.

The workflow consists of four sequential tasks:

1. `bronze_ingestion` — Ingests customer CSV data into a Bronze Delta table.
2. `silver_cleaning` — Cleans records, handles missing values, and removes duplicate customers.
3. `gold_analytics` — Aggregates customer counts by city.
4. `data_quality` — Executes validation and reconciliation checks.

**Pipeline execution order:**

Bronze → Silver → Gold → Data Quality

**Databricks job ID:** `318976192198802`

## 2. Reliability Configuration

### 2.1 Retry Policy

The Bronze ingestion task has the following retry configuration:

| Setting | Configuration |
|---|---|
| Retry interval | 1 minute |
| User-configured retries | 1 |
| Retry on timeout | Enabled |
| Serverless auto-optimization | Enabled |

**Important:** Databricks serverless auto-optimization may introduce additional retry attempts beyond the manually configured retry limit.

### 2.2 Failure Notifications

Job-level email notification is configured for workflow failures.

| Notification | Configuration |
|---|---|
| Job failure | Enabled |
| Job success | Disabled |
| Job start | Disabled |
| Duplicate Bronze task-level email alert | Removed |

**Verification:** Notification settings were confirmed in the Databricks UI.

**Limitation:** Actual failure email delivery has not yet been tested.

### 2.3 Timeout Configuration

A 10-minute task timeout was proposed but not configured because the timeout field was not located in the current Databricks Free Edition task UI.

**Status:** Pending configuration and verification.

## 3. Successful Workflow Execution

A complete pipeline execution was observed on October 22, 2026.

| Task | Result | Duration |
|---|---|---|
| Bronze ingestion | Succeeded | 52 seconds |
| Silver cleaning | Succeeded | 21 seconds |
| Gold analytics | Succeeded | 17 seconds |
| Data quality | Succeeded | 17 seconds |

**Overall workflow status:** Succeeded  
**Overall duration:** 1 minute 51 seconds  
**Execution type:** Manual

The task dependency graph showed successful execution of all four stages.

## 4. Count-Level Idempotency Test

**Objective:** Confirm that rerunning the pipeline with unchanged source data does not increase record counts.

| Layer | Before Rerun | After Rerun | Result |
|---|---:|---:|---|
| Bronze | 5 | 5 | PASS |
| Silver | 4 | 4 | PASS |
| Gold customer total | 4 | 4 | PASS |

**Result:** Count-level repeatability verified for the tested dataset.

The Gold count represents the sum of `total_customers`, not the number of city groups.

## 5. Duplicate Customer Validation

**Objective:** Confirm that each customer ID appears at most once in the Silver table.

```sql
SELECT
    customer_id,
    COUNT(*) AS duplicate_count
FROM workspace.customer_data_pipeline.silver_customers
GROUP BY customer_id
HAVING COUNT(*) > 1;
```

**Actual result:** 0 rows.

**Status:** PASS

## 6. Silver Data Fingerprint Validation

**Objective:** Compare the complete Silver dataset before and after a controlled rerun.

```sql
SELECT
    COUNT(*) AS total_records,
    sha2(
        concat_ws(
            '||',
            sort_array(
                collect_list(
                    to_json(struct(*))
                )
            )
        ),
        256
    ) AS data_fingerprint
FROM workspace.customer_data_pipeline.silver_customers;
```

**Before rerun:**

- Total records: 4
- SHA-256 fingerprint: `4a4e76b90ee038677a392225ff9b4d0a061f9e58e2b7d47e7c1e3ced55a462c3`

**After rerun:**

- Total records: 4
- SHA-256 fingerprint: `4a4e76b90ee038677a392225ff9b4d0a061f9e58e2b7d47e7c1e3ced55a462c3`

**Result:** PASS — Fingerprints matched exactly.

This provides evidence that the Silver dataset remained unchanged across the tested rerun, assuming the same table schema and serialization behavior.

## 7. Known Limitations

The current POC is not yet a fully production-ready system.

Remaining validation and engineering work includes:

- Controlled failure and recovery testing.
- Actual failure notification delivery verification.
- Task timeout configuration.
- Changed-input and incremental ingestion tests.
- Schema evolution and malformed-record handling.
- Business-key and null-value validation for additional scenarios.
- Git-managed job configuration and deployment procedures.
- Security, access control, and operational runbook improvements.

## 8. Conclusion

The pipeline successfully completed repeatability checks using unchanged customer source data.

Verified outcomes include:

- Successful four-task orchestration.
- Stable Bronze, Silver, and Gold counts.
- No duplicate customer IDs in Silver.
- Matching Silver data fingerprints across a rerun.
- Saved retry and job-level failure notification configurations.

**Overall assessment:** Initial reliability testing passed for the sample dataset. Further failure, recovery, and operational testing is required before production deployment.
