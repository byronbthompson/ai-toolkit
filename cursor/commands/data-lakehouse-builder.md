DATA LAKEHOUSE PLANNING & IMPLEMENTATION

Plan and implement data lakehouse architectures with medallion structure, dimensional modeling, and production-grade data engineering practices.

**CRITICAL**: This workflow ensures cost-effective, high-quality, observable data pipelines on Databricks platform.

**PHILOSOPHY**:
- **Medallion architecture first** - Bronze (raw), Silver (cleansed), Gold (business-level aggregates)
- **Dimensional modeling** - Star schema with facts and dimensions in Gold layer
- **Cost control** - Optimize compute, storage, and data transfer costs
- **Data quality by default** - Validation, monitoring, and alerting at every layer
- **Observability built-in** - Comprehensive logging, lineage, and metrics
- **CI/CD from day one** - Automated testing and deployment pipelines

---

## WHEN TO USE THIS WORKFLOW

Use this workflow when:
- Building a new data lakehouse or data warehouse
- Ingesting new data sources into existing lakehouse
- Creating new dimensional models (facts and dimensions)
- Migrating from legacy data warehouse to lakehouse
- Implementing analytics or reporting infrastructure
- Setting up data pipelines for ML/AI workloads

---

## PHASE 1: REQUIREMENTS GATHERING

### Step 1A: Data Source Discovery

**Question 1**: "What are your data sources?"

Present common categories and ask for specifics:

**Transactional Databases**:
- [ ] SQL Server / Azure SQL Database
- [ ] PostgreSQL
- [ ] MySQL / MariaDB
- [ ] Oracle
- [ ] Snowflake
- [ ] Other: ___

**SaaS Applications**:
- [ ] Salesforce
- [ ] HubSpot
- [ ] ServiceNow
- [ ] Dynamics 365
- [ ] NetSuite
- [ ] Other: ___

**Event Streams**:
- [ ] Azure Event Hubs
- [ ] Apache Kafka
- [ ] AWS Kinesis
- [ ] Google Pub/Sub
- [ ] Other: ___

**Files**:
- [ ] CSV / Excel files (Azure Blob Storage / S3 / GCS)
- [ ] JSON files
- [ ] Parquet files
- [ ] XML files
- [ ] Other: ___

**APIs**:
- [ ] REST APIs
- [ ] GraphQL APIs
- [ ] SOAP APIs
- [ ] Other: ___

For EACH data source, capture:
```markdown
## Data Source: <source-name>

**Type**: <Database/SaaS/Stream/File/API>
**Connection**:
- Host/URL: <connection string>
- Authentication: <method - service principal, managed identity, API key, etc.>
- Credentials storage: <Azure Key Vault / Databricks Secrets>

**Data Volume**:
- Current size: <GB/TB>
- Growth rate: <GB/day or TB/month>
- Record count: <approximate number>

**Change Capture**:
- [ ] Full load only (replace entire dataset)
- [ ] Incremental (append new records)
- [ ] CDC (Change Data Capture) - captures inserts, updates, deletes
- [ ] Timestamp-based (filter by modified_date column)
- [ ] Event-driven (triggers on data change)

**Load Frequency**:
- [ ] Real-time / Streaming
- [ ] Every 15 minutes
- [ ] Hourly
- [ ] Daily (batch)
- [ ] Weekly
- [ ] On-demand

**Critical Tables/Entities** (if applicable):
| Table/Entity | Record Count | Update Frequency | Business Purpose |
|--------------|--------------|------------------|------------------|
| <name> | <count> | <freq> | <purpose> |

**Data Sensitivity**:
- [ ] Contains PII (Personally Identifiable Information)
- [ ] Contains PHI (Protected Health Information - HIPAA)
- [ ] Contains PCI data (Payment Card Industry)
- [ ] Confidential business data
- [ ] Public data
```

---

### Step 1B: Business Requirements

**Question 2**: "What are the business use cases for this data?"

Capture use cases:
- [ ] **Operational Reporting** - Daily/weekly reports for business operations
- [ ] **Executive Dashboards** - KPIs and metrics for leadership
- [ ] **Self-Service Analytics** - Ad-hoc analysis by business users (Power BI, Tableau)
- [ ] **Machine Learning / AI** - Training data for ML models
- [ ] **Data Science Exploration** - Exploratory data analysis
- [ ] **Regulatory Reporting** - Compliance reports (SOX, GDPR, etc.)
- [ ] **Customer 360** - Unified view of customer data
- [ ] **Real-Time Analytics** - Low-latency insights
- [ ] Other: ___

**Question 3**: "What are the key metrics and dimensions?"

**Facts** (measures - what you want to analyze):
- Revenue, Sales Amount, Quantity Sold
- Cost, Profit Margin
- Order Count, Customer Count
- Page Views, Click-Through Rate
- Custom: ___

**Dimensions** (attributes - how you want to slice/dice):
- Date/Time (Year, Quarter, Month, Day, Hour)
- Geography (Country, State, City)
- Customer (Customer Name, Segment, Industry)
- Product (Product Name, Category, Brand)
- Custom: ___

**Question 4**: "What are the SLAs and performance requirements?"

- **Data Freshness**: <Real-time / 15 min / 1 hour / Daily>
- **Query Performance**: <Seconds for dashboards, minutes for ad-hoc queries>
- **Data Retention**: <How long to keep data - 1 year, 7 years, indefinitely>
- **Uptime SLA**: <99%, 99.9%, 99.99%>

---

### Step 1C: Existing Infrastructure Discovery

**Question 5**: "Do you already have a Databricks workspace?"

- **If YES**:
  - Workspace URL: ___
  - Region: <Azure region / AWS region>
  - Existing Delta tables: <Yes/No - if yes, list paths>
  - Existing clusters: <Yes/No - if yes, describe cluster configs>
  - Ask: "Should I extend the existing lakehouse or create a new one?"

- **If NO**:
  - Proceed to greenfield setup
  - Recommend cloud provider based on existing infrastructure

**Question 6**: "Where should data be stored?"

- **Azure**: Azure Data Lake Storage Gen2 (ADLS Gen2) - RECOMMENDED
- **AWS**: S3
- **GCP**: Google Cloud Storage

**Storage Structure**:
```
<storage-account>/
├── bronze/               # Raw ingested data (immutable)
│   ├── <source-system-1>/
│   ├── <source-system-2>/
├── silver/               # Cleansed, deduplicated, validated data
│   ├── <domain-1>/
│   ├── <domain-2>/
├── gold/                 # Business-level aggregates (dimensional model)
│   ├── dimensions/
│   │   ├── dim_date/
│   │   ├── dim_customer/
│   │   ├── dim_product/
│   ├── facts/
│   │   ├── fact_sales/
│   │   ├── fact_orders/
├── checkpoints/          # Streaming checkpoints
├── staging/              # Temporary staging area
```

---

## PHASE 2: MEDALLION ARCHITECTURE DESIGN

### Bronze Layer (Raw Ingestion)

**Purpose**: Immutable, raw data exactly as received from source systems

**Design Principles**:
- [ ] Preserve source data exactly as received (no transformations)
- [ ] Add metadata columns: `_ingestion_timestamp`, `_source_file`, `_source_system`
- [ ] Partition by ingestion date for performance and cost control
- [ ] Use Delta Lake format (ACID transactions, time travel)
- [ ] Never delete or update Bronze data (append-only)

**Bronze Table Template**:
```sql
CREATE TABLE bronze.<source_system>_<table_name> (
  -- All source columns (preserve original names and types)
  <original_column_1> <type>,
  <original_column_2> <type>,

  -- Metadata columns (REQUIRED)
  _ingestion_timestamp TIMESTAMP,
  _source_file STRING,
  _source_system STRING,
  _batch_id STRING
)
USING DELTA
PARTITIONED BY (_ingestion_date DATE)  -- Generated from _ingestion_timestamp
LOCATION 'abfss://<container>@<storage>.dfs.core.windows.net/bronze/<source_system>/<table_name>'
TBLPROPERTIES (
  'delta.autoOptimize.optimizeWrite' = 'true',
  'delta.autoOptimize.autoCompact' = 'true'
);
```

**Ingestion Pattern**:
- **Batch**: Use Databricks Auto Loader or COPY INTO for efficient incremental loads
- **Streaming**: Use Structured Streaming with Delta Lake sink
- **CDC**: Capture insert/update/delete operations with `_operation` column

---

### Silver Layer (Cleansed & Validated)

**Purpose**: Cleaned, validated, deduplicated, and conformed data

**Design Principles**:
- [ ] Apply data quality rules (validation, deduplication)
- [ ] Standardize column names (snake_case, consistent naming)
- [ ] Cast to appropriate data types (dates, numbers, booleans)
- [ ] Handle nulls and default values
- [ ] Join reference data (enrich with lookups)
- [ ] Partition by business date (e.g., order_date, transaction_date)
- [ ] Add data quality metadata: `_is_valid`, `_validation_errors`

