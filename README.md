**Project Overview -**
MetroCab Logistics, a mid-sized taxi fleet operator in New York City, is currently facing a 12% decline in profitability due to inefficient driver routing. Their drivers spend approximately 40% of their shift driving empty ("deadhead miles") searching for passengers.

The client has retained Gambill Data to build a data pipeline that processes historical trip records to identify high-demand zones and optimal time-slots. The goal is to provide a "Demand Heatmap" and "Driver Efficiency Scorecard" that Operations can use to direct drivers to high-value locations, thereby reducing idle time and increasing revenue per mile.

**Technical Architecture**
We utilized a Lakehouse Architecture (Medallion pattern) adapted for the Databricks Free Edition environment.

**A. Ingestion (Source)**
  Data Source: NYC Taxi & Limousine Commission (TLC) Trip Record Data.
  Accessibility: Pre-loaded in the Databricks datasets repository (/databricks-datasets/nyctaxi).
  Format: Delta / Parquet / CSV (Source files).
  Volume: High volume (Millions of rows available).
  
**B. Processing (Compute & Transformation)**
  Platform: Databricks Free Edition (Community).
  Engine: Apache Spark (PySpark & SparkSQL).
  Orchestration: Notebook Chaining.
  Constraint: The Free Edition does not support the Databricks Jobs Scheduler.
  Solution: You will create a "Master Pipeline" notebook that executes the Bronze, Silver, and Gold notebooks in sequence using the %run command to simulate an orchestrated workflow.
  
**C. Storage (The Medallion Layer)**
  Bronze (Raw): Ingestion of raw trip data.
  Silver (Cleansed): Deduplicated data, type casting (String -> Timestamp), handling nulls, and filtering invalid records (e.g., negative fares).
  Gold (Aggregated): Business-level aggregates (e.g., profitability_by_zone, hourly_demand_metrics).
  Storage Format: Delta Lake (Managed Tables).
  
**D. Serving (Analytics)**
  Visualization: Microsoft Power BI Desktop.
  Integration Method: Data Export (CSV).
  Constraint: The Free Edition does not allow JDBC/ODBC connectivity (DirectQuery).
  Solution: The pipeline must generate final "Report-Ready" CSV files in the Gold layer. You will download these files from DBFS and import them into Power BI.

  
**Implementation Plan**

**Phase 1: Environment Setup & Discovery**
  Objective: Provision the Databricks environment and explore the data schema.
  Task: Spun up a Compute Cluster (1 Driver, 15GB Memory - standard for Free Edition).
  

**Phase 2: The Ingestion Pipeline (Bronze & Silver)**
  Objective: Ingest raw data and apply data quality rules.
  Engineering Challenge: The raw data is messy; therefore, it was handled using different schemas from different years/months.
  Task: Created a Bronze notebook that read from source and wrote to a Delta table.
  Task: Created a Silver notebook.
  
**Phase 3: Business Logic Aggregation (Gold)**
  Objective: Answer the business questions.
  Logic (Zone Performance): Grouped by pickup_zip (or location ID) and hour_of_day. Calculated 
  [avg_revenue_per_mile]([url](https://dbc-38c471d1-00a8.cloud.databricks.com/sql/dashboardsv3/01f11ce819021fffaa6cf47be2e5c144/pages/85a936f1?autoLogin=true&email=mkamin.6696%40gmail.com&o=7474660684945760&utm_campaign=copy_widget_link&fullscreenWidget=85a936f1~pickup_honeypot_map)) 
  and 
  [total_trips]([url](https://dbc-38c471d1-00a8.cloud.databricks.com/sql/dashboardsv3/01f11ce819021fffaa6cf47be2e5c144/pages/85a936f1?autoLogin=true&email=mkamin.6696%40gmail.com&o=7474660684945760&utm_campaign=copy_widget_link&fullscreenWidget=85a936f1~nyc-taxi-ride-counts-weekdays-times-day)).
  
  to locate the hotspots during different days of the week and different parts of the day. The dashboards can be filtered to focus on specific days and times. This way the company can be more efficient by sending drivers to the nearest hotspots at the right time. Having the average fare would also enable the company to provide financial incentives to drivers. 
