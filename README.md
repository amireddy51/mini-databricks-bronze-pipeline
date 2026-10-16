# Mini Databricks Bronze Pipeline

A small hands-on data engineering project using
Databricks Free Edition, PySpark, and Delta Lake.

## Objective
Build a raw CSV to Bronze Delta pipeline and
implement basic data quality checks.

## Technology
- Databricks Free Edition
- PySpark
- Delta Lake
- Git and GitHub

## Planned Pipeline
Raw CSV -> Bronze Delta -> Data Quality Checks

## Project Status
Phase 1: Project setup and sample data.


## Phase 2: Bronze Layer Ingestion

### Technology
- Databricks Free Edition
- PySpark
- Delta Lake
- Unity Catalog
- Git and GitHub

### Data Pipeline
1. Upload `customers.csv` to a Unity Catalog volume.
2. Read the CSV using PySpark.
3. Store the raw records in a Bronze Delta table.
4. Validate the record count.

### Databricks Objects
- Catalog: `workspace`
- Schema: `customer_data_pipeline`
- Volume: `landing`
- Bronze table: `bronze_customers`

### Validation Results
- Expected records: 5
- Actual records: 5
- Status: PASSED

### Notebook
`notebooks/01_bronze_customer_ingestion.ipynb`

### Next Phase
Silver layer: data quality checks, null handling, and duplicate removal.