**Silver Table Template**:
```sql
CREATE TABLE silver.<domain>_<entity> (
  -- Business columns (standardized names, correct types)
  <business_key> STRING NOT NULL,  -- Natural key from source
  <attribute_1> <type>,
  <attribute_2> <type>,

  -- Temporal columns (for SCD Type 2 if needed)
  effective_start_date DATE,
  effective_end_date DATE,
  is_current BOOLEAN,

  -- Metadata columns (REQUIRED)
  _source_system STRING,
  _ingestion_timestamp TIMESTAMP,
  _processing_timestamp TIMESTAMP,
  _is_valid BOOLEAN,
  _validation_errors ARRAY<STRING>
)
USING DELTA
PARTITIONED BY (<business_date_column>)  -- e.g., order_date
LOCATION 'abfss://<container>@<storage>.dfs.core.windows.net/silver/<domain>/<entity>'
TBLPROPERTIES (
  'delta.autoOptimize.optimizeWrite' = 'true',
  'delta.autoOptimize.autoCompact' = 'true'
);
```

**Data Quality Checks (REQUIRED)**:
```python
# Example data quality checks in Silver layer
from pyspark.sql import functions as F

df_silver = (
  df_bronze
  .withColumn("_is_valid",
    F.when(
      (F.col("order_id").isNotNull()) &  # Required field check
      (F.col("order_amount") > 0) &      # Business rule check
      (F.col("order_date").isNotNull()), # Required field check
      True
    ).otherwise(False)
  )
  .withColumn("_validation_errors",
    F.array_remove(
      F.array(
        F.when(F.col("order_id").isNull(), F.lit("order_id is null")),
        F.when(F.col("order_amount") <= 0, F.lit("order_amount must be positive")),
        F.when(F.col("order_date").isNull(), F.lit("order_date is null"))
      ),
      None
    )
  )
)
```

---

### Gold Layer (Dimensional Model)

**Purpose**: Business-level aggregates, dimensional model (star schema)

**Design Principles**:
- [ ] **Star schema**: Fact tables surrounded by dimension tables
- [ ] **Surrogate keys**: Use auto-incrementing integers for dimension keys
- [ ] **Slowly Changing Dimensions (SCD)**: Support Type 1 (overwrite) or Type 2 (historical tracking)
- [ ] **Conformed dimensions**: Reuse dimensions across multiple facts
- [ ] **Grain**: Define clear grain for each fact table (e.g., one row per order line item)
- [ ] **Aggregates**: Pre-aggregate for performance (daily, monthly summaries)

#### Dimension Tables

**Dimension Design Template**:
```sql
-- Example: Customer Dimension (SCD Type 2)
CREATE TABLE gold.dim_customer (
  customer_key BIGINT GENERATED ALWAYS AS IDENTITY,  -- Surrogate key
  customer_id STRING NOT NULL,                        -- Natural key (business key)
  customer_name STRING,
  customer_email STRING,
  customer_segment STRING,
  customer_country STRING,
  customer_state STRING,
  customer_city STRING,

  -- SCD Type 2 columns
  effective_start_date DATE,
  effective_end_date DATE,
  is_current BOOLEAN,

  -- Metadata
  _source_system STRING,
  _created_timestamp TIMESTAMP,
  _updated_timestamp TIMESTAMP
)
USING DELTA
LOCATION 'abfss://<container>@<storage>.dfs.core.windows.net/gold/dimensions/dim_customer'
TBLPROPERTIES (
  'delta.autoOptimize.optimizeWrite' = 'true',
  'delta.autoOptimize.autoCompact' = 'true'
);

-- Unique constraint on natural key + current flag
CREATE UNIQUE INDEX idx_customer_nk ON gold.dim_customer (customer_id, is_current);
```

**Standard Dimensions** (RECOMMENDED):
- [ ] **dim_date**: Date dimension with year, quarter, month, week, day attributes
- [ ] **dim_customer**: Customer attributes
- [ ] **dim_product**: Product attributes (category, brand, SKU)
- [ ] **dim_geography**: Location hierarchy (country, state, city)
- [ ] **dim_employee**: Employee/sales rep attributes
- [ ] Custom dimensions: ___

**Date Dimension Template**:
```sql
CREATE TABLE gold.dim_date (
  date_key INT,                    -- YYYYMMDD format (e.g., 20250101)
  date DATE,
  year INT,
  quarter INT,
  quarter_name STRING,             -- e.g., "Q1 2025"
  month INT,
  month_name STRING,               -- e.g., "January"
  month_abbr STRING,               -- e.g., "Jan"
  week_of_year INT,
  day_of_month INT,
  day_of_week INT,                 -- 1 = Monday, 7 = Sunday
  day_of_week_name STRING,         -- e.g., "Monday"
  day_of_week_abbr STRING,         -- e.g., "Mon"
  is_weekend BOOLEAN,
  is_holiday BOOLEAN,
  holiday_name STRING,
  fiscal_year INT,
  fiscal_quarter INT,
  fiscal_month INT
)
USING DELTA
LOCATION 'abfss://<container>@<storage>.dfs.core.windows.net/gold/dimensions/dim_date'
TBLPROPERTIES (
  'delta.autoOptimize.optimizeWrite' = 'true'
);
```

#### Fact Tables

**Fact Design Template**:
```sql
-- Example: Sales Fact (one row per order line item)
CREATE TABLE gold.fact_sales (
  sales_key BIGINT GENERATED ALWAYS AS IDENTITY,  -- Surrogate key

  -- Foreign keys to dimensions (use surrogate keys)
  date_key INT NOT NULL,
  customer_key BIGINT NOT NULL,
  product_key BIGINT NOT NULL,
  geography_key BIGINT,

  -- Degenerate dimensions (fact attributes that don't warrant own dimension)
  order_id STRING,
  order_line_number INT,

  -- Measures (additive facts)
  quantity INT,
  unit_price DECIMAL(18,2),
  discount_amount DECIMAL(18,2),
  tax_amount DECIMAL(18,2),
  total_amount DECIMAL(18,2),
  cost_amount DECIMAL(18,2),
  profit_amount DECIMAL(18,2),

  -- Metadata
  _source_system STRING,
  _created_timestamp TIMESTAMP
)
USING DELTA
PARTITIONED BY (date_key)  -- Partition by date for performance
LOCATION 'abfss://<container>@<storage>.dfs.core.windows.net/gold/facts/fact_sales'
TBLPROPERTIES (
  'delta.autoOptimize.optimizeWrite' = 'true',
  'delta.autoOptimize.autoCompact' = 'true'
);

-- Foreign key indexes (optimize joins)
CREATE INDEX idx_fact_sales_customer ON gold.fact_sales (customer_key);
CREATE INDEX idx_fact_sales_product ON gold.fact_sales (product_key);
```

**Fact Table Grain** (CRITICAL - define explicitly):
- fact_sales: One row per order line item
- fact_customer_activity: One row per customer per day
- fact_web_events: One row per page view event
- Custom: ___

---

## PHASE 3: DATA PIPELINE DESIGN

### Ingestion Pipeline (Bronze Layer)

**Batch Ingestion (Auto Loader - RECOMMENDED)**:
```python
# Auto Loader example (efficient incremental file ingestion)
df = (
  spark.readStream
  .format("cloudFiles")
  .option("cloudFiles.format", "json")  # or csv, parquet, etc.
  .option("cloudFiles.schemaLocation", "/mnt/checkpoints/bronze/<source>/_schema")
  .option("cloudFiles.inferColumnTypes", "true")
  .load("abfss://<container>@<storage>.dfs.core.windows.net/landing/<source>/")
  .withColumn("_ingestion_timestamp", F.current_timestamp())
  .withColumn("_source_file", F.input_file_name())
  .withColumn("_source_system", F.lit("<source-system-name>"))
  .withColumn("_batch_id", F.lit(dbutils.widgets.get("batch_id")))
)

# Write to Bronze Delta table
(
  df.writeStream
  .format("delta")
  .option("checkpointLocation", "/mnt/checkpoints/bronze/<source>")
  .option("mergeSchema", "true")  # Allow schema evolution
  .trigger(availableNow=True)  # Batch mode
  .partitionBy("_ingestion_date")
  .start("abfss://<container>@<storage>.dfs.core.windows.net/bronze/<source>/<table>")
)
```

**Streaming Ingestion (Event Hubs / Kafka)**:
```python
# Streaming ingestion from Event Hubs
df = (
  spark.readStream
  .format("eventhubs")
  .options(**eventhubs_config)
  .load()
  .withColumn("_ingestion_timestamp", F.current_timestamp())
  .withColumn("body", F.col("body").cast("string"))
  .select(F.from_json("body", schema).alias("data"), "_ingestion_timestamp")
  .select("data.*", "_ingestion_timestamp")
)

# Write to Bronze Delta table (streaming)
(
  df.writeStream
  .format("delta")
  .option("checkpointLocation", "/mnt/checkpoints/bronze/<source>")
  .trigger(processingTime="1 minute")  # Micro-batch every 1 minute
  .partitionBy("_ingestion_date")
  .start("abfss://<container>@<storage>.dfs.core.windows.net/bronze/<source>/<table>")
)
```

