# dubai-real-estate-project
# Dubai Real Estate End-to-End Analytics Pipeline

An enterprise-grade, automated data engineering pipeline built on **Azure** and **Databricks** utilizing the **Medallion Architecture**. This system ingests, cleans, aggregates, and visualizes multi-gigabyte transactional real estate records, turning raw logs into an interactive executive market dashboard.

## 📊 Executive Dashboard Analytics
The final analytical layer serves business intelligence insights natively within Databricks:
* **Market Trends (Line Chart):** Evaluates macro-economic trends and seasonal fluctuations of total transaction volumes (`total_sales_aed`) across historical months.
* **Supply Inventory (Donut Chart):** Highlights property distribution metrics broken down by unit classifications and bedroom configurations.
* **Top Hotspots (Horizontal Bar Chart):** Pinpoints geographic performance metrics, tracking elite real estate volume hubs (e.g., Burj Khalifa, Al Barsha South Fourth, Business Bay).

---

## 🏗️ System Architecture & Data Flow
The project rigorously implements a structured Medallion Architecture data pipeline to guarantee data quality and lineage control.

```text
+------------------+      +-------------------+      +------------------+      +------------------+
| Raw Data Sources | ---> |    Bronze Layer   | ---> |   Silver Layer   | ---> |    Gold Layer    |
| (Transactions/   |      | (Append-Only/Raw  |      | (Cleaned, Typed, |      | (Aggregated Data/|
|    Units CSV)    |      |  History Snapshots|      | Enriched Delta)  |      | BI-Ready Tables) |
+------------------+      +-------------------+      +------------------+      +------------------+
                                                                                    |
                                                                                    v
                                                                          +------------------+
                                                                          | Power BI / Direct|
                                                                          | SQL Dashboard    |
                                                                          +------------------+

### 🗂️ Medallion Breakdown
1. **Landing Zone & Archival Logic:** Raw `.csv` transaction and unit dumps are ingested into an Azure Data Lake Storage (ADLS Gen2) container. Automated Python processing manages historical run states by clearing and archiving old runs to a secure directory using timestamped schemas to prevent destination collisions.
2. **Bronze Layer (`1_ingest_transactions`, `2_ingest_dimensions`):** Direct schema-defined snapshot ingest capturing raw state files into unified, highly performant Delta tables.
3. **Silver Layer (`3_transform_silver`):** Cleans and conforms the data. This stage resolves type casting mismatch constraints, handles structural null values, enforces data compliance rules, and matches primary keys across dimensional unit details and transactions.
4. **Gold Layer (`4_transform_gold`):** Performs business-logic rollups and calculations (e.g., calculating running averages for price per square foot, grouping transactional metrics by region and date strings). Output files are written directly as optimized Delta files into dedicated Azure Cloud target containers.
5. **BI Bridge Layer (`5_refresh_bridge`):** Connects physical Delta Lake storage directly to the Databricks Metastore and Data Catalog, optimizing indexing schemas so external reporting frameworks can execute ultra-low-latency query calls.

---

## 🛠️ Technology Stack
* **Cloud Infrastructure:** Azure Data Lake Storage Gen2 (ADLS), Azure Active Directory (Service Principal Authentication)
* **Unified Analytics Engine:** Azure Databricks (Single-Node Spark Clusters Engine)
* **Storage Framework:** Delta Lake (ACID Transactions, Schema Enforcement, Time-Travel Capabilities)
* **Languages & API Layer:** PySpark (Spark SQL & DataFrames), Native Databricks SQL
* **Orchestration:** Databricks Workflows (Multistage Job Graph Execution)

---

## 📈 Automated Workflow Orchestration
Pipeline health is completely automated using integrated Databricks Task Graphs. If upstream jobs fail or detect structural system variations, down-stream transformations gracefully halt execution to prevent data corruption. 

* *Pipeline Status:* Fully Operational & Verified Green

---

## 🚀 Step-by-Step Installation & Setup

### 1. Azure Storage Configuration
Configure an ADLS Gen2 storage account and establish hierarchical structures for your storage tiers:
```bash
abfss://landing@adlsdubaiishitaa.dfs.core.windows.net/
abfss://bronze@adlsdubaiishitaa.dfs.core.windows.net/
abfss://silver@adlsdubaiishitaa.dfs.core.windows.net/
abfss://gold@adlsdubaiishitaa.dfs.core.windows.net/
