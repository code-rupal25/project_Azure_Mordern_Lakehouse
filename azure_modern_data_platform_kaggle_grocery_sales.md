# Azure Modern Data Platform – Grocery Sales (Kaggle)

This documentation set supports an **end-to-end Azure Data Engineering interview project** built using the Kaggle *Grocery Sales Dataset*. The goal is to demonstrate ingestion, transformation, modeling, and analytics using **Modern Data Warehousing + Lakehouse (Medallion Architecture)** principles.

---

## 📄 architecture.md

### 1. Objective
Build a scalable, production-style **Azure Modern Data Platform** that ingests grocery sales data, processes it using Medallion Architecture, stores curated data in a cloud data warehouse, and serves analytics to Power BI.

This project mimics a **retail analytics platform** used by grocery chains to analyze:
- Sales performance
- Product/category trends
- Customer behavior
- City & country-wise revenue

---

### 2. High-Level Architecture Flow

**Source → Ingestion → Lakehouse → Warehouse → BI**

```
Kaggle CSV Files
   ↓
Azure Data Factory (Pipelines)
   ↓
Azure Data Lake Gen2
   - Bronze (Raw CSV)
   - Silver (Cleaned Delta)
   - Gold (Aggregated Delta)
   ↓
Azure Synapse Dedicated SQL Pool
   ↓
Power BI Dashboards
```

---

### 3. Azure Components Used

| Layer | Azure Service | Purpose |
|-----|-------------|--------|
| Ingestion | Azure Data Factory | Orchestration & data movement |
| Storage | ADLS Gen2 | Central data lake |
| Processing | Spark (PySpark) | Data transformations |
| Lakehouse | Delta Lake | ACID + schema enforcement |
| Warehouse | Synapse Dedicated Pool | BI-optimized analytics |
| Visualization | Power BI | Business reporting |

---

### 4. Medallion Architecture Design

- **Bronze Layer**
  - Raw CSV ingestion
  - Append-only
  - No transformations

- **Silver Layer**
  - Type casting
  - Deduplication
  - Foreign key validation
  - Delta format

- **Gold Layer**
  - Star schema
  - Aggregated metrics
  - Business-ready datasets

---

### 5. Security & Governance (Conceptual)
- Role-based access (RBAC)
- Read-only access for BI users
- Separation of raw vs curated data

---

## 📄 assumptions.md

### 1. Business Assumptions

- Dataset represents **one grocery retail company** operating in multiple cities and countries
- Sales are generated daily
- Each sale has one product (no basket-level modeling)
- Discounts are applied at transaction level

---

### 2. Data Assumptions

- CSV files are periodically updated (daily batch)
- `SalesDate` is used for incremental loading
- Primary keys are unique within each file
- Foreign keys may contain invalid references (handled in Silver layer)

---

### 3. Technical Assumptions

- Initial load is full load
- Subsequent loads are incremental
- Late-arriving data is possible (handled via MERGE)
- Timezone is consistent across all files

---

### 4. Performance Assumptions

- Data volume is small initially but designed to scale
- Partitioning by `SalesDate` in Silver & Gold layers
- Synapse Dedicated Pool used to support concurrent BI users

---

### 5. Interview-Oriented Assumptions

- CI/CD concepts are explained but not fully implemented
- Monitoring is demonstrated via logs and pipeline runs
- Cost optimization is discussed conceptually

---

## 📄 dataset-description.md

### 1. Dataset Source

**Dataset:** Grocery Sales Dataset
**Source:** Kaggle
**Domain:** Retail / FMCG Analytics

This dataset simulates a transactional retail system with customers, products, employees, and geographic hierarchy.

---

### 2. Entity Relationship Overview

```
Country → City → Customer
        → Employee

Category → Product → Sales
Customer → Sales
Employee → Sales
```

---

### 3. Table-Level Description

#### categories.csv
Defines the product categories.

| Column | Description |
|------|------------|
| CategoryID | Unique category identifier (PK) |

---

#### countries.csv
Stores country-level metadata.

| Column | Description |
|------|------------|
| CountryID | Primary key |
| CountryName | Country name |
| CountryCode | ISO country code |

---

#### cities.csv
City-level geographic hierarchy.

| Column | Description |
|------|------------|
| CityID | Primary key |
| CityName | Name of the city |
| Zipcode | Postal code |
| CountryID | Foreign key to countries |

---

#### customers.csv
Customer master data.

| Column | Description |
|------|------------|
| CustomerID | Primary key |
| FirstName | Customer first name |
| MiddleInitial | Middle initial |
| LastName | Customer last name |
| CityID | Customer city |
| Address | Street address |

---

#### employees.csv
Employees handling sales.

| Column | Description |
|------|------------|
| EmployeeID | Primary key |
| FirstName | Employee name |
| BirthDate | DOB |
| Gender | Gender |
| CityID | Work city |
| HireDate | Employment start date |

---

#### products.csv
Product master data.

| Column | Description |
|------|------------|
| ProductID | Primary key |
| ProductName | Product name |
| Price | Unit price |
| CategoryID | Product category |
| ModifyDate | Last updated |
| Resistant | Shelf-life flag |
| IsAllergic | Allergy indicator |
| VitalityDays | Shelf life in days |

---

#### sales.csv
Transactional sales data (fact table).

| Column | Description |
|------|------------|
| SalesID | Primary key |
| SalesPersonID | FK to employees |
| CustomerID | FK to customers |
| ProductID | FK to products |
| Quantity | Units sold |
| Discount | Discount applied |
| TotalPrice | Final price |
| SalesDate | Transaction date |
| TransactionNumber | Business transaction ID |

---

### 4. Target Star Schema (Gold Layer)

**FactSales**
- SalesDate
- ProductID
- CustomerID
- EmployeeID
- Quantity
- Discount
- TotalSalesAmount

**Dimensions**
- DimProduct
- DimCustomer
- DimEmployee
- DimCity
- DimCountry
- DimCategory

---

### 5. Business Use Cases Enabled

- Daily & monthly sales trends
- Top-selling products and categories
- City & country-wise revenue
- Employee sales performance
- Discount impact analysis

---

### 6. Interview Summary Statement

> This dataset was used to implement an end-to-end Azure Modern Data Platform following Medallion Architecture, enabling scalable ingestion, transformation, warehousing, and BI analytics for a retail sales domain.