---

### Transformation Pipeline (Silver Layer)

**Silver Transformation Pattern**:
```python
# Read from Bronze (batch or streaming)
df_bronze = spark.readStream.table("bronze.<source>_<table>")

# Apply transformations
df_silver = (
  df_bronze
  # Standardize column names
  .withColumnRenamed("OldName", "new_name")

  # Cast to correct data types
  .withColumn("order_date", F.to_date("order_date_string", "yyyy-MM-dd"))
  .withColumn("order_amount", F.col("order_amount").cast("decimal(18,2)"))

  # Handle nulls
  .withColumn("customer_email", F.coalesce(F.col("customer_email"), F.lit("unknown@example.com")))

  # Data quality validation
  .withColumn("_is_valid",
    (F.col("order_id").isNotNull()) &
    (F.col("order_amount") > 0) &
    (F.col("order_date").isNotNull())
  )
  .withColumn("_validation_errors",
    F.when(~F.col("_is_valid"),
      F.concat_ws(", ",
        F.when(F.col("order_id").isNull(), F.lit("Missing order_id")),
        F.when(F.col("order_amount") <= 0, F.lit("Invalid order_amount"))
      )
    )
  )

  # Deduplication (keep latest record by timestamp)
  .withColumn("row_num", F.row_number().over(
    Window.partitionBy("order_id").orderBy(F.col("_ingestion_timestamp").desc())
  ))
  .filter(F.col("row_num") == 1)
  .drop("row_num")

  # Add metadata
  .withColumn("_processing_timestamp", F.current_timestamp())
)

# Write to Silver Delta table (MERGE for SCD Type 2)
def upsert_to_silver(batch_df, batch_id):
  from delta.tables import DeltaTable

  silver_table = DeltaTable.forPath(spark, "abfss://.../silver/<domain>/<entity>")

  silver_table.alias("target").merge(
    batch_df.alias("source"),
    "target.<business_key> = source.<business_key> AND target.is_current = true"
  ).whenMatchedUpdate(
    condition = "target.<hash_of_attributes> != source.<hash_of_attributes>",
    set = {
      "effective_end_date": "current_date()",
      "is_current": "false"
    }
  ).whenNotMatchedInsert(
    values = {
      **{col: f"source.{col}" for col in batch_df.columns},
      "effective_start_date": "current_date()",
      "effective_end_date": "to_date('9999-12-31')",
      "is_current": "true"
    }
  ).execute()

df_silver.writeStream.foreachBatch(upsert_to_silver).start()
```

---

### Gold Layer Pipeline (Dimensional Model)

**Dimension Load (SCD Type 2)**:
```python
# Load dimension with SCD Type 2 (track history)
from delta.tables import DeltaTable

df_new_customers = spark.table("silver.customers")

dim_customer = DeltaTable.forPath(spark, "abfss://.../gold/dimensions/dim_customer")

# Close out old records (set effective_end_date)
dim_customer.alias("target").merge(
  df_new_customers.alias("source"),
  """
  target.customer_id = source.customer_id
  AND target.is_current = true
  AND (
    target.customer_name != source.customer_name OR
    target.customer_email != source.customer_email OR
    target.customer_segment != source.customer_segment
  )
  """
).whenMatchedUpdate(
  set = {
    "effective_end_date": "current_date()",
    "is_current": "false",
    "_updated_timestamp": "current_timestamp()"
  }
).execute()

# Insert new records (new customers or changed attributes)
df_new_customers.write.format("delta").mode("append").save("abfss://.../gold/dimensions/dim_customer")
```

**Fact Load**:
```python
# Load fact table (join to dimensions to get surrogate keys)
df_fact = (
  spark.table("silver.orders")
  .alias("orders")
  .join(spark.table("gold.dim_date").alias("d"), F.col("orders.order_date") == F.col("d.date"))
  .join(spark.table("gold.dim_customer").alias("c"),
    (F.col("orders.customer_id") == F.col("c.customer_id")) & (F.col("c.is_current") == True)
  )
  .join(spark.table("gold.dim_product").alias("p"),
    (F.col("orders.product_id") == F.col("p.product_id")) & (F.col("p.is_current") == True)
  )
  .select(
    F.col("d.date_key"),
    F.col("c.customer_key"),
    F.col("p.product_key"),
    F.col("orders.order_id"),
    F.col("orders.quantity"),
    F.col("orders.unit_price"),
    F.col("orders.total_amount"),
    F.current_timestamp().alias("_created_timestamp")
  )
)

# Write to fact table (append only)
df_fact.write.format("delta").mode("append").save("abfss://.../gold/facts/fact_sales")
```

---

## PHASE 4: COST OPTIMIZATION

### Compute Cost Control

**Cluster Configuration** (CRITICAL for cost):
```python
# Use Databricks Jobs Clusters (terminate after job) NOT interactive clusters
{
  "cluster_name": "<job-name>-cluster",
  "spark_version": "14.3.x-scala2.12",  # LTS version
  "node_type_id": "Standard_D4ds_v5",   # RIGHT-SIZE: Start small, scale up if needed
  "autoscale": {
    "min_workers": 1,
    "max_workers": 10                    # Set max to control cost
  },
  "autotermination_minutes": 10,         # Terminate after idle
  "enable_elastic_disk": true,
  "azure_attributes": {
    "spot_bid_max_price": -1             # Use Spot VMs (70% cost savings)
  }
}
```

**Cluster Sizing Recommendations**:
- **Development**: Single-node cluster (Standard_D4ds_v5) - $0.50/hour
- **Small workloads (<1TB)**: 1-3 workers (Standard_D4ds_v5) - $1.50-$4.50/hour
- **Medium workloads (1-10TB)**: 3-10 workers (Standard_D8ds_v5) - $6-$20/hour
- **Large workloads (>10TB)**: 10-50 workers (Standard_D16ds_v5) - $20-$100/hour

**Use Spot VMs** (RECOMMENDED):
- [ ] Enable Spot instances for all non-critical workloads (70% cost savings)
- [ ] Use on-demand only for mission-critical SLA workloads

**Optimize Spark Jobs**:
```python
# Enable adaptive query execution (auto-optimize)
spark.conf.set("spark.sql.adaptive.enabled", "true")
spark.conf.set("spark.sql.adaptive.coalescePartitions.enabled", "true")

# Broadcast small lookup tables (< 100MB)
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", "100MB")

# Use Photon engine (3-5x faster for SQL workloads)
# Enable in cluster config: "runtime_engine": "PHOTON"
```

---

### Storage Cost Control

**Delta Lake Optimization**:
```sql
-- Compact small files (reduce storage cost and improve performance)
OPTIMIZE gold.fact_sales
ZORDER BY (date_key, customer_key);  -- Co-locate related data

-- Vacuum old files (delete old versions beyond retention period)
VACUUM gold.fact_sales RETAIN 168 HOURS;  -- 7 days retention

-- Set table properties for auto-optimization
ALTER TABLE gold.fact_sales SET TBLPROPERTIES (
  'delta.autoOptimize.optimizeWrite' = 'true',  -- Optimize on write
  'delta.autoOptimize.autoCompact' = 'true',    -- Auto-compact small files
  'delta.deletedFileRetentionDuration' = '7 days'  -- Vacuum policy
);
```

**Partitioning Strategy** (CRITICAL):
- [ ] **Bronze**: Partition by `_ingestion_date` (enables efficient deletion of old data)
- [ ] **Silver**: Partition by business date (e.g., `order_date`, `transaction_date`)
- [ ] **Gold Facts**: Partition by `date_key` (most common filter in queries)
- [ ] **Gold Dimensions**: Typically NO partitioning (small tables)

**Storage Tiers**:
- [ ] **Hot tier**: Current month data (frequently accessed)
- [ ] **Cool tier**: 1-12 months old (occasionally accessed) - 50% cheaper
- [ ] **Archive tier**: >12 months old (rarely accessed) - 90% cheaper

```python
# Move old partitions to Cool tier (Azure)
# Use Azure Data Lake lifecycle management policies
{
  "rules": [
    {
      "name": "MoveToCool",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "prefixMatch": ["bronze/", "silver/"]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": {"daysAfterModificationGreaterThan": 30}
          }
        }
      }
    }
  ]
}
```

---

### Data Transfer Cost Control

**Minimize Cross-Region Transfer**:
- [ ] Co-locate Databricks workspace and storage in same region
- [ ] Use regional endpoints (not public endpoints) for data access
- [ ] Avoid reading from other regions (expensive)

