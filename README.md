# 🏗️ SQL Data Warehouse Project

![Status](https://img.shields.io/badge/status-completed-brightgreen)
![SQL Server](https://img.shields.io/badge/SQL%20Server-T--SQL-CC2927?logo=microsoftsqlserver&logoColor=white)
![Architecture](https://img.shields.io/badge/architecture-Medallion%20(Bronze%2FSilver%2FGold)-orange)
![License](https://img.shields.io/badge/license-MIT-blue)

> A modern data warehouse built on **SQL Server**, implementing the **Medallion Architecture** (Bronze → Silver → Gold) to integrate ERP and CRM source data into a clean, business-ready **star schema** for analytics and reporting.

---

## 📑 Table of Contents

- [Overview](#-overview)
- [High-Level Architecture](#-high-level-architecture)
- [Data Flow (Lineage)](#-data-flow-lineage)
- [Data Model — Star Schema](#-data-model--star-schema)
- [Repository Structure](#-repository-structure)
- [Naming Conventions](#-naming-conventions)
- [Data Catalog](#-data-catalog)
- [Project Requirements](#-project-requirements)
- [How to Use This Project](#-how-to-use-this-project)
- [Tools Used](#-tools-used)
- [License](#-license)

---

## 📊 Overview

This project builds a complete data warehouse from two source systems — **CRM** and **ERP** — flat files delivered as CSVs. Data moves through three progressively refined layers:

| Layer | Purpose |
|---|---|
| 🥉 **Bronze** | Raw, unprocessed data loaded as-is from source files — full traceability and debugging |
| 🥈 **Silver** | Cleaned, standardized, and enriched data — ready for analysis |
| 🥇 **Gold** | Business-ready data, modeled as views in a **star schema** — consumed directly by BI tools, ad-hoc SQL, and ML |

The end deliverable is a queryable Gold layer (`dim_customers`, `dim_products`, `fact_sales`) that powers reporting, dashboards, and analytics.

---

## 🏛️ High-Level Architecture

| Layer | Object Type | Load Method | Transformations | Data Model | Audience |
|---|---|---|---|---|---|
| **Bronze** | Tables | Full Load (Truncate & Insert) | None (as-is) | None (as-is) | Data Engineers |
| **Silver** | Tables | Full Load (Truncate & Insert) | Cleansing, Standardization, Normalization, Derived Columns, Enrichment | None (as-is) | Data Analysts, Data Engineers |
| **Gold** | Views | None | Data Integration, Aggregation, Business Logic & Rules | Star Schema, Flat Tables, Aggregated Tables | Data Analysts, Business Users |

Sources (CRM, ERP — CSV files) land in SQL Server, flow through Bronze → Silver → Gold, and are consumed by **BI & Reporting**, **Ad-Hoc SQL Queries**, and **Machine Learning** workloads.

---

## 🔄 Data Flow (Lineage)

| Source | Bronze Table | Silver Table | Gold Object |
|---|---|---|---|
| CRM | `crm_sales_details` | `crm_sales_details` | → `fact_sales` |
| CRM | `crm_cust_info` | `crm_cust_info` | → `dim_customers` |
| CRM | `crm_prd_info` | `crm_prd_info` | → `dim_products` |
| ERP | `erp_cust_az12` | `erp_cust_az12` | → `dim_customers` |
| ERP | `erp_loc_a101` | `erp_loc_a101` | → `dim_customers` |
| ERP | `erp_px_cat_g1v2` | `erp_px_cat_g1v2` | → `dim_products` |

---

## ⭐ Data Model — Star Schema

![Data Model](docs/data_model.png)

The Gold layer exposes a classic star schema:

- **`gold.fact_sales`** (fact table) — order-level transactions, linked via `product_key` and `customer_key`
- **`gold.dim_customers`** (dimension) — customer demographic & geographic attributes
- **`gold.dim_products`** (dimension) — product attributes, category, cost, product line

> **Sales Calculation:** `sales_amount = quantity * price`

---

## 📂 Repository Structure

```
sql-data-warehouse-project/
├── datasets/                      # Raw source datasets (CRM & ERP CSV files)
├── docs/                          # Project documentation & diagrams
│   ├── data_architecture.png      # High-level architecture diagram
│   ├── data_flow.png              # Data flow / lineage diagram
│   ├── data_model.png             # Star schema diagram
│   ├── data_layers.pdf            # Bronze/Silver/Gold layer comparison & workflow
│   ├── naming_conventions.md      # Naming rules for schemas, tables, columns
│   └── data_catalog.md            # Gold layer data dictionary
├── scripts/                       # ETL SQL scripts
│   ├── bronze/                    # Load raw data (load_bronze)
│   ├── silver/                    # Clean & transform data (load_silver)
│   └── gold/                      # Build business views (dim_*, fact_*)
├── tests/                         # Data quality & validation scripts
├── README.md                      # You are here
└── LICENSE
```

---

## 📐 Naming Conventions

Full reference: [`docs/naming_conventions.md`](docs/naming_conventions.md)

| Object | Pattern | Example |
|---|---|---|
| Bronze / Silver tables | `<sourcesystem>_<entity>` | `crm_customer_info` |
| Gold tables | `<category>_<entity>` | `dim_customers`, `fact_sales` |
| Surrogate keys | `<table_name>_key` | `customer_key` |
| Technical/metadata columns | `dwh_<column_name>` | `dwh_load_date` |
| Load stored procedures | `load_<layer>` | `load_bronze`, `load_silver` |

General rules: `snake_case`, English only, no SQL reserved words.

---

## 📖 Data Catalog

Full reference: [`docs/data_catalog.md`](docs/data_catalog.md)

**`gold.dim_customers`** — customer_key, customer_id, customer_number, first_name, last_name, country, marital_status, gender, birthdate, create_date

**`gold.dim_products`** — product_key, product_id, product_number, product_name, category_id, category, subcategory, maintenance_required, cost, product_line, start_date

**`gold.fact_sales`** — order_number, product_key, customer_key, order_date, shipping_date, due_date, sales_amount, quantity, price



## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **SQL Server / SSMS** | Data warehouse engine & query development |
| **T-SQL** | ETL scripting (stored procedures per layer) |
| **Git / GitHub** | Version control & documentation |

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

⭐ If this project was useful, consider starring the repo!

