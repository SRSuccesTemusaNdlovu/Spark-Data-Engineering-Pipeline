# 🛠️ Circuit ETL Pipeline with Apache Spark

This project showcases a simple yet effective **Data Engineering ETL pipeline** using **Apache Spark** on **Microsoft Fabric**. It demonstrates how to load raw CSV data from a lakehouse, transform it using PySpark, and write the processed data to a new location in Parquet format.

---

## 🔍 Objective

To build a structured pipeline that:

- Ingests raw circuit data (CSV format) from a **Data Lake (OneLake)**.
- Applies schema enforcement and selects specific fields.
- Renames columns and adds an ingestion timestamp.
- Writes the transformed data into a **Processed zone** as **Parquet files**.

---

## 🗂️ Project Structure

```
circuit-etl-spark/
│
├── notebooks/
│   └── circuits_etl_pipeline.ipynb   # Main ETL notebook
│
├── assets/
│   └── pipeline_diagram.png          # Visual pipeline overview
│
└── README.md                         # Project documentation

```

### 💻 Technologies Used
```
Apache Spark (PySpark)

Microsoft Fabric (Lakehouse)

Azure OneLake (abfss)

Parquet (columnar storage)

Python 3.x
```

### 📁 Data Source
```
Raw Data Location:

abfss://projects@onelake.dfs.fabric.microsoft.com/LakehouseTraining.Lakehouse/Files/circuits.csv
Processed Data Output:

abfss://projects@onelake.dfs.fabric.microsoft.com/LakehouseTraining.Lakehouse/Files/processed
```
### 🔄 ETL Flow Overview
```
graph TD
A[Read CSV from Raw Layer] --> B[Apply Schema]
B --> C[Select Required Columns]
C --> D[Rename Columns]
D --> E[Add Ingestion Timestamp]
E --> F[Write to Processed Layer (Parquet)]
```
### 📸 Or view the image below:

![ETL Flow Diagram](https://raw.githubusercontent.com/SRSuccesTemusaNdlovu/Spark-Data-Engineering-Pipeline/main/assets/pipeline_diagram.png)


### 🧪 Sample Code Snippet

```
from pyspark.sql.types import StructType, StructField, IntegerType, StringType, DoubleType
from pyspark.sql.functions import col, current_timestamp

# Define schema
circuits_schema = StructType([
    StructField("circuitId", IntegerType(), False),
    StructField("circuitRef", StringType(), True),
    StructField("name", StringType(), True),
    StructField("location", StringType(), True),
    StructField("country", StringType(), True),
    StructField("lat", DoubleType(), True),
    StructField("lng", DoubleType(), True),
    StructField("alt", IntegerType(), True),
    StructField("url", StringType(), True)
])

# Load CSV with schema
circuits_df = spark.read.format("csv") \
    .option("header", "true") \
    .schema(circuits_schema) \
    .load("abfss://projects@onelake.dfs.fabric.microsoft.com/LakehouseTraining.Lakehouse/Files/circuits.csv")

# Select and rename
selected_df = circuits_df.select("circuitId", "circuitRef", "name", "location", "country", "lat", "lng", "alt")
renamed_df = selected_df.withColumnRenamed("circuitId", "circuit_id") \
                        .withColumnRenamed("circuitRef", "circuit_ref") \
                        .withColumnRenamed("lat", "latitude") \
                        .withColumnRenamed("lng", "longitude") \
                        .withColumnRenamed("alt", "altitude")

# Add timestamp
final_df = renamed_df.withColumn("ingestion_date", current_timestamp())

# Write to processed layer
final_df.write.mode("overwrite").parquet(
    "abfss://projects@onelake.dfs.fabric.microsoft.com/LakehouseTraining.Lakehouse/Files/processed"
)

```

### 📦 Output
```
The processed data is stored in Parquet format and can now be used for:

Data analysis

Reporting

Machine Learning pipelines

Dashboarding in Power BI
```

### ✅ Skills Demonstrated
```
Schema enforcement in PySpark

Column transformation and data typing

Timestamp-based ingestion tracking

Writing to Azure Data Lake with abfss

Basic ETL pipeline architecture in Data Engineering
```
### 📌 License
```
MIT License. Feel free to use, share, and contribute!
```
### 🙌 Acknowledgements
```
Built as part of my learning journey into Data Engineering using Apache Spark on Microsoft Fabric. Inspired by the need to structure and automate data preparation in a scalable manner
```
