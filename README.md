**Serverless Lakehouse Pipeline on AWS (Bronze → Silver → Gold with Iceberg)**
--

This project demonstrates a cost-efficient, fully serverless data pipeline built on AWS using S3, Lambda, Glue, and Apache Iceberg.
It follows the Medallion Architecture (Raw → Bronze → Silver → Gold) and processes a public dataset (NYC Taxi Trips) into analytics-ready Iceberg tables optimized for BI and downstream consumption.

This repo showcases real-world engineering patterns such as event-driven ETL, incremental MERGE operations, schema evolution, partitioned data lakes, and Iceberg-based Lakehouse modeling.


**🔍 Problem Solved**
--

Most data projects don't need expensive tech tools or warehouses.
This pipeline shows how to build a scalable, production-style Lakehouse using:

 - Serverless compute
   
 - Low-cost storage
   
 - Pay-per-query analytics
   
 - Open table formats (Iceberg)

The result is a true modern data stack without vendor lock-in or unnecessary cost.

**🏗️ Medallion Layers**
--
**RAW Layer**

Raw files stored exactly as ingested from the source.

Folder:

s3://<your-bucket>/raw/


Triggered automatically by S3 events.

**BRONZE Layer**
--

**Purpose:**

 - Clean minimal transformations
   
  - Normalize column types
   
  - Add ingestion metadata
 
  - Partition for downstream use

**Output:**
s3://<your-bucket>/bronze/nyc_taxi/job_run_id=<id>/

Stored as **Parquet**.

**SILVER Layer (Iceberg)**
--

**Purpose:**

- Business-level cleaning & enrichment

- Deduplication using unique_trip_id

- Transformation of payment types

- Iceberg table with schema evolution

**Table:**
glue_catalog.silver_iceberg.nyc_taxi

**Iceberg Advantages**:

 - MERGE operations
   
 - Time Travel
   
 - Hidden partitioning
   
 - Atomic commits
   
 - Schema evolution

**GOLD Layer (Iceberg)**
---

**Purpose:**

Produce analytics-ready data models.

This pipeline generates:

daily_taxi_metrics fact table

**Metrics:**

 - trip_count
  - total_revenue
 - total_fare
  - total_tip_amount
  - 
Grain: daily
Partitioned by year, month, day

**Gold** also uses Iceberg MERGE INTO for incremental updates (late-arriving data, reprocessing, etc.)

Table:

glue_catalog.gold_iceberg.daily_taxi_metrics

**🚀 How the Pipeline Works End-to-End**
--

**Upload a raw file to S3 under raw/.**

S3 triggers the Lambda function.

Lambda calls the Bronze Glue job with:

--input_uri

--run_id

**Bronze job** cleans the data and writes parquet to:

**s3://<bucket>/bronze/nyc_taxi/job_run_id=<run_id>/**


Then writes a metadata JSON linking the **Silver input path**.

**Silver job** reads Bronze output and writes to the Silver Iceberg table using MERGE.

**Gold job** reads Silver, aggregates metrics, and MERGEs into the Gold Iceberg table.

**Data becomes available in Amazon Athena for BI.**

**🧪 Querying in Athena**
--

**Example queries:**

**Total daily trips:**
SELECT pickup_date, trip_count
FROM gold_iceberg.daily_taxi_metrics
ORDER BY pickup_date;

**Revenue trend:**
SELECT pickup_date, total_revenue
FROM gold_iceberg.daily_taxi_metrics
ORDER BY pickup_date;

**Tip percentage distribution:**
SELECT pickup_date, avg_tip_pct
FROM gold_iceberg.daily_taxi_metrics;

**📌 Tech Stack**
--
**Component	Purpose**

 - AWS **S3** Raw/Bronze data lake storage
   
 - AWS **Lambda**	Event-driven orchestration
   
 - AWS **Glue (PySpark)**	ETL for Bronze, Silver, Gold
   
 - **Apache Iceberg**	Lakehouse table format
   
 - AWS **Glue Catalog**	Iceberg metadata store
   
 - Amazon **Athena**	Serverless SQL queries
   
 - **Python** /  **PySpark**	Transformation logic
   

**🏁 Why This Project Matters**
--

**This pipeline demonstrates:**

✔ Modern Lakehouse design

✔ Event-driven serverless ingestion

✔ Incremental processing with MERGE

✔ Medallion-based architecture

✔ Iceberg table management

✔ Production ETL patterns

✔ Cost-efficient data engineering

