# The Universal Core: SOA-C03 + DVA-C02 + DEA-C01 Overlap

To succeed in any of these three associate-level certifications, you must have a deep, foundational understanding of the following AWS services. They form the backbone of compute, storage, security, and observability across the entire AWS ecosystem.

## 🪣 1. Amazon S3 (Simple Storage Service)
S3 is arguably the most important overlapping service in AWS. 
* **Developer (DVA-C02):** Uses S3 to host static website assets, store application uploads, and manage application configuration files.
* **SysOps (SOA-C03):** Manages S3 bucket policies, lifecycle rules (moving data to Glacier), cross-region replication, and backup strategies.
* **Data Engineer (DEA-C01):** S3 is the absolute foundation of the **Data Lake**. Data Engineers use it to land raw data, store transformed Parquet files, and query data directly using Amazon Athena.

## 🔐 2. AWS IAM (Identity and Access Management)
Security is Job Zero on AWS. You must understand IAM policies, roles, and least privilege for all three exams.
* **Developer:** Focuses on creating **Execution Roles** (e.g., giving a Lambda function permission to read from a specific S3 bucket) and using the AWS SDK securely without hardcoding credentials.
* **SysOps:** Focuses on managing users, groups, federated access (IAM Identity Center), and cross-account access via STS (Security Token Service).
* **Data Engineer:** Focuses on granular data access controls—ensuring that only specific users or analytics tools can read sensitive PII data in the data lake (often integrating with AWS Lake Formation).

## ⚡ 3. AWS Lambda
Serverless compute is no longer just for developers; it is heavily utilized in operations and data processing.
* **Developer:** Writes the actual business logic inside Lambda, manages versions, aliases, and API Gateway integrations.
* **SysOps:** Uses Lambda for **Event-Driven Operations** (e.g., automatically triggering a Lambda function to terminate an unapproved EC2 instance when a CloudTrail event is detected).
* **Data Engineer:** Uses Lambda for lightweight, event-driven ETL (Extract, Transform, Load). For example, triggering a Lambda function to clean a CSV file the moment it is uploaded to S3.

## 📊 4. Amazon CloudWatch & AWS CloudTrail
Observability and auditing are mandatory across all disciplines.
* **Developer:** Uses CloudWatch Logs to debug application errors and uses X-Ray for tracing distributed serverless microservices.
* **SysOps:** Builds CloudWatch Dashboards, sets up metric alarms (e.g., CPU utilization > 80%), and uses CloudTrail to audit "who did what" in the AWS account.
* **Data Engineer:** Uses CloudWatch to monitor data pipeline health (e.g., did the AWS Glue job fail?) and set up event rules to trigger downstream data processes.

## 🛡️ 5. AWS KMS (Key Management Service)
Data protection and encryption at rest are heavily tested on all three exams.
* **Developer:** Uses KMS via the AWS SDK to encrypt/decrypt sensitive application data (like API tokens) before saving them to a database.
* **SysOps:** Manages Customer Managed Keys (CMKs), key rotation policies, and enforces encryption on EBS volumes and S3 buckets.
* **Data Engineer:** Ensures that all data at rest in the Data Lake, Redshift data warehouses, and Kinesis data streams is encrypted using KMS.

## 🗄️ 6. Core Databases (Amazon RDS & DynamoDB)
While the Data Engineer focuses more heavily on analytics (Redshift, Athena), foundational operational databases are shared.
* **Developer:** Designs DynamoDB partition keys for fast lookups and writes CRUD operations for RDS.
* **SysOps:** Manages RDS automated backups, Multi-AZ deployments for high availability, and monitors database CPU/Storage metrics.
* **Data Engineer:** Uses tools like AWS Database Migration Service (DMS) or AWS Glue to extract operational data *out* of RDS and DynamoDB to move it into the Data Lake for analytics.