**Cache Frequently Accessed Data**:
```python
# Cache small reference tables in memory
dim_customer = spark.table("gold.dim_customer").cache()
dim_product = spark.table("gold.dim_product").cache()
```

---

## PHASE 5: DATA QUALITY & OBSERVABILITY

### Data Quality Framework

**Data Quality Dimensions**:
- [ ] **Completeness**: No missing required fields
- [ ] **Validity**: Data conforms to expected format/range
- [ ] **Accuracy**: Data is correct and up-to-date
- [ ] **Consistency**: Data is consistent across sources
- [ ] **Uniqueness**: No duplicate records
- [ ] **Timeliness**: Data is fresh and meets SLA

**Great Expectations Integration** (RECOMMENDED):
```python
import great_expectations as gx

# Define expectations for Silver table
context = gx.get_context()
suite = context.add_expectation_suite("silver.orders")

# Completeness checks
suite.add_expectation(
  gx.expectations.ExpectColumnValuesToNotBeNull(column="order_id")
)

# Validity checks
suite.add_expectation(
  gx.expectations.ExpectColumnValuesToBeBetween(
    column="order_amount",
    min_value=0,
    max_value=1000000
  )
)

# Uniqueness checks
suite.add_expectation(
  gx.expectations.ExpectColumnValuesToBeUnique(column="order_id")
)

# Run validation
results = context.run_checkpoint("silver_orders_checkpoint")

# Fail pipeline if validation fails
if not results["success"]:
  raise Exception(f"Data quality validation failed: {results}")
```

**Custom Data Quality Checks**:
```python
# Add data quality metrics to tables
def add_dq_metrics(df, table_name):
  from pyspark.sql import functions as F

  metrics = {
    "table_name": table_name,
    "row_count": df.count(),
    "null_count_order_id": df.filter(F.col("order_id").isNull()).count(),
    "negative_amount_count": df.filter(F.col("order_amount") < 0).count(),
    "duplicate_count": df.groupBy("order_id").count().filter("count > 1").count(),
    "check_timestamp": F.current_timestamp()
  }

  # Log to DQ metrics table
  spark.createDataFrame([metrics]).write.mode("append").saveAsTable("monitoring.dq_metrics")

  return df

df_silver = add_dq_metrics(df_silver, "silver.orders")
```

---

### Data Observability

**Data Lineage** (Unity Catalog):
```sql
-- Enable lineage tracking (Unity Catalog)
-- Automatically tracks table dependencies and column lineage

-- Query lineage
DESCRIBE HISTORY gold.fact_sales;  -- Shows all operations on table
```

**Monitoring Tables** (REQUIRED):
```sql
-- Pipeline run metrics
CREATE TABLE monitoring.pipeline_runs (
  pipeline_name STRING,
  run_id STRING,
  start_time TIMESTAMP,
  end_time TIMESTAMP,
  status STRING,  -- SUCCESS, FAILED, RUNNING
  rows_read BIGINT,
  rows_written BIGINT,
  duration_seconds INT,
  error_message STRING
)
USING DELTA
LOCATION 'abfss://.../monitoring/pipeline_runs';

-- Data quality metrics
CREATE TABLE monitoring.dq_metrics (
  table_name STRING,
  check_timestamp TIMESTAMP,
  row_count BIGINT,
  null_count_<column> BIGINT,
  validation_failures BIGINT,
  freshness_hours DECIMAL(10,2)
)
USING DELTA
LOCATION 'abfss://.../monitoring/dq_metrics';

-- SLA metrics
CREATE TABLE monitoring.sla_metrics (
  table_name STRING,
  sla_timestamp TIMESTAMP,
  expected_freshness_minutes INT,
  actual_freshness_minutes INT,
  sla_met BOOLEAN
)
USING DELTA
LOCATION 'abfss://.../monitoring/sla_metrics';
```

**Logging** (structured):
```python
import logging
import json

# Configure structured logging
logging.basicConfig(
  format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
  level=logging.INFO
)
logger = logging.getLogger(__name__)

# Log structured events
logger.info(json.dumps({
  "event": "pipeline_start",
  "pipeline_name": "bronze_to_silver",
  "source_table": "bronze.orders",
  "target_table": "silver.orders",
  "batch_id": batch_id,
  "timestamp": str(datetime.now())
}))
```

**Alerting**:
```python
# Send alert if data quality fails
def send_alert(message, severity="WARNING"):
  # Azure Monitor / AWS CloudWatch / Slack / PagerDuty
  import requests

  webhook_url = dbutils.secrets.get("alerts", "slack-webhook")

  payload = {
    "text": f"[{severity}] Data Quality Alert",
    "blocks": [
      {
        "type": "section",
        "text": {"type": "mrkdwn", "text": message}
      }
    ]
  }

  requests.post(webhook_url, json=payload)

# Example usage
if dq_failures > 0:
  send_alert(f"Data quality check failed for silver.orders: {dq_failures} validation errors", severity="ERROR")
```

---

## PHASE 6: CI/CD PIPELINE

### Source Control Structure

**Repository Layout**:
```
databricks-lakehouse/
├── notebooks/
│   ├── bronze/
│   │   ├── ingest_salesforce.py
│   │   ├── ingest_sql_server.py
│   ├── silver/
│   │   ├── transform_customers.py
│   │   ├── transform_orders.py
│   ├── gold/
│   │   ├── load_dim_customer.py
│   │   ├── load_fact_sales.py
├── sql/
│   ├── ddl/
│   │   ├── create_bronze_tables.sql
│   │   ├── create_silver_tables.sql
│   │   ├── create_gold_tables.sql
│   ├── dml/
│       ├── populate_dim_date.sql
├── config/
│   ├── dev.yaml
│   ├── staging.yaml
│   ├── prod.yaml
├── tests/
│   ├── test_bronze_ingestion.py
│   ├── test_silver_transformations.py
│   ├── test_data_quality.py
├── workflows/
│   ├── bronze_pipeline.yaml
│   ├── silver_pipeline.yaml
│   ├── gold_pipeline.yaml
├── .github/workflows/   # or .azure-pipelines/ or .gitlab-ci/
│   ├── deploy.yml
├── databricks.yml       # Databricks Asset Bundles config
├── requirements.txt
└── README.md
```

---

### Databricks Asset Bundles (DABs)

**databricks.yml**:
```yaml
bundle:
  name: lakehouse-pipelines

# Development environment
targets:
  dev:
    mode: development
    workspace:
      host: https://adb-<workspace-id>.azuredatabricks.net
      root_path: /Workspace/Users/<user>/dev
    resources:
      jobs:
        bronze_pipeline:
          name: bronze-pipeline-dev
          tasks:
            - task_key: ingest_salesforce
              notebook_task:
                notebook_path: ./notebooks/bronze/ingest_salesforce
              job_cluster_key: bronze_cluster
          job_clusters:
            - job_cluster_key: bronze_cluster
              new_cluster:
                spark_version: 14.3.x-scala2.12
                node_type_id: Standard_D4ds_v5
                num_workers: 1
                autotermination_minutes: 10
          schedule:
            quartz_cron_expression: "0 0 * * * ?"  # Hourly
            timezone_id: "UTC"

  # Production environment
  prod:
    mode: production
    workspace:
      host: https://adb-<workspace-id>.azuredatabricks.net
      root_path: /Workspace/Production
    resources:
      jobs:
        bronze_pipeline:
          name: bronze-pipeline-prod
          tasks:
            - task_key: ingest_salesforce
              notebook_task:
                notebook_path: ./notebooks/bronze/ingest_salesforce
              job_cluster_key: bronze_cluster
          job_clusters:
            - job_cluster_key: bronze_cluster
              new_cluster:
                spark_version: 14.3.x-scala2.12
                node_type_id: Standard_D8ds_v5
                autoscale:
                  min_workers: 2
                  max_workers: 10
                autotermination_minutes: 10
                azure_attributes:
                  spot_bid_max_price: -1  # Use Spot VMs
          schedule:
            quartz_cron_expression: "0 0 * * * ?"
            timezone_id: "UTC"
```

**Deploy with DABs**:
```bash
# Validate bundle
databricks bundle validate

# Deploy to dev
databricks bundle deploy -t dev

# Run job in dev
databricks bundle run bronze_pipeline -t dev

# Deploy to prod (after validation)
databricks bundle deploy -t prod
```

---

### GitHub Actions CI/CD

