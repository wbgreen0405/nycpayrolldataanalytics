# NYC Payroll Data Analytics Pipeline

## Project Overview
This project builds a Data Analytics platform on Azure Synapse Analytics for the City of New York to analyze how financial resources are allocated and how much of the City's budget is devoted to overtime pay for municipal employees.

## Architecture
The pipeline uses the following Azure resources:
- **Azure Data Lake Storage Gen2** - Source data storage
- **Azure SQL Database** - Data warehouse tables
- **Azure Data Factory** - Pipeline orchestration
- **Azure Synapse Analytics** - Analytics and reporting

## Data Sources
The following CSV files are used as source data:
- `EmpMaster.csv` - Employee master data
- `AgencyMaster.csv` - Agency master data
- `TitleMaster.csv` - Job title master data
- `nycpayroll_2021.csv` - Current year payroll data
- `nycpayroll_2020.csv` - Historical payroll data

## Pipeline Structure
The pipeline `PL_NYC_Payroll_Master` runs in three waves:

**Wave 1 (parallel):**
- `DF_Load_AgencyMaster` - Loads agency data from ADLS to SQL DB
- `DF_Load_EmpMaster` - Loads employee data from ADLS to SQL DB
- `DF_Load_TitleMaster` - Loads title data from ADLS to SQL DB

**Wave 2 (after Wave 1):**
- `DF_Load_Payroll2020` - Loads 2020 payroll data from ADLS to SQL DB
- `DF_Load_Payroll2021` - Loads 2021 payroll data from ADLS to SQL DB

**Wave 3 (after Wave 2):**
- `DF_Summary` - Aggregates payroll data and writes to SQL DB and ADLS staging

## Data Aggregation
The `DF_Summary` data flow:
1. Reads 2020 and 2021 payroll data from SQL DB
2. Aligns column names (AgencyCode → AgencyID)
3. Unions both datasets
4. Filters by fiscal year parameter
5. Computes `TotalPaid = RegularGrossPaid + TotalOTPaid + TotalOtherPay`
6. Aggregates by AgencyName and FiscalYear
7. Writes results to SQL DB summary table and ADLS dirstaging

## SQL Database Tables
- `NYC_Payroll_EMP_MD` - Employee master
- `NYC_Payroll_TITLE_MD` - Job title master
- `NYC_Payroll_AGENCY_MD` - Agency master
- `NYC_Payroll_Data_2020` - 2020 payroll transactions
- `NYC_Payroll_Data_2021` - 2021 payroll transactions
- `NYC_Payroll_Summary` - Aggregated summary data

## Synapse Analytics
An external table `NYC_Payroll_Summary` in the `udacity` database reads from the `dirstaging` directory in ADLS Gen2.

## Setup Instructions
1. Create Azure Data Lake Storage Gen2 account
2. Create Azure SQL Database
3. Create Azure Synapse Analytics workspace
4. Create Azure Data Factory
5. Configure linked services in ADF
6. Run pipeline `PL_NYC_Payroll_Master`

## Author
William Green

## Tools & Technologies
- Azure Data Lake Storage Gen2
- Azure SQL Database
- Azure Data Factory
- Azure Synapse Analytics
- GitHub
