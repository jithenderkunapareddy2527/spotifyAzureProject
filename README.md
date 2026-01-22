# spotifyAzureProject

Overview

This project implements an end-to-end Azure-based data engineering pipeline using Azure Data Factory, Azure Data Lake Gen2, and Databricks.
The goal is to ingest Spotify data incrementally, apply structured transformations, and build analytics-ready dimension and fact tables using Delta Live Tables (DLT) with Slowly Changing Dimension (SCD) Type 2 handling.

🏗️ Architecture

Ingestion: Azure Data Factory (ADF)

Storage: Azure Data Lake Gen2 (Bronze / Silver / Gold)

Processing: Azure Databricks (PySpark)

Gold Layer Modeling: Delta Live Tables (DLT)

Version Control: GitHub (ADF + Databricks Asset Bundle)

🔄 Data Ingestion – Azure Data Factory
Incremental & Backfill Logic

The ADF pipeline is fully parameterized and supports both incremental loads and historical backfilling.

Key parameters used:

schema

file

cdc

back_date

Flow Logic

Lookup activity reads the last processed CDC timestamp from metadata.

Set Variable captures the current execution timestamp.

Copy Activity loads only new or updated records using the CDC column.

If Condition:

If new data exists → data is ingested and CDC metadata is updated.

If no new data → pipeline exits safely without reprocessing.

This approach avoids full reloads and ensures idempotent, efficient ingestion.

🥈 Silver Layer – Transformations (Databricks)

In the Silver layer, raw ingested data is cleaned and standardized using PySpark.

Key transformations:

Schema normalization and column renaming

Data type casting

Deduplication using business keys

Handling nulls and invalid records

Enforcing consistent timestamps

The Silver layer serves as a clean, reliable source for downstream analytics and modeling.

🥇 Gold Layer – Delta Live Tables (DLT)

The Gold layer is built using Declarative Delta Live Tables, focusing on analytics-ready dimension and fact tables.

Key Features

Streaming tables for continuous processing

Built-in data quality enforcement

Automatic dependency management

SCD Type 2 Implementation

For dimension tables (e.g. dimuser, dimtrack, dimdate), SCD Type 2 is implemented using DLT CDC APIs.

Highlights:

Tracks historical changes

Maintains effective_from and effective_to

Uses updated_at as the sequencing column

Preserves full change history for analytics

Example use cases:

User attribute changes

Track metadata updates

Slowly changing reference data

📁 Project Structure
spotifyAzureProject/
│
├── dataset/                 # ADF datasets
├── linkedService/           # ADF linked services
├── pipeline/                # ADF pipelines (incremental + backfill)
│
├── spotify_dab/             # Databricks Asset Bundle
│   ├── transformations/     # Silver layer PySpark logic
│   ├── explorations/        # Exploration notebooks
│   ├── utilities/           # Shared utilities
│   └── databricks.yml
│
├── publish_config.json
└── README.md

🚀 Key Learnings & Outcomes

Designed metadata-driven incremental ingestion

Implemented parameterized ADF pipelines with backfill support

Built clean Silver layer transformations

Used Delta Live Tables for scalable Gold layer modeling

Implemented SCD Type 2 for historical tracking

Enabled Git-based version control for both ADF and Databricks

🛠️ Tech Stack

Azure Data Factory

Azure Data Lake Gen2

Azure Databricks

PySpark

Delta Lake

Delta Live Tables (DLT)

GitHub

<img width="1440" height="900" alt="Screenshot 2026-01-22 at 2 51 10 PM" src="https://github.com/user-attachments/assets/630e34c8-6ce9-4fcc-94f6-ea4e330a88d0" />
<img width="1440" height="900" alt="Screenshot 2026-01-22 at 2 08 42 PM" src="https://github.com/user-attachments/assets/db2b92fe-9265-4e0b-85f4-ad1b203b1691" />

