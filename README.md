# UK Site Weather ETL Pipeline 

An end-to-end ETL pipeline that pulls daily weather data for UK locations from the
[Open-Meteo API](https://open-meteo.com/), cleans and enriches it, and loads it into a
database for analysis.

**Use case:** weather affects construction schedules, since heavy rain and frost stop work on site.
This pipeline flags which days were workable at each location.

The pipeline is built twice to compare approaches:

| Version | Tools | File |
|---|---|---|
| 1 | Python, pandas, SQLite | `weather_pipeline_pandas.ipynb` |
| 2 | PySpark, Delta Lake, Databricks | `weather_pipeline_spark.ipynb` |

## Pipeline steps

**Extract:** calls the Open-Meteo API for each location (York, Leeds, Bristol, Exeter) and
combines the results into one table.

**Transform:**
- Renames columns to clear, consistent names
- Converts dates from text to a proper date type
- Removes rows with missing values
- Adds calculated columns:
  - `rain_day`: rain > 1mm
  - `workable`: rain < 5mm and minimum temperature above 0°C
  - `conditions`: categorised as Wet, Frost or Good

**Load:**
- Version 1: SQLite database
- Version 2: Delta table in Databricks (Unity Catalog)

## Analysis (SQL)
- Workable days per location
- Total rainfall per location
- Coldest day recorded
- Percentage of workable days per location
- Count of Wet / Frost / Good days per location

## What I learned
- Building ETL pipelines from an API source
- Translating pandas logic into PySpark
- Spark concepts: lazy evaluation, transformations vs actions, immutable DataFrames
- Delta Lake features, including table history
- SQL differences between SQLite and Spark SQL (e.g. boolean handling, integer division)

## Possible improvements
- Read locations from a reference table instead of hard-coding them
- Schedule the pipeline as a Databricks Job
- Append new days incrementally instead of overwriting
- Add data quality checks and alerts

*Note: the "workable day" thresholds are simplified assumptions for this project. In practice
these would be agreed with business stakeholders.*
