# ETL Setup and Execution Guide

This guide explains how to run the ETL processes responsible for collecting:

* **DEC data**
* **FEC data**
* **Geographic distributor data**
* **Energy loss data**

---

# Initial Configuration

Before running the ETL, complete the following setup steps.

## 1. Configure Distributor CNPJ

Make sure the `application.properties` file contains the CNPJ of the target distributor in:

```
etl_cnpj=<DISTRIBUTOR_CNPJ>
```

---

## 2. Download Required Files

Two Excel files are required.

### DistBase.xlsx

Place the file in the root directory of the backend.

Download from:

[Power BI - DistBase.xlsx Source](https://app.powerbi.com/view?r=eyJrIjoiNDI4ODJiODctYTUyYS00OTgxLWE4MzktMDczYTlmMDU0ODYxIiwidCI6IjQwZDZmOWI4LWVjYTctNDZhMi05MmQ0LWVhNGU5YzAxNzBlMSIsImMiOjR9&pageName=ReportSection&utm_source=chatgpt.com)

---

### Perdas.xlsx

Place the file in the root directory of the backend.

Download from:

[ANEEL Energy Loss Report](https://portalrelatorios.aneel.gov.br/luznatarifa/perdasenergias?utm_source=chatgpt.com#!)

---

# Database Setup

To ensure all schemas and tables are created correctly, go to the backend folder and run:

```bash id="v30cd7"
docker compose down -v
docker compose up --build
```

Wait until initialization is complete.

Then stop the application using:

```bash id="6k4nm9"
CTRL + C
```

---

# Start Databases Only

Run only PostgreSQL and MongoDB:

```bash id="ofw54f"
docker compose up postgres -d
docker compose up mongodb -d
```

---

# Run ETL Processes

Move to the Java backend:

```bash id="m2wv74"
cd backend-java
```

---

## Run Distributor ETL (DEC, FEC, Geographic Data)

```bash id="hvhht0"
ETL_FILE_DIST_PATH=/path/to/DistBase.xlsx \
mvn spring-boot:run -Dspring-boot.run.arguments=etl
```

---

## Run Energy Loss ETL

```bash id="h0m1p5"
ETL_FILE_PERDAS_PATH=/path/to/Perdas.xlsx \
mvn spring-boot:run -Dspring-boot.run.arguments=etl-perdas
```

---

# Important Notes

* Replace `/path/to/DistBase.xlsx` with the actual path to your `DistBase.xlsx` file.
* Replace `/path/to/Perdas.xlsx` with the actual path to your `Perdas.xlsx` file.
* Ensure both files are accessible before running the ETL processes.
