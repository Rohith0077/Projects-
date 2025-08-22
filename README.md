# 📊 YouTube Data Analysis on AWS

## 📌 Project Description

This project analyzes **YouTube trending videos** using **AWS services** such as **S3, Glue, Athena, QuickSight, and Lambda**. The dataset consists of trending videos from multiple regions, helping derive insights like **top trending channels, most liked videos, and user engagement trends**.

The ETL pipeline is automated with **AWS Lambda**, which triggers a Glue job whenever a new dataset is uploaded to S3. The transformed data is then stored in an optimized format, queried using Athena, and visualized in QuickSight.

---

## 🏭 Architecture Diagram

```
YouTube Data (CSV/JSON) ➔ S3 (Raw Storage) ➔ Lambda (Trigger) ➔ Glue (ETL Processing) ➔ S3 (Processed Data) ➔ Athena (Query Engine) ➔ QuickSight (Visualization)
```

---

## 🛠 Tech Stack

- **Amazon S3** - Data storage for raw and processed datasets.
- **AWS Lambda** - Triggers Glue job when a new file is uploaded to S3.
- **AWS Glue** - ETL pipeline to clean and transform data.
- **Amazon Athena** - SQL queries on transformed data.
- **Amazon QuickSight** - Data visualization and dashboard creation.

---

## 📼 Project Structure

```
📆 youtube-analysis-aws
 ┣ 📂 lambda_function
 ┃ ┣ 📄 lambda_trigger.py    # Triggers Glue Job on S3 upload
 ┣ 📂 glue_scripts
 ┃ ┣ 📄 youtube_etl.py       # Glue script for ETL processing
 ┣ 📂 sql_queries
 ┃ ┣ 📄 athena_queries.sql   # Queries for Athena
 ┣ 📂 dashboard_screenshots  # QuickSight visualizations
 ┣ 📄 README.md              # Project documentation
 ┣ 📄 requirements.txt        # Dependencies (if needed)
```

---

## 🚀 Setup Instructions

### 1️⃣ **S3 Bucket Setup**

- Create an **S3 bucket** (e.g., `youtube-analysis-data`).
- Upload the **raw YouTube dataset** (`.csv` or `.json` format).

### 2️⃣ **AWS Lambda Function**

- Create a **Lambda function** to trigger AWS Glue when a new file is uploaded to S3.
- Sample Python code for Lambda:

```python
import boto3

def lambda_handler(event, context):
    glue = boto3.client('glue')
    response = glue.start_job_run(JobName='youtube-etl-job')
    return response
```

### 3️⃣ **AWS Glue ETL Job**

- Create an AWS Glue **Crawler** to catalog the S3 dataset.
- Write a Glue ETL script (`youtube_etl.py`) to clean and transform the data.
- Sample **ETL Script**:

```python
import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job

args = getResolvedOptions(sys.argv, ['JOB_NAME'])
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session
job = Job(glueContext)
job.init(args['JOB_NAME'], args)

# Read data from S3
datasource0 = glueContext.create_dynamic_frame.from_catalog(
    database="youtube_raw_db",
    table_name="raw_statistics",
    transformation_ctx="datasource0"
)

# Data Cleaning and Transformation
applymapping1 = ApplyMapping.apply(frame=datasource0, mappings=[
    ("video_id", "string", "video_id", "string"),
    ("title", "string", "title", "string"),
    ("views", "long", "views", "long"),
    ("likes", "long", "likes", "long"),
    ("dislikes", "long", "dislikes", "long"),
    ("comment_count", "long", "comment_count", "long"),
    ("region", "string", "region", "string")
], transformation_ctx="applymapping1")

# Store transformed data back in S3
datasink4 = glueContext.write_dynamic_frame.from_options(
    frame=applymapping1,
    connection_type="s3",
    connection_options={"path": "s3://youtube-analysis-data/processed/", "partitionKeys": ["region"]},
    format="parquet",
    transformation_ctx="datasink4"
)

job.commit()
```

### 4️⃣ **Amazon Athena Queries**

- Create an **Athena table** using the cleaned dataset.
- Sample SQL query for most liked videos:

```sql
SELECT title, likes FROM youtube_data ORDER BY likes DESC LIMIT 10;
```

### 5️⃣ **Amazon QuickSight Dashboard**

- Connect Amazon QuickSight to Athena.
- Create dashboards for **most trending videos, engagement trends, and regional trends**.

---


## 📊 Example Insights

- 🎥 **Top Trending YouTube Channels**
- 🔥 **Most Liked & Disliked Videos**
- 📈 **Viewership Trends Across Regions**
- 💬 **Comment Engagement Insights**

---


👥 Contributions Welcome!





