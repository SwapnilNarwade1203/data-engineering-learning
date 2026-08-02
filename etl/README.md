# Comprehensive ETL & Data Engineering Master Notes

> [!NOTE]
> **Source material**: Compiled from your hands-on learning session on **ETL Processes, Ingestion Frameworks, Python DB Extraction, PySpark, Snowpark, and Production Pipeline Architecture**.

---

## Table of Contents
1. [Core Concepts & Modern ETL vs ELT Architecture](#1-core-concepts--modern-etl-vs-elt-architecture)
2. [End-to-End ETL Process Steps](#2-end-to-end-etl-process-steps)
3. [Multi-Engine Extraction Guide (Python, PySpark, Snowpark)](#3-multi-engine-extraction-guide-python-pyspark-snowpark)
4. [Production-Grade Python Database Extraction](#4-production-grade-python-database-extraction)
5. [Production ETL Project Skeleton & Software Engineering](#5-production-etl-project-skeleton--software-engineering)
6. [Senior / FAANG Data Engineering Interview Questions & Answers](#6-senior--faang-data-engineering-interview-questions--answers)

---

## 1. Core Concepts & Modern ETL vs ELT Architecture

### 1.1 Fundamental Definitions
* **OLTP (Online Transaction Processing)**: Databases optimized for fast, transactional row-level read/write operations (e.g., PostgreSQL, MySQL, Oracle). Characterized by high concurrency, ACID compliance, normalized schemas (3NF), and low latency.
* **OLAP (Online Analytical Processing)**: Data warehouses designed for analytical queries spanning millions/billions of records (e.g., Snowflake, Google BigQuery, AWS Redshift). Characterized by columnar storage, heavy aggregation, data compression, and denormalized schemas (Star / Snowflake Schema).
* **Data Warehouse (DWH)**: Centralized analytical store holding curated, cleansed, and structured data ready for BI and analytics.
* **Data Lake**: Storage repository (e.g., AWS S3, Azure ADLS, GCP GCS) holding raw, semi-structured (JSON, Parquet), and unstructured data in native format.

### 1.2 Paradigm Shift: ETL vs ELT

```mermaid
flowchart TD
    subgraph ETL ["Traditional ETL Architecture"]
        S1[Source OLTP / API / Files] --> E1[Extract]
        E1 --> T1[Dedicated Transformation Engine / Spark]
        T1 --> L1[Load Cleaned Data]
        L1 --> W1[Data Warehouse / Mart]
    end

    subgraph ELT ["Modern ELT Architecture"]
        S2[Source OLTP / API / Files] --> E2[Extract & Load Raw Data]
        E2 --> L2[Raw Data Lake / Snowflake Staging]
        L2 --> T2["Warehouse SQL / dbt Transformation"]
        T2 --> W2[Modeled Analytics Tables]
    end
```

| Dimension | Traditional ETL | Modern ELT |
| :--- | :--- | :--- |
| **Transform Location** | Pre-load (on ETL Server / Spark cluster) | Post-load (inside Cloud Data Warehouse via SQL/dbt) |
| **Raw Data Availability** | Lost or kept in separate archive | Persisted directly in Data Lake / Staging layer |
| **Scalability** | Bottlenecked by ETL server compute | Scales elastically with cloud warehouse (e.g., Snowflake virtual warehouses) |
| **Security / Compliance** | Excellent for PII masking before data lands | Requires row/column-level security inside the warehouse |
| **Flexibility** | High initial engineering overhead per pipeline | High query-time flexibility; raw data can be re-transformed anytime |

---

## 2. End-to-End ETL Process Steps

A production pipeline consists of sequential, tightly controlled steps:

```mermaid
graph LR
    A[1. Extract] --> B[2. Stage & Validate]
    B --> C[3. Transform & Enrich]
    C --> D[4. Quality Check]
    D --> E[5. Load Target]
    E --> F[6. Audit & Reconcile]
```

### Step 1: Extraction
* **Full Extraction**: Ingesting the entire dataset from source (used for small lookup tables or snapshot tables).
* **Incremental Extraction (CDC / High-Watermark)**: Fetching only rows changed or created since the last successful extraction run.

### Step 2: Staging Area
* **Purpose**: Landing area isolating production databases from analytical processing.
* Raw data is saved as-is (e.g., Parquet or staging tables) without mutating source fields.

### Step 3: Data Quality & Validation
* **Schema Validation**: Checking column names, data types, and non-null constraints.
* **Business Rule Validation**: Detecting negative prices, invalid email formats, or future timestamps.
* **Quarantine Pattern**: Invalid records are diverted to a quarantine table for alerting rather than blowing up the pipeline.

### Step 4: Transformation
* **Cleaning & Standardizing**: Trimming strings, standardizing date formats (ISO-8601 UTC), handling nulls.
* **Enrichment & Lookup**: Joining dimension tables or calling geo-IP services to enrich data.
* **Data Modeling**: Converting flat records into Star Schema (Facts and Slowly Changing Dimensions - SCD Type 1/2).

### Step 5: Loading
* **Append / Full Overwrite**: Appending new partition data or truncating small dimension tables.
* **Upsert (MERGE)**: Updating existing records when primary key matches and inserting new records when it does not.

### Step 6: Audit Logging & Reconciliation
* Logging pipeline metrics: `batch_id`, `source_name`, `records_read`, `records_inserted`, `records_quarantined`, `execution_time_ms`, `status`.
* Row-count & sum checksum reconciliation between source and target.

---

## 3. Multi-Engine Extraction Guide (Python, PySpark, Snowpark)

### 3.1 Extraction Matrix

```
                      ┌───────────────────────────────────────┐
                      │    Data Extraction Source Systems    │
                      └──────────────────┬────────────────────┘
                                         │
     ┌───────────────────────────────────┼───────────────────────────────────┐
     ▼                                   ▼                                   ▼
┌─────────┐                         ┌─────────┐                         ┌─────────┐
│ Database│                         │ REST API│                         │Cloud/File│
└────┬────┘                         └────┬────┘                         └────┬────┘
     │                                   │                                   │
     ├── Python (SQLAlchemy / DB-API)     ├── Python (Requests / aiohttp)     ├── Python (Boto3 / Pandas)
     ├── PySpark (spark.read.jdbc)       ├── PySpark (requests + parallelize)├── PySpark (spark.read.parquet)
     └── Snowpark (session.table)        └── Snowpark (UDTF / External Access)└── Snowpark (session.read)
```

---

### 3.2 Python Implementation (Database, API, Flat File, Cloud S3)

```python
import pandas as pd
import requests
import boto3
from sqlalchemy import create_engine

# 1. Database Extraction (SQLAlchemy + Chunking)
def extract_from_db(connection_str: str, query: str, chunksize: int = 10000):
    engine = create_engine(connection_str)
    with engine.connect() as conn:
        for chunk in pd.read_sql(query, conn, chunksize=chunksize):
            yield chunk

# 2. REST API Extraction (Paginated REST API)
def extract_from_api(base_url: str, headers: dict):
    page = 1
    all_data = []
    while True:
        response = requests.get(f"{base_url}?page={page}&limit=100", headers=headers)
        response.raise_for_status()
        data = response.json()
        if not data.get("items"):
            break
        all_data.extend(data["items"])
        page += 1
    return pd.DataFrame(all_data)

# 3. Cloud S3 Parquet File Extraction (Boto3 + Pandas/PyArrow)
def extract_from_s3(bucket: str, key: str):
    s3_url = f"s3://{bucket}/{key}"
    # Ingest directly using PyArrow backend
    df = pd.read_parquet(s3_url, storage_options={"anon": False})
    return df
```

---

### 3.3 PySpark Implementation (Distributed Lake Extraction)

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("ProductionSparkExtraction") \
    .config("spark.jars", "/drivers/postgresql-42.6.0.jar") \
    .getOrCreate()

# 1. Database Extraction via JDBC (Partitioned for Parallelism)
def extract_db_pyspark(jdbc_url: str, table: str, properties: dict):
    df_db = spark.read.jdbc(
        url=jdbc_url,
        table=table,
        column="id",
        lowerBound=1,
        upperBound=1000000,
        numPartitions=10,
        properties=properties
    )
    return df_db

# 2. Cloud Data Lake Parquet Extraction (AWS S3)
def extract_s3_parquet(s3_path: str):
    df_s3 = spark.read \
        .option("header", "true") \
        .option("recursiveFileLookup", "true") \
        .parquet(s3_path)
    return df_s3
```

---

### 3.4 Snowpark Python Implementation (In-Warehouse Processing)

```python
from snowflake.snowpark import Session
from snowflake.snowpark.functions import col

# Connection parameters dictionary
connection_parameters = {
    "account": "<account_identifier>",
    "user": "<username>",
    "password": "<password>",
    "role": "ACCOUNTADMIN",
    "warehouse": "COMPUTE_WH",
    "database": "DEMO_DB",
    "schema": "PUBLIC"
}

session = Session.builder.configs(connection_parameters).create()

# 1. Direct Table Ingestion into DataFrame
def extract_snowpark_table(table_name: str):
    df_table = session.table(table_name).filter(col("IS_ACTIVE") == True)
    return df_table

# 2. Extract from Internal/External Stage (S3 linked stage)
def extract_snowpark_stage(stage_path: str):
    df_stage = session.read.option("INFER_SCHEMA", True).parquet(stage_path)
    return df_stage
```

---

## 4. Production-Grade Python Database Extraction

### 4.1 Connection Pooling & Resource Lifecycle
Using bare `psycopg2.connect()` or `sqlite3.connect()` per query causes connection exhaustion under load. Production systems utilize **Connection Pools**:

```python
from sqlalchemy import create_engine

# Production Engine with Connection Pool limits
engine = create_engine(
    "postgresql+psycopg2://user:password@db-host:5432/analytics_db",
    pool_size=10,          # Persistent connections maintained in pool
    max_overflow=20,       # Temporary connections permitted during spikes
    pool_recycle=3600,     # Recycle connections hourly to prevent stale sockets
    pool_pre_ping=True     # Test connection validity before issuing query
)
```

---

### 4.2 High-Watermark Incremental Extraction Logic

Incremental extraction relies on tracking state between pipeline runs.

```mermaid
sequenceDiagram
    participant State as Watermark Store (watermark.json)
    participant Pipeline as Python ETL Runner
    participant DB as Source OLTP DB
    participant Target as Data Warehouse Staging

    Pipeline->>State: 1. Read last_watermark (e.g. 2026-08-01 00:00:00)
    Pipeline->>DB: 2. Query: SELECT * FROM orders WHERE updated_at >= last_watermark
    DB-->>Pipeline: 3. Return batch of modified records
    Pipeline->>Target: 4. Write records to staging table
    Pipeline->>State: 5. Update watermark to MAX(updated_at) from batch
```

#### Production Python Incremental Extractor Implementation

```python
import json
from datetime import datetime
import pandas as pd
from sqlalchemy import create_engine, text

class WatermarkExtractor:
    def __init__(self, db_engine, state_filepath: str):
        self.engine = db_engine
        self.state_filepath = state_filepath

    def get_last_watermark(self, default_start: str = "1970-01-01 00:00:00") -> str:
        try:
            with open(self.state_filepath, "r") as f:
                state = json.load(f)
                return state.get("last_watermark", default_start)
        except FileNotFoundError:
            return default_start

    def save_watermark(self, new_watermark: str):
        with open(self.state_filepath, "w") as f:
            json.dump({"last_watermark": str(new_watermark)}, f, indent=2)

    def extract_incremental(self, table_name: str, watermark_col: str, batch_size: int = 10000):
        last_wm = self.get_last_watermark()
        print(f"[INFO] Extracting {table_name} starting from watermark: {last_wm}")
        
        query = text(f"""
            SELECT * FROM {table_name}
            WHERE {watermark_col} >= :last_wm
            ORDER BY {watermark_col} ASC
        """)

        max_wm = last_wm
        total_extracted = 0

        with self.engine.connect() as conn:
            # Stream results using server-side cursor execution
            result_proxy = conn.execution_options(yield_per=batch_size).execute(query, {"last_wm": last_wm})
            
            while True:
                chunk = result_proxy.fetchmany(batch_size)
                if not chunk:
                    break
                
                df_chunk = pd.DataFrame(chunk, columns=result_proxy.keys())
                total_extracted += len(df_chunk)
                
                # Track max timestamp encountered
                current_max = df_chunk[watermark_col].max()
                if current_max and str(current_max) > str(max_wm):
                    max_wm = current_max

                yield df_chunk

        # Update state file upon successful completion
        if total_extracted > 0:
            self.save_watermark(str(max_wm))
            print(f"[SUCCESS] Extraction complete. {total_extracted} records. New Watermark: {max_wm}")
        else:
            print("[INFO] No new records found.")
```

---

## 5. Production ETL Project Skeleton & Software Engineering

### 5.1 Directory Layout
```
production_etl_project/
├── config/
│   ├── config.yaml          # Environment settings (dev, staging, prod)
│   └── secrets.env          # Environment variables (never committed)
├── db/
│   ├── connection.py        # Connection pooling & engine initialization
│   └── state.py             # Persistent state/watermark manager
├── extractors/
│   ├── base.py              # Abstract Base Class for Extractors
│   ├── db_extractor.py      # Database extractor subclass
│   └── api_extractor.py     # API extractor subclass
├── utils/
│   ├── logger.py            # Structured JSON logger
│   └── decorators.py        # Retry with exponential backoff
├── logs/                    # Local runtime logs
└── main.py                  # CLI Entry point & orchestrator
```

### 5.2 Fault-Tolerant Retry Decorator (`utils/decorators.py`)

```python
import time
import functools
import logging

logger = logging.getLogger("ETL_Logger")

def retry_with_backoff(retries: int = 3, backoff_in_seconds: int = 2):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            x = 0
            while True:
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if x == retries:
                        logger.error(f"Function {func.__name__} failed after {retries} retries. Error: {e}")
                        raise e
                    sleep_time = (backoff_in_seconds ** x)
                    logger.warning(f"Error in {func.__name__}: {e}. Retrying in {sleep_time} seconds...")
                    time.sleep(sleep_time)
                    x += 1
        return wrapper
    return decorator
```

---

## 6. Senior / FAANG Data Engineering Interview Questions & Answers

> [!IMPORTANT]
> The following section presents interview questions derived from your practice session. **Answers reflect GPT's authoritative, production-grade responses**, outlining technical edge cases, architectural trade-offs, and exact terminology expected in senior technical evaluations.

---

### Question 1: ETL vs ELT Architecture & Trade-Offs

**Interviewer Question:**  
*"Can you explain the difference between ETL and ELT? Why would an enterprise choose a traditional ETL workflow over ELT in today's modern cloud data ecosystem?"*

#### Comprehensive Production Answer:
* **Definitions**:
  * **ETL (Extract, Transform, Load)** cleans, standardizes, and joins data on an intermediate compute engine (e.g., PySpark, AWS Glue, custom Python) *before* writing the final structured results into the storage/warehouse layer.
  * **ELT (Extract, Load, Transform)** loads raw data directly into cloud storage and a cloud data warehouse (e.g., Snowflake, BigQuery) first, leveraging warehouse SQL/dbt for downstream transformation.
* **Why Choose ETL over ELT Today?**:
  1. **Strict Privacy & Regulatory Compliance (PII / GDPR / HIPAA)**: When sensitive data (credit cards, SSNs, medical records) must *never* land in raw form on cloud storage due to compliance rules. ETL scrubs, hashes, or masks PII prior to data landing anywhere in the warehouse.
  2. **Unstructured / Complex Non-SQL Transformations**: Transformations involving custom NLP, machine learning feature extraction, image processing, or complex binary decoding cannot easily be expressed in warehouse SQL. A distributed engine like PySpark (ETL) is required.
  3. **Data Warehouse Cost Control**: Cloud Data Warehouses charge based on compute credits (e.g., Snowflake virtual warehouses). Performing massive, CPU-intensive data cleansing or cross-dataset joins inside the data warehouse can lead to massive compute bills compared to running cheap, spot-instance Spark nodes.
  4. **Legacy Target Systems**: When the target analytical system is an on-premises RDBMS or legacy database that lacks elastic scaling.

---

### Question 2: Safely Extracting from OLTP Production Databases

**Interviewer Question:**  
*"Why is running `SELECT * FROM orders` directly against an active OLTP database considered a critical production failure? How do you engineer data extraction without impacting live transactional systems?"*

#### Comprehensive Production Answer:
* **The Problem**:
  * **Table & Row Locking**: Shared (`S`) or Exclusive (`X`) read locks taken during a full table scan can block concurrent `INSERT`, `UPDATE`, or `DELETE` operations from web users, causing application timeouts and outages.
  * **Resource Exhaustion**: Scanning millions of disk blocks consumes database buffer pool memory and saturates disk I/O & CPU, degrading application response times.
* **Engineering Solutions**:
  1. **Extract from Read Replicas / Standby Nodes**: Divert all ETL queries away from the primary database to read-only read replicas (using PostgreSQL streaming replication or MySQL read replicas).
  2. **Non-Blocking Transaction Isolation Levels**: Execute extraction sessions under `READ UNCOMMITTED` or PostgreSQL `SET TRANSACTION ISOLATION LEVEL SNAPSHOT` / `READ COMMITTED`. In SQL Server, use table hints like `WITH (NOLOCK)` (if dirty reads are acceptable).
  3. **Incremental High-Watermark Extraction**: Never run `SELECT *`. Extract only incremental changes using filtered indexed queries: `WHERE updated_at >= :last_watermark`.
  4. **Chunking / Pagination with Index Bounds**: Break large queries into bounded primary key chunks (e.g., `WHERE id BETWEEN 1 AND 50000`), allowing the DB scheduler to interleave transactional writes between chunks.
  5. **Log-Based Change Data Capture (CDC)**: Read database transaction logs (WAL in Postgres, binlog in MySQL) directly using tools like Debezium. This extracts changes asynchronously with **zero query load** on the database tables.

---

### Question 3: Incremental Watermarking Edge Cases & Out-of-Order Writes

**Interviewer Question:**  
*"You built an incremental pipeline using `WHERE updated_at > last_watermark`. What critical edge cases can cause data loss or duplicate ingestion, and how do you solve them?"*

#### Comprehensive Production Answer:
* **Edge Case 1: Duplicate Timestamps across Batch Boundaries**
  * *Scenario*: Multiple rows share the exact same `updated_at` timestamp (e.g., `2026-08-02 10:00:00.000`). If a batch stops mid-second and the watermark updates to `10:00:00.000`, using `>` skips remaining records stamped at that exact timestamp.
  * *Solution*: Use `>= last_watermark` combined with **downstream idempotency (MERGE / Upsert)** or use a composite tuple watermark `WHERE (updated_at, id) > (:last_updated_at, :last_id)`.
* **Edge Case 2: Long-Running Open Transactions (Late-Arriving Writes)**
  * *Scenario*: Transaction A starts at 09:58:00 and updates a record (`updated_at = 09:58:00`), but does NOT commit until 10:05:00. If your ETL pipeline runs at 10:01:00, it cannot see uncommitted Transaction A. Watermark updates to 10:01:00. When Transaction A commits at 10:05:00, its timestamp (09:58:00) is *less* than the current watermark (10:01:00), making the record **permanently invisible** to future incremental runs!
  * *Solution*: Implement a **Lookback / Overlap Window**:  
    Query `WHERE updated_at >= (:last_watermark - INTERVAL '15 MINUTES')`.  
    Because this re-extracts records from the past 15 minutes, target loading must be **idempotent** (using `MERGE INTO target USING staging ON target.id = staging.id`).

---

### Question 4: Distributed Compute vs Cloud Data Warehouse: PySpark vs Snowflake

**Interviewer Question:**  
*"When designing an enterprise data platform, how do you decide whether to perform data transformations in PySpark versus Snowflake?"*

#### Comprehensive Production Answer:

```mermaid
graph TD
    A[Data Ingestion Arrives] --> B{Is data unstructured/semi-structured or complex ML?}
    B -- Yes (Images, Logs, ML Feature Prep) --> C[Use PySpark on Data Lake]
    B -- No (Structured / Semi-structured JSON) --> D{Where does the data reside?}
    D -- Resides in S3/Data Lake needing heavy ETL --> E{Are Snowflake compute credits a concern?}
    E -- Yes (Very high volume transformation) --> C
    E -- No (Team prefers SQL / dbt) --> F[Use Snowflake ELT + dbt]
    D -- Data already landed in Snowflake --> F
```

#### Detailed Comparison Matrix:

| Criteria | PySpark (Distributed Compute) | Snowflake (Cloud Warehouse) |
| :--- | :--- | :--- |
| **Primary Architecture** | General-purpose distributed cluster (memory + CPU nodes) | Cloud Data Warehouse (decoupled storage & virtual warehouses) |
| **Data Types** | Structured, Semi-structured, Unstructured (audio, text, binary, images) | Structured & Semi-structured (JSON/Variant, XML, Parquet) |
| **Transformation Paradigm** | Procedural Python, DataFrames, RDDs, PySpark SQL | SQL-native declarative transformations, dbt models, Snowpark |
| **Operational Overhead** | Cluster management, memory tuning (`oom` debugging), partitioning | Zero infrastructure management, auto-scaling |
| **Cost Model** | Pay for raw compute nodes (e.g. AWS EMR / Databricks worker instances) | Pay per second for Snowflake credit consumption |
| **Best Fit Use Case** | Extracting raw data from lakes, ML feature stores, streaming (Kafka), complex procedural Python rules | Analytical modeling, Star schema joins, BI reporting layer, dbt SQL pipelines |

---

### Question 5: Handling Schema Drift & Pipeline Quality Control

**Interviewer Question:**  
*"How do you prevent a production pipeline from crashing when a source team adds, drops, or alters a database column without notifying you?"*

#### Comprehensive Production Answer:
1. **Schema Evolution in Storage Formats**: Use storage formats like **Parquet** or **Delta Lake** that support explicit schema evolution (`mergeSchema=true` in Spark).
2. **Dynamic Column Mapping (Not explicit SELECTs)**: Avoid hardcoding column lists in extraction scripts. Read source metadata dynamically via system catalogs (`information_schema.columns`) and compare against target schema.
3. **Quarantine / DLQ (Dead-Letter Queue)**: Capture failing rows or incompatible data types and route them to an isolation table while allowing valid rows to proceed.
4. **Contract Testing & Alerting**: Implement data contract validation (e.g., using **Great Expectations** or **dbt tests**) prior to production model execution, alerting engineers via Slack/PagerDuty if column types shift unexpectedly.

---

## 7. Summary & Next Steps

* **Key Takeaway**: A senior Data Engineer goes beyond simple scripts. You must design for **resource safety (connection pools, read replicas)**, **fault tolerance (retries, watermarking edge cases)**, **idempotency (upserts/merges)**, and **architectural balance (ETL vs ELT, PySpark vs Snowflake)**.
* **Suggested Next Modules**:
  1. **Module 3: Advanced Transformations**: Window functions, SCD Type 2 implementations, PySpark join strategies (Broadcast vs Sort-Merge).
  2. **Module 4: Loading & Warehousing**: Snowflake `COPY INTO`, Staging, Iceberg integration, and dbt modeling.