**.github/workflows/deploy.yml**:
```yaml
name: Deploy Databricks Lakehouse

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install pytest databricks-cli

      - name: Run unit tests
        run: pytest tests/

      - name: Validate bundle
        env:
          DATABRICKS_HOST: ${{ secrets.DATABRICKS_HOST }}
          DATABRICKS_TOKEN: ${{ secrets.DATABRICKS_TOKEN }}
        run: databricks bundle validate

  deploy-dev:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3

      - name: Deploy to dev
        env:
          DATABRICKS_HOST: ${{ secrets.DATABRICKS_HOST_DEV }}
          DATABRICKS_TOKEN: ${{ secrets.DATABRICKS_TOKEN_DEV }}
        run: |
          pip install databricks-cli
          databricks bundle deploy -t dev

  deploy-prod:
    needs: deploy-dev
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - uses: actions/checkout@v3

      - name: Deploy to prod
        env:
          DATABRICKS_HOST: ${{ secrets.DATABRICKS_HOST_PROD }}
          DATABRICKS_TOKEN: ${{ secrets.DATABRICKS_TOKEN_PROD }}
        run: |
          pip install databricks-cli
          databricks bundle deploy -t prod
```

---

### Testing Strategy

**Unit Tests**:
```python
# tests/test_bronze_ingestion.py
import pytest
from pyspark.sql import SparkSession
from notebooks.bronze.ingest_salesforce import transform_salesforce_data

@pytest.fixture(scope="session")
def spark():
  return SparkSession.builder.master("local[1]").getOrCreate()

def test_bronze_adds_metadata_columns(spark):
  # Arrange
  input_data = [{"account_id": "001", "account_name": "Acme Corp"}]
  df = spark.createDataFrame(input_data)

  # Act
  result_df = transform_salesforce_data(df)

  # Assert
  assert "_ingestion_timestamp" in result_df.columns
  assert "_source_system" in result_df.columns
  assert result_df.count() == 1
```

**Data Quality Tests**:
```python
# tests/test_data_quality.py
def test_silver_no_null_order_ids(spark):
  df = spark.table("silver.orders")
  null_count = df.filter("order_id IS NULL").count()
  assert null_count == 0, f"Found {null_count} null order IDs in silver.orders"

def test_silver_no_negative_amounts(spark):
  df = spark.table("silver.orders")
  negative_count = df.filter("order_amount < 0").count()
  assert negative_count == 0, f"Found {negative_count} negative amounts in silver.orders"
```

**Integration Tests**:
```python
# tests/test_end_to_end.py
def test_bronze_to_gold_pipeline(spark):
  # 1. Load test data to Bronze
  test_data = [{"order_id": "ORD001", "customer_id": "CUST001", "amount": 100.00}]
  df_test = spark.createDataFrame(test_data)
  df_test.write.mode("overwrite").saveAsTable("bronze.test_orders")

  # 2. Run Silver transformation
  run_silver_pipeline("bronze.test_orders", "silver.test_orders")

  # 3. Run Gold transformation
  run_gold_pipeline("silver.test_orders", "gold.test_fact_sales")

  # 4. Validate Gold table
  df_gold = spark.table("gold.test_fact_sales")
  assert df_gold.count() == 1
  assert df_gold.filter("total_amount = 100.00").count() == 1
```

---

## PHASE 7: SECURITY & COMPLIANCE

### Authentication & Authorization

**Unity Catalog** (RECOMMENDED):
```sql
-- Create catalog hierarchy
CREATE CATALOG lakehouse;
CREATE SCHEMA lakehouse.bronze;
CREATE SCHEMA lakehouse.silver;
CREATE SCHEMA lakehouse.gold;

-- Grant permissions (principle of least privilege)
GRANT USAGE ON CATALOG lakehouse TO `data-engineers`;
GRANT SELECT ON SCHEMA lakehouse.gold TO `business-analysts`;
GRANT ALL PRIVILEGES ON SCHEMA lakehouse.bronze TO `data-engineers`;

-- Row-level security (RLS) - CRITICAL for multi-tenant and sensitive data
-- Pattern 1: Restrict rows by user group
CREATE OR REPLACE FUNCTION filter_by_region()
RETURN IF(IS_MEMBER('regional-analysts'),
  CASE
    WHEN IS_MEMBER('north-america-analysts') THEN 'region IN ("US", "CA", "MX")'
    WHEN IS_MEMBER('europe-analysts') THEN 'region IN ("UK", "FR", "DE")'
    ELSE '1=0'  -- No access
  END,
  '1=1'  -- Global access for admins
);

ALTER TABLE gold.fact_sales SET ROW FILTER filter_by_region ON (region);

-- Pattern 2: Restrict by customer_id (B2B multi-tenant)
CREATE OR REPLACE FUNCTION filter_by_customer()
RETURN IF(IS_MEMBER('customer-users'),
  CONCAT('customer_id = "', CURRENT_USER(), '"'),  -- Users see only their data
  '1=1'  -- Internal users see all
);

ALTER TABLE gold.fact_sales SET ROW FILTER filter_by_customer ON (customer_id);

-- Pattern 3: Restrict by sensitive flag
CREATE OR REPLACE FUNCTION filter_sensitive_records()
RETURN IF(IS_MEMBER('pii-authorized'),
  '1=1',  -- See all records
  'is_sensitive = false'  -- See only non-sensitive records
);

ALTER TABLE silver.customers SET ROW FILTER filter_sensitive_records ON (is_sensitive);

-- Column-level masking (dynamic data masking)
CREATE OR REPLACE FUNCTION mask_email(email STRING)
RETURN IF(IS_MEMBER('pii-authorized'),
  email,  -- Show real email
  CONCAT(SUBSTRING(email, 1, 2), '***@', SPLIT(email, '@')[1])  -- Mask email
);

ALTER TABLE silver.customers ALTER COLUMN email SET MASK mask_email;

-- Pattern 4: Mask SSN/Credit Card
CREATE OR REPLACE FUNCTION mask_ssn(ssn STRING)
RETURN IF(IS_MEMBER('pii-authorized'), ssn, '***-**-****');

ALTER TABLE silver.customers ALTER COLUMN ssn SET MASK mask_ssn;

-- Pattern 5: Hide entire column from unauthorized users
CREATE OR REPLACE FUNCTION hide_salary(salary DECIMAL)
RETURN IF(IS_MEMBER('hr-team'), salary, NULL);

ALTER TABLE silver.employees ALTER COLUMN salary SET MASK hide_salary;
```

**Service Principal Authentication**:
```python
# Use Azure Service Principal for data access (NOT user credentials)
spark.conf.set("fs.azure.account.auth.type.<storage-account>.dfs.core.windows.net", "OAuth")
spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account>.dfs.core.windows.net",
  "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account>.dfs.core.windows.net",
  dbutils.secrets.get("azure", "client-id"))
spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account>.dfs.core.windows.net",
  dbutils.secrets.get("azure", "client-secret"))
spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account>.dfs.core.windows.net",
  "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
```

---

### Data Encryption

**Encryption at Rest** (REQUIRED):
- [ ] Azure Storage: Encrypted by default with Microsoft-managed keys
- [ ] Option: Customer-managed keys (CMK) in Azure Key Vault for compliance

**Encryption in Transit** (REQUIRED):
- [ ] HTTPS/TLS 1.2+ for all data transfers
- [ ] Databricks cluster-to-storage encryption enabled by default

---

### PII/PHI Handling

**Data Masking**:
```python
# Mask PII in Silver/Gold layers
from pyspark.sql import functions as F

df_masked = (
  df_silver
  .withColumn("email_masked",
    F.concat(
      F.substring("email", 1, 2),
      F.lit("***@"),
      F.split("email", "@")[1]
    )
  )
  .withColumn("ssn_masked", F.lit("***-**-****"))
)
```

**Column-Level Encryption**:
```python
# Encrypt sensitive columns
from pyspark.sql import functions as F

encryption_key = dbutils.secrets.get("encryption", "column-key")

df_encrypted = df.withColumn(
  "credit_card_encrypted",
  F.expr(f"aes_encrypt(credit_card, '{encryption_key}')")
)
```

---

### Audit Logging

**Enable Unity Catalog Audit Logs**:
```sql
-- Audit logs automatically captured for:
-- - Data access (SELECT queries)
-- - Data modifications (INSERT, UPDATE, DELETE)
-- - Permission changes (GRANT, REVOKE)

-- Query audit logs
SELECT * FROM system.access.audit
WHERE action_name = 'SELECT'
  AND request_params.table_name = 'gold.fact_sales'
  AND event_time > current_timestamp() - INTERVAL 7 DAYS;
```

---

## PHASE 8: STRUCTURED LOGGING & OBSERVABILITY

### Logging Requirements

**Structured Logging Format**:
- [ ] JSON-formatted logs (machine-parseable)
- [ ] Standard fields: timestamp, level, pipeline_name, table_name, batch_id, trace_id, user_id
- [ ] Consistent log levels: DEBUG, INFO, WARN, ERROR, FATAL
- [ ] No PII/secrets in logs

**Log Aggregation**:
- **Azure**: Azure Monitor / Log Analytics Workspace
- **AWS**: CloudWatch Logs
- **Databricks**: Built-in logging to `/dbfs/cluster-logs/`

