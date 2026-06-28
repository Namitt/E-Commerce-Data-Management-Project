# E-Commerce Data Management Project

An end-to-end data management project that replicates a real-world e-commerce environment for a fictional US university shopping platform. It covers the full lifecycle: database design, synthetic data generation, ingestion with quality assurance, an automated ETL pipeline, and user-behaviour analysis with reporting.

Built with **SQLite** for storage, **R** for data generation and analysis, **GitHub Actions** for CI/automation, and **Quarto** for reporting.

## Overview

The project simulates the e-commerce operations of a university in the United States using data for the year 2023 (with the current date assumed to be 01/01/2024). It models users, products, orders, sellers, deliveries, and shippers, then analyses purchasing behaviour to build a profile of the platform's users.

## Pipeline

1. **Database design** — An Entity-Relationship Diagram (ERD) is translated into a normalised relational schema (3NF) of seven tables.
2. **Synthetic data generation** — Realistic data is generated using a combination of R and Mockaroo, enforcing business logic and temporal consistency.
3. **Data import & QA** — Data is ingested into SQLite with validation, deduplication, and referential integrity checks.
4. **Automated pipeline** — GitHub Actions runs the ETL workflow on each push and on a schedule.
5. **Analysis & reporting** — User behaviour is analysed across product preference, member loyalty, and regional distribution.

## Database Schema

The schema consists of seven tables derived from the ERD:

| Table | Description |
|-------|-------------|
| `user` | User and address details (membership status, location, contact) |
| `product` | Product attributes, pricing, stock, ratings |
| `product_category` | Category names and descriptions |
| `seller` | Seller details and delivery methods |
| `order_details` | Order line items, quantities, status, payment method |
| `delivery` | Delivery type, status, and shipper linkage |
| `shipper` | Shipper company and contact details |

Primary and foreign keys enforce relationships between entities, and `NOT NULL` constraints ensure required fields are populated.

## Data Generation Logic

The synthetic data was generated to reflect realistic conditions, including:

- A 70/30 Prime to non-Prime membership split, roughly mirroring Amazon USA statistics.
- A 5% discount applied to Prime member orders.
- Temporal ordering enforced so that `published_date < order_date < delivery_date`.
- Order and delivery statuses kept consistent with each other and with the temporal data.
- Shipper information assigned only to relevant delivery methods (Express and Standard Shipping).
- A limited set of product categories, reflecting a small university-scale company.

## Data Quality Assurance

During ingestion, each function performs:

- Email format validation
- Filtering of rows with missing required values
- Uniqueness checks on primary keys
- Referential integrity checks (verifying referenced records exist before insert)
- Error handling via `tryCatch` with success/failure feedback per row

## Automation (CI/CD)

A GitHub Actions workflow (`ETL Workflow for Group 20`) runs on Ubuntu and is triggered by pushes to `main` and on a schedule (every 6 hours). It:

- Checks out the code and sets up the R environment
- Caches and installs required R packages
- Loads and validates new data, checking formats and referential integrity
- Updates the analysis with new data
- Commits and pushes changes back to the repository

## Analysis Highlights

**Product preference** — A total of 7,684 products were sold in 2023. Books (43%), Clothing (20%), and Electronics (17%) were the top categories, reflecting strong demand for learning materials and apparel.

**Member loyalty** — Most active users hold memberships, and members contribute the majority of order volume, indicating the membership scheme drives engagement. Non-members spent unusually more in January, attributed to alumni events.

**Regional distribution** — Texas, California, and Florida lead in delivery volume, highlighting priority areas for improving delivery efficiency and offline pickup options.

## Tech Stack

- **Database:** SQLite
- **Language:** R (`dplyr`, `readr`, `RSQLite`, `ggplot2`, `lubridate`, `tidyr`)
- **Data generation:** R + Mockaroo
- **CI/CD:** GitHub Actions
- **Reporting:** Quarto

## Repository Structure

```
.
├── .github/workflows/      # GitHub Actions ETL workflow
├── R/                      # R scripts (table creation, loading, validation)
├── Synthetic_Data_Generation/  # Data generation scripts and CSV tables
├── Data_Analysis/          # Analysis scripts and Quarto reports
├── data_upload/            # Input datasets
├── database/               # SQLite database file
└── project.Rproj           # RStudio project file
```

## Getting Started

1. Clone the repository.
2. Open `project.Rproj` in RStudio.
3. Install the required packages:
   ```r
   install.packages(c("ggplot2", "dplyr", "readr", "tidyr", "RSQLite", "DBI", "lubridate"))
   ```
4. Run the table creation, data loading, and validation scripts in the `R/` directory.
5. Run the analysis scripts in `Data_Analysis/` to reproduce the figures.

## Notes

This was a university group project. All data is synthetic and generated for educational purposes; no real customer data is used.
