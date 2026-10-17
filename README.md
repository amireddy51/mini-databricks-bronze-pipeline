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


## Phase 3: Silver Layer

### Notebook
`notebooks/02_silver_customer_cleaning`

### Source Table
`workspace.customer_data_pipeline.bronze_customers`

### Target Table
`workspace.customer_data_pipeline.silver_customers`

### Transformations
1. Read the Bronze Delta table.
2. Check missing values and duplicate customer IDs.
3. Remove duplicate customer IDs.
4. Replace missing email and city values with "Unknown".
5. Validate cleaned records.
6. Write the Silver Delta table.
7. Read back and validate the saved table.

### Validation Results
- Bronze records: 5
- Silver records: 4
- Duplicate customer IDs: 0
- Null email values: 0
- Null city values: 0
- Silver Delta table validation: PASSED

### Notes
- "Unknown" is a placeholder used for this learning POC.
- The Silver table uses overwrite mode.
- Production pipelines should use business-approved
  missing-value and deduplication rules.


## Phase 4 - Gold Customer Analytics

### Business Requirement
Calculate the number of unique customers in each city
for business reporting and analytics.

### Source Delta Table
workspace.customer_data_pipeline.silver_customers

### Target Delta Table
workspace.customer_data_pipeline.gold_customers_by_city

### Gold Transformation
1. Read cleaned customer data from Silver.
2. Group customers by city.
3. Count distinct customer IDs.
4. Validate customer totals.
5. Save aggregated results as a Delta table.
6. Read the saved table and verify results.

### Validation Results
- Silver unique customers: 4
- Gold total customers: 4
- Gold city groups: 4
- Data quality validation: PASSED
- Saved Delta table validation: PASSED

### Gold Analytics Results

| City | Total Customers |
|------|-----------------|
| Austin | 1 |
| Dallas | 1 |
| Houston | 1 |
| Unknown | 1 |

### Notebook
notebooks/03_gold_customer_analytics.ipynb

### Technology
- Databricks Free Edition
- PySpark
- Delta Lake
- Unity Catalog
- Git and GitHub

  