**Pipeline Logging** (REQUIRED):
```python
import logging
import json
from datetime import datetime

logger = logging.getLogger(__name__)

def log_pipeline_event(event_type, details):
  log_entry = {
    "timestamp": datetime.now().isoformat(),
    "level": "INFO",
    "pipeline_name": dbutils.widgets.get("pipeline_name"),
    "table_name": details.get("table_name"),
    "batch_id": details.get("batch_id"),
    "trace_id": details.get("trace_id"),
    "event_type": event_type,
    "rows_processed": details.get("rows_processed"),
    "duration_seconds": details.get("duration_seconds"),
    "status": details.get("status"),
    "error_message": details.get("error_message")
  }
  logger.info(json.dumps(log_entry))

# Usage
log_pipeline_event("pipeline_start", {
  "table_name": "bronze.salesforce_accounts",
  "batch_id": "20250101-001",
  "trace_id": "abc123"
})
```

---

### Metrics & Monitoring

**Pipeline Metrics** (REQUIRED):
- [ ] **Throughput**: Rows/second, GB/hour processed
- [ ] **Latency**: Pipeline end-to-end duration (p50, p95, p99)
- [ ] **Error rate**: Failed pipelines / Total pipelines
- [ ] **Data freshness**: Time since last successful update
- [ ] **Data volume**: Rows ingested, bytes written
- [ ] **Cost metrics**: DBU consumption, compute hours

**Databricks Job Metrics**:
```python
# Emit custom metrics to monitoring system
from azure.monitor.opentelemetry import configure_azure_monitor
from opentelemetry import metrics

configure_azure_monitor(connection_string=dbutils.secrets.get("monitoring", "app-insights"))
meter = metrics.get_meter(__name__)

rows_processed_counter = meter.create_counter("lakehouse.rows_processed")
pipeline_duration_histogram = meter.create_histogram("lakehouse.pipeline_duration_seconds")

# Record metrics
rows_processed_counter.add(df.count(), {"pipeline": "bronze_to_silver", "table": "orders"})
pipeline_duration_histogram.record(duration_seconds, {"pipeline": "bronze_to_silver"})
```

---

### Alerting

**Critical Alerts** (REQUIRED):
- [ ] Pipeline failure (any job fails)
- [ ] Data quality validation failure (>5% of rows fail validation)
- [ ] Data freshness SLA miss (data not updated within SLA window)
- [ ] Storage capacity >80%
- [ ] Cost anomaly (DBU consumption >20% above baseline)

**Warning Alerts**:
- [ ] Pipeline duration >2x baseline
- [ ] Data quality degradation (>1% validation failures)
- [ ] Cluster auto-scaling to max capacity
- [ ] Schema drift detected

**Alert Configuration**:
```python
# Azure Monitor alert example
from azure.monitor.query import MetricsQueryClient

def check_pipeline_sla(table_name, sla_minutes):
  # Query last update time
  last_update = spark.sql(f"""
    SELECT MAX(_processing_timestamp) as last_update
    FROM {table_name}
  """).collect()[0]["last_update"]

  freshness_minutes = (datetime.now() - last_update).total_seconds() / 60

  if freshness_minutes > sla_minutes:
    send_alert(
      f"SLA MISS: {table_name} has not been updated in {freshness_minutes} minutes (SLA: {sla_minutes} min)",
      severity="CRITICAL"
    )
```

---

### Dashboards

**Required Dashboards**:
- [ ] **Pipeline Health**: Success/failure rates, duration trends, error counts
- [ ] **Data Quality**: Validation failure trends, completeness metrics, freshness
- [ ] **Cost Monitoring**: DBU consumption by pipeline, cluster utilization, storage costs
- [ ] **Performance**: Query latency, data volume trends, throughput
- [ ] **SLA Compliance**: Data freshness vs SLA, uptime percentage

---

## PHASE 9: AI/ML INTEGRATION & GENIE

**CRITICAL**: Modern lakehouse must support AI/ML workloads, natural language queries (Genie), and vector databases for RAG/knowledge bases.

### Databricks Genie (Natural Language to SQL)

**Enable Genie Spaces**:
```sql
-- Create Genie space for business users (natural language SQL)
CREATE GENIE SPACE sales_analytics
CATALOG lakehouse
SCHEMAS (gold)
DESCRIPTION 'Natural language interface to sales data'
INSTRUCTIONS '
- Use fact_sales for sales transactions
- Use dim_customer for customer attributes
- Use dim_date for time-based analysis
- Revenue is in USD
- Date range: 2020-present
';

-- Grant access to business users
GRANT USE GENIE SPACE sales_analytics TO `business-analysts`;
```

**Genie Best Practices**:
- [ ] Create separate Genie spaces per domain (sales, marketing, finance)
- [ ] Provide clear instructions and business context in space description
- [ ] Limit to Gold layer only (users should not query Bronze/Silver)
- [ ] Include sample questions to guide users
- [ ] Monitor Genie queries for cost control

**Sample Questions Genie Can Answer**:
- "What were total sales last quarter?"
- "Show me top 10 customers by revenue"
- "Compare sales this year vs last year by product category"
- "What's the average order value by region?"

---

### Vector Databases for RAG & Knowledge Bases

**Use Case 1: Document Search with Vector Similarity**

Create vector index for semantic search over documents:

```python
from databricks.vector_search.client import VectorSearchClient

# Initialize Vector Search client
vsc = VectorSearchClient()

# Create vector search endpoint
vsc.create_endpoint(
  name="knowledge-base-endpoint",
  endpoint_type="STANDARD"
)

# Create vector index on document embeddings
vsc.create_delta_sync_index(
  endpoint_name="knowledge-base-endpoint",
  index_name="lakehouse.gold.documents_vector_index",
  source_table_name="lakehouse.gold.documents",
  primary_key="document_id",
  embedding_source_column="embedding",  # Pre-computed embeddings
  embedding_model_endpoint_name="databricks-bge-large-en"  # Or custom model
)
```

**Document Table with Embeddings**:
```sql
CREATE TABLE gold.documents (
  document_id STRING PRIMARY KEY,
  document_text STRING,
  document_title STRING,
  document_category STRING,
  embedding ARRAY<FLOAT>,  -- 1024-dimensional vector from BGE-large model
  _created_timestamp TIMESTAMP
)
USING DELTA
LOCATION 'abfss://.../gold/documents'
TBLPROPERTIES (
  'delta.enableChangeDataFeed' = 'true'  -- Required for vector search sync
);
```

**Generate Embeddings**:
```python
# Use Databricks Foundation Model API for embeddings
import mlflow.deployments

client = mlflow.deployments.get_deploy_client("databricks")

def generate_embeddings(texts):
  response = client.predict(
    endpoint="databricks-bge-large-en",
    inputs={"input": texts}
  )
  return [item["embedding"] for item in response["data"]]

# Process documents and create embeddings
from pyspark.sql import functions as F
from pyspark.sql.types import ArrayType, FloatType

# UDF to generate embeddings
@F.udf(returnType=ArrayType(FloatType()))
def get_embedding(text):
  return generate_embeddings([text])[0]

df_with_embeddings = (
  spark.table("silver.documents")
  .withColumn("embedding", get_embedding(F.col("document_text")))
)

df_with_embeddings.write.format("delta").mode("overwrite").saveAsTable("gold.documents")
```

**Semantic Search**:
```python
# Search similar documents using vector similarity
results = vsc.get_index(
  index_name="lakehouse.gold.documents_vector_index"
).similarity_search(
  query_text="What is the data retention policy?",
  columns=["document_id", "document_title", "document_text"],
  num_results=5
)

# Returns top 5 most similar documents
for result in results["result"]["data_array"]:
  print(f"Title: {result[1]}, Score: {result[-1]}")
```

---

### MCP Server Integration (Model Context Protocol)

**CRITICAL**: Support for Claude/LLM tools to access lakehouse data via MCP servers.

