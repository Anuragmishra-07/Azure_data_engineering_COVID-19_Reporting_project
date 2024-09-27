# Azure_data_engineering_COVID-19_Reporting_project

Concept of the Project 💡
This project involves the acquisition of several Covid-19 Datasets from the ECDC website. These datasets are subsequently processed through diverse ADF components to effect transformations. These transformations are executed using ADF, HDInsight, and Databricks. The resultant data is then loaded into SQL Datawarehouse with the intention of enabling the Analytics team to draw meaningful and practical insights from these datasets. The primary objective is to comprehensively understand the influence of Covid on the entirety of the European Region throughout the course of the year 2020.
Task 🎯
This project's mission is to ingest data from multiple data sources, clean it up, and alter it so that it is more robust and suitable for the goal. Once the data has been cleaned, it should be imported into a central repository, such as a data warehouse or datalake, so that the analytics team can utilize Power BI and other BI tools to access it. The data warehouse will contain information on confirmed cases, regrettable death rates, hospitalization and ICU cases from our weekly data lake counts, as well as testing statistics. Additionally, we can utilize these data to construct ML Models that forecast the spread of COVID in the European region.
Source Data: 📤
ECDC (https://www.ecdc.europa.eu/en/covid-19)
Population Data From Azure Blob Storage (eurostat_data)
Destination: 📥📍
Azure Data Lake Gen2 Storage.
Tools ⚙
Data Integration/Ingestion
ADF Data Flows within the Data Factory
Transformation
Data Flows within the Data Factory
DataBricks
Data Warehouse Solution
Azure SQL Database
Visualization
Power BI Desktop
Power BI Service
All the steps performed in this project are available as images in the Covidreporting_Azure_Screenshots folder in this repository.
Approach
Environment Setup
Azure Subscription
Data Factory
Azure Blob Storage Account
Data Lake Storage Gen2
Azure SQL Database
Azure Databricks Cluster
HD Insight Cluster
Solution Architecture Overview
Solution

DATA EXTRACTION/ INGESTION
Four different datasets were ingested from both the ECDC website and azure blob storage into Datalake Gen2. They are -

Cases and Deaths Data
Hospital Admissions Data
Population Data
Test Conducted Data
We used various components of ADF Pipeline activities to ingest the data from both HTTP Data Source and Azure Storage Account to Azure DataLake. Some of those activities are:

Validation Activity
Get Metadata Activity
Copy Activity
Population Data : Load into Storage Account and move it to Destination Data Lake
Ingest "population by age" data for all EU Countries into the Data Lake to support the machine learning models with the data to predict an increase in Covid-19 mortality rate.

Solution Flow
SolutionFlow

Steps:
Create a Linked Service To Azure Blob Storage
Create a Source Data Set
Create a Linked Service To Azure Data Lake storage (GEN2)
Create a Sink Data set
Create a Pipeline:
Execute Copy activity when the file becomes available
Check metadata counts before loading the data using the IF Condition
Finally Load Data into our destination
ScheduleTrigger
Pipeline Design :
Pipeline

ECDC Data from Web to Destination Data Lake
ECDC Data Content - Four files of CSV :
Case & Deaths Data.csv
Hospital Admission Data.csv
testing.csv
country_response.csv
Solution Flow
SOLUTION

Steps:

Create a Linked Service using an HTTP connector
Create a Source Data Set
Create a Linked Service To Azure Data Lake storage (GEN2)
Create a Sink Data set
Create a Pipeline With Parameters & Variables
Lookup to get all the parameters from json file, then pass it to ForEach ECDC DATA as shown below
Schedule Trigger
Screenshot 2023-08-22 112844

Pipeline Design :
13 changes_madeto_pl

2. DATA TRANSFORMATION
The Cases and Deaths data together with the Hospital admissions data was transformed using ADF Data flows. The Data Flows transformation used on both dataset include

Select transformation
Lookup transformation
Filter transformation
Join transformation
Sort transformation
Conditional split transformation
Derived columns transformation
Sink transformation
Data Flows (1) Cases & Deaths Data:
Solution Flow
Screenshot 2023-08-22 115115

Steps:
Cases And Deaths Source (Azure Data Lake Storage Gen2 )
Filter Europe-Only Data
Select only the required columns
PivotCounts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count
Lookup Country to get country_code_2_digit,country_code_3_digit columns
Select Only the required columns for the Sink
Create a Sink dataset (Azure Data Lake Storage Gen2)
Used Schedule Trigger
6 lookup

Data Flows (2) Hospital Admissions Data:
Solution Flow
Screenshot 2023-08-22 115705

Steps:
Hospital Admissions Source (Azure Data Lake Storage Gen2 )
Select only the required columns
Lookup Country to get country_code_2_digit,country_code_3_digit columns
Select only the required columns
Condition Split Weekly, Daily Split condition
indicator=='Weekly new hospital admissions per 100k' || indicator=='Weekly new ICU admissions per 100k'
indicator== "Daily hospital occupancy" || indicator=="Daily ICU occupancy"
For Weekly Path
Join with Date to get ecdc_Year_week, week_start_date, week_End_date
Piovt Counts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count
Sort data using reported_year_week ASC and Country DESC
Select only required columns for sink
Create a sink dataset (Azure Data Lake Storage Gen2)
Schedule Trigger
For Daily Path
Pivot Counts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count
Sort Data using reported_year_week ASC and Country DESC
Select only required columns for sink
Create a sink dataset (Azure Data Lake Storage Gen2)
Used Schedule Trigger
pllll

Databricks Activity (3) -- Population File:
Screenshot 2023-08-22 120411

3. Copy Data to Azure SQL
1- Copy Cases and Deaths 2- Copy hospital admissions data 3- Copy testing data

4 pl_succ

7 PL-SUCC

4. Reporting via Power BI
Create a Connection from Azure SQL to Power Bi and load the data
Analyze the data to get the total confirmed cases and deaths count
Identify the trends in data based on reporting date
Publish the report to Power BI Server
Publish to web
Covid-19 Trend in the EU/EEA & UK 2020 by Cases, Deaths, Hospital Occupancy, and ICU Occupancy
Screenshot 2023-08-22 121510

Covid-19 Cases and Death breakdown by population in the UK, France, and Germany
Screenshot 2023-08-22 121924

Total Number of covid tests carried out vs Confirmed Cases
Screenshot 2023-08-22 122141

Power BI Dashboard
Dashboard Link

Used Technologies
Azure DataFactory
Azure HDinsight (Hive)
Azure Databricks (Pyspark, SparkSql)
Azure Storage Account
Azure Data Lake Gen2
Azure SQL Database
