# 🎵 Spotify Azure Data Pipeline

> End-to-end cloud data engineering pipeline — incremental ingestion, structured transformation, and analytics-ready modeling using Azure & Databricks.

----

## 📐 Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                      DATA FLOW                                  │
│                                                                 │
│  Spotify API                                                    │
│      │                                                          │
│      ▼                                                          │
│  ┌─────────────────────┐                                        │
│  │  Azure Data Factory │  ← Incremental + Backfill CDC Logic    │
│  └─────────┬───────────┘                                        │
│            │                                                    │
│            ▼                                                    │
│  ┌───────────────────────────────────────────┐                  │
│  │        Azure Data Lake Gen2               │                  │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐  │                  │
│  │  │  BRONZE  │ │  SILVER  │ │   GOLD   │  │                  │
│  │  │   Raw    │→│  Clean   │→│Analytics │  │                  │
│  │  └──────────┘ └──────────┘ └──────────┘  │                  │
│  └───────────────────────────────────────────┘                  │
│                        │                                        │
│                        ▼                                        │
│  ┌─────────────────────────────────────┐                        │
│  │         Azure Databricks            │                        │
│  │   PySpark Transformations + DLT     │                        │
│  │   SCD Type 2 Dimension Modeling     │                        │
│  └─────────────────────────────────────┘                        │
│                        │                                        │
│                        ▼                                        │
│              GitHub (Version Control)                           │
│          ADF Pipelines + Databricks Asset Bundle                │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Component | Technology |
|---|---|
| Orchestration | Azure Data Factory |
| Storage | Azure Data Lake Gen2 |
| Processing | Azure Databricks + PySpark |
| Table Format | Delta Lake |
| Gold Modeling | Delta Live Tables (DLT) |
| Version Control | GitHub |

---

## 🔄 Data Ingestion — Azure Data Factory

The ADF pipeline is **fully parameterized** and supports both incremental loads and historical backfills.

### Key Parameters

| Parameter | Purpose |
|---|---|
| `schema` | Target schema name |
| `file` | Source file identifier |
| `cdc` | Change Data Capture timestamp column |
| `back_date` | Historical backfill start date |

### Pipeline Flow

```
Lookup (last CDC timestamp)
        │
        ▼
Set Variable (current execution timestamp)
        │
        ▼
Copy Activity (load only new/updated records via CDC)
        │
        ▼
If Condition ──► New data found → Ingest + Update CDC metadata
        │
        └──► No new data → Exit safely (no reprocessing)
```

> ✅ This design is **idempotent** — safe to re-run without duplicating data.

---

## 🥈 Silver Layer — Transformations

Raw ingested data is **cleaned and standardized** in the Silver layer using PySpark.

**Transformations applied:**
- Schema normalization and column renaming
- Data type casting
- Deduplication using business keys
- Null and invalid record handling
- Consistent timestamp enforcement

The Silver layer is the single reliable source of truth for downstream modeling.

---

## 🥇 Gold Layer — Delta Live Tables (DLT)

Analytics-ready **dimension and fact tables** are built declaratively using Delta Live Tables.

### Features
- Streaming tables for continuous processing
- Built-in data quality enforcement via expectations
- Automatic dependency resolution between tables

### SCD Type 2 Implementation

Historical changes are tracked using DLT's CDC API for all dimension tables.

| Dimension | Description |
|---|---|
| `dimuser` | User attribute history |
| `dimtrack` | Track metadata changes |
| `dimdate` | Calendar/reference dimension |

**SCD Type 2 Mechanics:**

```
Sequencing column : updated_at
Tracks            : effective_from / effective_to
Behavior          : Full change history preserved for analytics
```

---

## 📁 Project Structure

```
spotifyAzureProject/
│
├── dataset/                  # ADF dataset definitions
├── linkedService/            # ADF linked service configs
├── pipeline/                 # ADF pipelines (incremental + backfill)
│
├── spotify_dab/              # Databricks Asset Bundle
│   ├── transformations/      # Silver layer PySpark logic
│   ├── explorations/         # Exploratory notebooks
│   ├── utilities/            # Shared helper utilities
│   └── databricks.yml        # Bundle configuration
│
├── publish_config.json
└── README.md
```

---

## 🚀 Key Learnings & Outcomes

- ✅ Metadata-driven incremental ingestion with CDC
- ✅ Parameterized ADF pipelines with backfill support
- ✅ Clean Silver layer transformation framework
- ✅ Declarative Gold layer modeling with DLT
- ✅ SCD Type 2 for full historical dimension tracking
- ✅ Git-based version control for ADF + Databricks

---

## 📸 Pipeline Screenshots

**ADF Pipeline — Full View**

![ADF Pipeline Full View](https://github.com/user-attachments/assets/630e34c8-6ce9-4fcc-94f6-ea4e330a88d0)

**ADF Pipeline — Detail View**

![ADF Pipeline Detail View](https://github.com/user-attachments/assets/db2b92fe-9265-4e0b-85f4-ad1b203b1691)

---

<div align="center">
  <sub>Built with Azure Data Factory · Databricks · Delta Live Tables</sub>
</div>