**MCP Server for Lakehouse Data Access**:
```python
# mcp_server/lakehouse_tools.py
from mcp.server import Server
from mcp.types import Tool, TextContent
import databricks.sql as dbsql

app = Server("lakehouse-mcp-server")

# Define tools that Claude can call
@app.list_tools()
async def list_tools() -> list[Tool]:
  return [
    Tool(
      name="query_sales_data",
      description="Query sales data from the lakehouse. Supports filtering by date range, customer, product.",
      inputSchema={
        "type": "object",
        "properties": {
          "start_date": {"type": "string", "description": "Start date (YYYY-MM-DD)"},
          "end_date": {"type": "string", "description": "End date (YYYY-MM-DD)"},
          "customer_id": {"type": "string", "description": "Optional customer filter"},
          "aggregation": {"type": "string", "enum": ["sum", "avg", "count"], "default": "sum"}
        },
        "required": ["start_date", "end_date"]
      }
    ),
    Tool(
      name="search_knowledge_base",
      description="Search company knowledge base using semantic search.",
      inputSchema={
        "type": "object",
        "properties": {
          "query": {"type": "string", "description": "Search query"},
          "top_k": {"type": "integer", "default": 5, "description": "Number of results"}
        },
        "required": ["query"]
      }
    )
  ]

@app.call_tool()
async def call_tool(name: str, arguments: dict) -> list[TextContent]:
  if name == "query_sales_data":
    # Connect to Databricks SQL Warehouse
    with dbsql.connect(
      server_hostname=os.getenv("DATABRICKS_SERVER_HOSTNAME"),
      http_path=os.getenv("DATABRICKS_HTTP_PATH"),
      access_token=os.getenv("DATABRICKS_TOKEN")
    ) as connection:
      with connection.cursor() as cursor:
        sql = f"""
        SELECT
          SUM(total_amount) as total_sales,
          COUNT(*) as order_count
        FROM lakehouse.gold.fact_sales fs
        JOIN lakehouse.gold.dim_date dd ON fs.date_key = dd.date_key
        WHERE dd.date BETWEEN '{arguments['start_date']}' AND '{arguments['end_date']}'
        """
        if arguments.get("customer_id"):
          sql += f" AND fs.customer_key = (SELECT customer_key FROM lakehouse.gold.dim_customer WHERE customer_id = '{arguments['customer_id']}')"

        cursor.execute(sql)
        result = cursor.fetchall()

        return [TextContent(
          type="text",
          text=f"Sales Results:\nTotal Sales: ${result[0][0]:,.2f}\nOrder Count: {result[0][1]:,}"
        )]

  elif name == "search_knowledge_base":
    from databricks.vector_search.client import VectorSearchClient

    vsc = VectorSearchClient()
    results = vsc.get_index(
      index_name="lakehouse.gold.documents_vector_index"
    ).similarity_search(
      query_text=arguments["query"],
      columns=["document_title", "document_text"],
      num_results=arguments.get("top_k", 5)
    )

    documents = "\n\n".join([
      f"Document: {r[0]}\n{r[1][:500]}..."
      for r in results["result"]["data_array"]
    ])

    return [TextContent(
      type="text",
      text=f"Found {len(results['result']['data_array'])} relevant documents:\n\n{documents}"
    )]

# Run MCP server
if __name__ == "__main__":
  import asyncio
  asyncio.run(app.run())
```

**Deploy MCP Server**:
```yaml
# docker-compose.yml for MCP server
version: '3.8'
services:
  lakehouse-mcp-server:
    build: ./mcp_server
    ports:
      - "3000:3000"
    environment:
      DATABRICKS_SERVER_HOSTNAME: ${DATABRICKS_SERVER_HOSTNAME}
      DATABRICKS_HTTP_PATH: ${DATABRICKS_HTTP_PATH}
      DATABRICKS_TOKEN: ${DATABRICKS_TOKEN}
      VECTOR_SEARCH_ENDPOINT: ${VECTOR_SEARCH_ENDPOINT}
    restart: unless-stopped
```

**Claude Desktop Integration**:
```json
// Claude Desktop config (~/.config/claude/config.json)
{
  "mcpServers": {
    "lakehouse": {
      "command": "docker",
      "args": ["run", "-p", "3000:3000", "lakehouse-mcp-server"],
      "env": {
        "DATABRICKS_TOKEN": "your-token-here"
      }
    }
  }
}
```

---

### RAG Pipeline for LLM Applications

**Architecture**:
```
User Query
  ↓
[Embedding Model] → Query Vector
  ↓
[Vector Search] → Top K Similar Documents
  ↓
[LLM (Claude/GPT)] + Retrieved Context → Answer
```

**Implementation**:
```python
# RAG pipeline using Databricks and vector search
def rag_query(user_question: str, model: str = "claude-3-5-sonnet-20241022") -> str:
  from databricks.vector_search.client import VectorSearchClient
  import anthropic

  # 1. Retrieve relevant documents from vector index
  vsc = VectorSearchClient()
  search_results = vsc.get_index(
    index_name="lakehouse.gold.documents_vector_index"
  ).similarity_search(
    query_text=user_question,
    columns=["document_title", "document_text", "document_category"],
    num_results=5
  )

  # 2. Build context from retrieved documents
  context = "\n\n".join([
    f"Document: {result[0]}\nCategory: {result[2]}\n{result[1]}"
    for result in search_results["result"]["data_array"]
  ])

  # 3. Query LLM with context
  client = anthropic.Anthropic(api_key=dbutils.secrets.get("llm", "anthropic-key"))

  message = client.messages.create(
    model=model,
    max_tokens=1024,
    messages=[
      {
        "role": "user",
        "content": f"""Answer the following question using ONLY the provided context.
If the context doesn't contain enough information, say so.

Context:
{context}

Question: {user_question}

Answer:"""
      }
    ]
  )

  return message.content[0].text

# Example usage
answer = rag_query("What is our data retention policy?")
print(answer)
```

---

### ML Feature Store Integration

**Create Feature Tables** (for ML models):
```python
from databricks.feature_engineering import FeatureEngineeringClient

fe = FeatureEngineeringClient()

# Create feature table from Gold layer
fe.create_table(
  name="lakehouse.gold.customer_features",
  primary_keys=["customer_id"],
  df=spark.sql("""
    SELECT
      c.customer_id,
      c.customer_segment,
      c.customer_country,
      COUNT(fs.sales_key) as total_orders,
      SUM(fs.total_amount) as lifetime_value,
      AVG(fs.total_amount) as avg_order_value,
      MAX(dd.date) as last_purchase_date,
      DATEDIFF(CURRENT_DATE(), MAX(dd.date)) as days_since_last_purchase
    FROM lakehouse.gold.dim_customer c
    LEFT JOIN lakehouse.gold.fact_sales fs ON c.customer_key = fs.customer_key
    LEFT JOIN lakehouse.gold.dim_date dd ON fs.date_key = dd.date_key
    WHERE c.is_current = true
    GROUP BY c.customer_id, c.customer_segment, c.customer_country
  """),
  description="Customer behavioral features for churn prediction"
)
```

**Use Features in ML Model**:
```python
from databricks.feature_engineering import FeatureLookup

# Define feature lookups
feature_lookups = [
  FeatureLookup(
    table_name="lakehouse.gold.customer_features",
    lookup_key="customer_id",
    feature_names=["total_orders", "lifetime_value", "avg_order_value", "days_since_last_purchase"]
  )
]

# Create training set
training_set = fe.create_training_set(
  df=spark.table("lakehouse.gold.customer_labels"),  # Labels for supervised learning
  feature_lookups=feature_lookups,
  label="is_churned"
)

# Train model with feature tracking
import mlflow
from sklearn.ensemble import RandomForestClassifier

with mlflow.start_run():
  # Feature engineering client automatically logs features
  training_df = training_set.load_df().toPandas()

  X = training_df.drop("is_churned", axis=1)
  y = training_df["is_churned"]

  model = RandomForestClassifier(n_estimators=100)
  model.fit(X, y)

  # Log model with feature metadata
  fe.log_model(
    model=model,
    artifact_path="model",
    flavor=mlflow.sklearn,
    training_set=training_set
  )
```

---

### AI/ML Observability

**Track Model Performance**:
```sql
-- Create model monitoring table
CREATE TABLE monitoring.model_performance (
  model_name STRING,
  model_version STRING,
  prediction_timestamp TIMESTAMP,
  prediction_id STRING,
  customer_id STRING,
  predicted_churn_probability FLOAT,
  actual_churn BOOLEAN,  -- Filled in later with ground truth
  feature_values MAP<STRING, FLOAT>,
  _created_timestamp TIMESTAMP
)
USING DELTA
LOCATION 'abfss://.../monitoring/model_performance';

-- Monitor for data drift
CREATE TABLE monitoring.feature_drift (
  feature_name STRING,
  check_timestamp TIMESTAMP,
  training_mean FLOAT,
  training_std FLOAT,
  current_mean FLOAT,
  current_std FLOAT,
  drift_score FLOAT,  -- Statistical distance (e.g., KL divergence)
  alert_threshold FLOAT,
  is_drifting BOOLEAN
)
USING DELTA
LOCATION 'abfss://.../monitoring/feature_drift';
```

**Alert on Model Degradation**:
```python
# Check model accuracy over time
def check_model_performance():
  df_predictions = spark.sql("""
    SELECT
      model_version,
      DATE(prediction_timestamp) as prediction_date,
      COUNT(*) as total_predictions,
      SUM(CASE WHEN predicted_churn_probability > 0.5 THEN 1 ELSE 0 END) as predicted_churns,
      SUM(CASE WHEN actual_churn = true THEN 1 ELSE 0 END) as actual_churns,
      -- Calculate precision/recall when ground truth is available
      SUM(CASE WHEN predicted_churn_probability > 0.5 AND actual_churn = true THEN 1 ELSE 0 END) as true_positives
    FROM monitoring.model_performance
    WHERE prediction_timestamp > CURRENT_DATE() - INTERVAL 7 DAYS
      AND actual_churn IS NOT NULL  -- Only rows with ground truth
    GROUP BY model_version, DATE(prediction_timestamp)
  """)

  # Alert if precision drops below threshold
  for row in df_predictions.collect():
    precision = row.true_positives / row.predicted_churns if row.predicted_churns > 0 else 0
    if precision < 0.70:  # 70% precision threshold
      send_alert(
        f"Model performance degradation: {row.model_version} precision={precision:.2%} on {row.prediction_date}",
        severity="WARNING"
      )
```

