# End-to-End-Azure-Pipeline-ADF-Databricks-ADLS-Gen2-Synapse-Power-BI
Data Engineering Pipeline: Medallion Architecture with Databricks & Azure Synapse
This repository contains the end-to-end data engineering pipeline implementing a Medallion Architecture (Bronze, Silver, Gold) using Microsoft Azure services and Databricks. The pipeline ingests incremental raw data, transforms it into clean analytical datasets, structures it into a star schema, and delivers it to downstream analytical warehouses and reporting tools.

![Project Architecture](https://github.com/Maccha-Ankit/End-to-End-Azure-Pipeline-ADF-Databricks-ADLS-Gen2-Synapse-Power-BI/raw/471876a0db42747a6689fd8287e9f2ad67788110/project_architecture.png)

Data Engineering Pipeline: Medallion Architecture with Databricks & Azure Synapse
This repository contains the end-to-end data engineering pipeline implementing a Medallion Architecture (Bronze, Silver, Gold) using Microsoft Azure services and Databricks. The pipeline ingests incremental raw data, transforms it into clean analytical datasets, structures it into a star schema, and delivers it to downstream analytical warehouses and reporting tools.

🏗️ Architecture Overview
The pipeline follows a modern data lakehouse design pattern, structured as follows:

[Data Sources] ➔ [ADF / Databricks Ingestion] ➔ [Bronze (Raw)] ➔ [Silver (Transformed)] ➔ [Gold (Star Schema)] ➔ [Synapse / Power BI]
1. Ingestion & Orchestration
Data Sources: Raw files and structural data sources.

Azure Data Factory (ADF): Orchestrates the data pipelines, schedules ingestion, and triggers Databricks notebooks.

Databricks: Used for high-performance data processing, streaming ingestion, and heavy transformations.

CI/CD & Version Control: GitHub manages code versioning, collaboration, and deployment pipelines.

2. Storage Tier: Medallion Architecture (Azure Data Lake Storage Gen2)
🥉 Bronze Layer (Incremental Data): Houses the Raw Data Store. Data is ingested as-is from source systems with appended metadata (e.g., ingestion timestamps).

🥈 Silver Layer (Transformation): Stores Transformed Data. This layer performs data cleaning, enrichment, schema enforcement, deduplication, and standardization.

🥇 Gold Layer (STAR SCHEMA): The Serving layer. Data is structured into business-level facts and dimensions (Star Schema) optimized for analytical queries.

3. Consumption & Presentation
Warehouse: Azure Synapse Analytics acts as the enterprise data warehouse, loading data from the Gold layer for high-concurrency relational querying.

Reporting: Power BI connects directly to the gold layer or Synapse warehouse to serve business intelligence dashboards and operational reports.

4. Cross-Cutting Concerns
Security & Governance: Integrated using Azure Key Vault (for secure credential management) and Microsoft Entra ID / Unity Catalog for access control.


Before deploying the pipeline, ensure you have access to and configuration rights for:

An active Azure Subscription.

Azure Data Lake Storage (ADLS) Gen2 account with Storage Blob Data Contributor rights.

Azure Databricks Workspace (Premium tier recommended for Unity Catalog/Security).

Azure Data Factory V2 instance.

Azure Synapse Analytics SQL Pool.

GitHub repository linked to your ADF and Databricks workspaces.

Environment Setup
1. Security & Secrets Management
Create an Azure Key Vault.

Add your service principal credentials, storage account keys, and Synapse connection strings as secrets.

In Azure Databricks, create a Secret Scope backed by your Azure Key Vault:

Bash
databricks secrets create-scope --scope azure-key-vault --scope-backend-type AZURE_KEYVAULT --resource-id <key-vault-resource-id> --dns-name <key-vault-dns-uri>
2. Mounting Storage in Databricks (or configuring ABFSS)
Configure access to your Delta Lake storage layers using the Azure Blob File System (ABFSS) driver inside your notebooks:

Python
spark.conf.set(f"fs.azure.account.key.{storage_account_name}.dfs.core.windows.net", 
               dbutils.secrets.get(scope="azure-key-vault", key="storage-account-key"))
⚙️ Pipeline Execution Flow
The pipeline runs sequentially, managed automatically by Azure Data Factory:

Ingest: ADF triggers data movement, landing files into /mnt/bronze/raw_data_store/.

Refine: The 02_transform_silver Databricks notebook runs, validating fields and parsing nested structures, writing output to /mnt/silver/transformed_data/ in Delta format.

Model: The 03_serving_gold notebook processes the Silver Delta tables into multi-dimensional Fact and Dimension tables saved in /mnt/gold/star_schema/.

Load & Visualize: ADF instructs Synapse to refresh its relational tables, triggering an automated visual refresh in the targeted Power BI dashboards.

🛠️ Development & Deployment (CI/CD)
Feature Branching: Developers create feature branches from main.

ADF & Notebook Sync: Local modifications to Databricks notebooks or ADF pipelines are synced via native GitHub integrations.

Automated Testing: Pull Requests trigger GitHub Actions workflows to validate template syntaxes.

Release: Merging into main automatically promotes and deploys code artifacts to the production environments.