---

### AI/ML Integration Checklist

**Vector Search Setup**:
- [ ] Vector search endpoint created
- [ ] Documents table with embeddings created
- [ ] Vector index synced to Delta table
- [ ] Semantic search tested and validated
- [ ] Monitoring for vector index refresh lag

**Genie Setup**:
- [ ] Genie spaces created per domain
- [ ] Instructions and context provided
- [ ] Access granted to business users
- [ ] Sample questions documented
- [ ] Cost monitoring for Genie queries enabled

**MCP Server Setup** (if applicable):
- [ ] MCP server implemented with lakehouse tools
- [ ] Authentication secured (Databricks PAT)
- [ ] Tools documented for LLM integration
- [ ] MCP server deployed and accessible
- [ ] Integration tested with Claude/LLM

**RAG Pipeline** (if applicable):
- [ ] Document corpus loaded and embedded
- [ ] RAG pipeline implemented and tested
- [ ] LLM API keys secured in Databricks Secrets
- [ ] Response quality validated
- [ ] Latency and cost monitored

**ML Feature Store** (if applicable):
- [ ] Feature tables created from Gold layer
- [ ] Feature freshness SLA defined
- [ ] Feature lineage tracked
- [ ] Model training integrated with feature store
- [ ] Feature drift monitoring enabled

---

## PHASE 10: DOCUMENTATION

### Engineering Documentation

**Architecture Documentation** (REQUIRED):
- [ ] **Lakehouse Architecture Diagram**: Visual representation of Bronze/Silver/Gold layers
  - Show data sources, storage paths, pipelines, and consumption layers
  - Tool: draw.io, Lucidchart, or Databricks Workspace diagrams
  - Store: `/docs/architecture/lakehouse-architecture.png`
- [ ] **Data Lineage Diagram**: End-to-end data flow from sources to reports
  - Unity Catalog auto-generates lineage
  - Store: Unity Catalog lineage viewer
- [ ] **Dimensional Model (ERD)**: Star schema showing facts and dimensions
  - Show grain, foreign keys, measures
  - Tool: dbdiagram.io, ERDPlus
  - Store: `/docs/architecture/dimensional-model.png`

**Table Documentation** (REQUIRED):
```sql
-- Document tables with comments
COMMENT ON TABLE gold.fact_sales IS 'Daily sales transactions at order line item grain';
COMMENT ON COLUMN gold.fact_sales.date_key IS 'Foreign key to dim_date (YYYYMMDD format)';
COMMENT ON COLUMN gold.fact_sales.total_amount IS 'Total sale amount including tax (USD)';

-- Create data dictionary
CREATE TABLE monitoring.data_dictionary (
  schema_name STRING,
  table_name STRING,
  column_name STRING,
  data_type STRING,
  is_nullable BOOLEAN,
  description STRING,
  sample_values STRING
)
USING DELTA
LOCATION 'abfss://.../monitoring/data_dictionary';
```

**Pipeline Documentation**:
- [ ] **Pipeline README**: Purpose, schedule, dependencies, owner
- [ ] **Data Source Configuration**: Connection details, credentials, change capture method
- [ ] **Transformation Logic**: Business rules applied in Silver and Gold layers
- [ ] **Data Quality Rules**: Validation checks and thresholds
- [ ] Store: `/notebooks/<layer>/README.md` for each pipeline

---

### Operational Documentation

**Runbooks** (REQUIRED):
- [ ] **Pipeline Failure Runbook**: How to diagnose and fix failed pipelines
  - Check logs in Databricks Job Runs
  - Common errors and resolutions
  - Escalation path
  - Store: `/docs/runbooks/pipeline-failure.md`
- [ ] **Data Quality Incident Runbook**: How to handle data quality issues
  - Investigate validation failures
  - Quarantine bad data
  - Root cause analysis
  - Store: `/docs/runbooks/data-quality-incident.md`
- [ ] **SLA Miss Runbook**: How to respond to data freshness SLA misses
  - Check pipeline status
  - Manually trigger pipeline if needed
  - Notify stakeholders
  - Store: `/docs/runbooks/sla-miss.md`

**Operations Manual**:
- [ ] **Daily Operations**: Routine checks (pipeline health, data quality, costs)
- [ ] **Maintenance Procedures**: OPTIMIZE, VACUUM, cluster upgrades
- [ ] **On-Call Guide**: Alert descriptions, first response, escalation
- [ ] Store: `/docs/operations/`

---

### User Documentation

**Data Catalog** (Unity Catalog):
- [ ] All tables registered in Unity Catalog
- [ ] Table and column descriptions populated
- [ ] Tags applied (PII, confidential, domain)
- [ ] Owners assigned to each table

**User Guides**:
- [ ] **BI Analyst Guide**: How to query Gold tables in Power BI / Tableau
- [ ] **Data Scientist Guide**: How to access Silver tables for ML
- [ ] **Self-Service Analytics Guide**: How to explore data using SQL warehouses
- [ ] Store: `/docs/user-guide/`

---

### Documentation Checklist

**Before Production Deployment** (MANDATORY):
- [ ] Lakehouse architecture diagram created and reviewed
- [ ] Dimensional model (ERD) documented
- [ ] All tables have descriptions and column comments
- [ ] Data dictionary populated
- [ ] Pipeline documentation complete (README for each pipeline)
- [ ] Data quality rules documented
- [ ] Runbooks created and tested (pipeline failure, DQ incident, SLA miss)
- [ ] Operations manual complete
- [ ] User guides published
- [ ] Unity Catalog fully populated
- [ ] All documentation reviewed and approved
- [ ] Knowledge transfer session conducted with data team

**Documentation Storage**:
- **Engineering Docs**: `/docs/` in repository
- **Runbooks**: `/docs/runbooks/`
- **Architecture Diagrams**: `/docs/architecture/`
- **User Docs**: `/docs/user-guide/` or internal wiki
- **Data Catalog**: Unity Catalog (accessible via Databricks UI)

---

## KEY PRINCIPLES

**Medallion Architecture** (MANDATORY):
- Bronze: Raw, immutable, append-only
- Silver: Cleansed, validated, deduplicated
- Gold: Business-level aggregates, dimensional model
- NEVER skip layers - data flows Bronze → Silver → Gold

**Dimensional Modeling** (MANDATORY):
- Star schema with facts and dimensions
- Surrogate keys for dimensions
- Grain explicitly defined for each fact
- Conformed dimensions reused across facts

**Cost Optimization First**:
- Right-size clusters (start small, scale up if needed)
- Use Spot VMs for all non-critical workloads
- Optimize Delta tables (OPTIMIZE, ZORDER, VACUUM)
- Partition strategically
- Move old data to Cool/Archive tiers

**Data Quality by Default**:
- Validate at every layer (Bronze → Silver → Gold)
- Use Great Expectations or custom checks
- Track data quality metrics over time
- Alert on quality degradation
- Quarantine bad data (do not fail pipeline)

**Observability Built-In**:
- Structured logging with trace_id
- Pipeline metrics (throughput, latency, error rate)
- Data freshness tracking
- SLA monitoring
- Comprehensive dashboards

**CI/CD from Day One**:
- Version control all code (Git)
- Automated testing (unit, integration, data quality)
- Databricks Asset Bundles for deployment
- Dev → Staging → Prod promotion
- Infrastructure as Code

---

## WORKFLOW EXECUTION SUMMARY

**Simple Lakehouse (Single Data Source, Basic Reporting)**:
1. Ingest to Bronze (Auto Loader)
2. Transform to Silver (basic cleansing)
3. Create Gold fact table (simple aggregation)
4. Connect Power BI to Gold table
5. **Duration**: 1-2 days

**Complex Lakehouse (Multiple Sources, Dimensional Model, ML)**:
1. Discover all data sources and requirements (Phase 1)
2. Design medallion architecture and dimensional model (Phase 2)
3. Implement Bronze pipelines for all sources (Phase 3)
4. Implement Silver transformations with data quality (Phase 3)
5. Implement Gold dimensional model (Phase 3)
6. Optimize costs (Phase 4)
7. Set up data quality and observability (Phase 5)
8. Build CI/CD pipelines (Phase 6)
9. Configure security and compliance (Phase 7)
10. Create comprehensive documentation (Phase 9)
11. **Duration**: 4-8 weeks

---

Generated using data-lakehouse-builder.md
Last updated: 2025-01-XX
