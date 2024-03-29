# Data_Pipeline_with_Airflow
## Introduction
A music streaming company, Sparkify, has decided that it is time to introduce more automation and monitoring to their data warehouse ETL pipelines and come to the conclusion that the best tool to achieve this is Apache Airflow.

They decided to bring me into the project and expected me to create a high grade data pipeline that was dynamic and built from reusable tasks, can be monitored, and allow easy backfills. They have also noted that data quality plays a big part when analyses are executed on top the data warehouse and wanted to run tests against their datasets after the ETL steps have been executed to catch any discrepancies in the datasets.

The source data resided in S3 and needed to be processed in Sparkify's data warehouse in Amazon Redshift. The source datasets consisted of CSV logs that tell about user activity in the application and JSON metadata about the songs the users listen to.


## Project Datasets
I worked with two datasets that reside in S3. Here are the S3 links for each:

>Song data: s3://udacity-dend/song_data

>Log data: s3://udacity-dend/log_data

### Song Dataset
The first dataset is a subset of real data from the Million Song Dataset. Each file is in JSON format and contains metadata about a song and the artist of that song. The files are partitioned by the first three letters of each song's track ID.

### Log Dataset
The second dataset consists of log files in JSON format generated by this event simulator based on the songs in the dataset above. These simulate app activity logs from an imaginary music streaming app based on configuration settings.

The log files in the dataset I worked with are partitioned by year and month.

## Schema for Song Play Analysis
Using the song and event datasets, I created a star schema optimized for queries on song play analysis. It consists of two staging tables, one fact table and four dimension tables. The tables are detailed below:

### Fact Table
1. TABLE NAME: songplays
- TABLE COLUMNS: songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent


### Dimension Tables
1. TABLE NAME: users
- TABLE COLUMNS: user_id, first_name, last_name, gender, level

2. TABLE NAME: songs
- TABLE COLUMNS: song_id, title, artist_id, year, duration

3. TABLE NAME: artists  
- TABLE COLUMNS: artist_id, artist_name, artist_location, artist_latitude, artist_longitude

4. TABLE NAME: time
- TABLE COLUMNS: start_time, hour, day, week, month, year, weekday

## Airflow configuration
Four different operators were built which will stage the data, transform the data, and run checks on data quality. They are detailed below:

### Stage Operator
The stage operator loads JSON files from S3 to Amazon Redshift. The operator creates and runs a SQL COPY statement based on the parameters provided. The operator's parameters specify where in S3 the file is loaded and what is the target table.

### Fact and Dimension Operators
The dimension and fact operators, utilizes the provided SQL helper class to run data transformations.

Dimension loads are often done with the truncate-insert pattern where the target table is emptied before the load. Thus, you could also have a parameter that allows switching between insert modes when loading dimensions.

### Data Quality Operator
The data quality operator is used to run checks on the data itself. The operator's main functionality is to receive one or more SQL based test cases along with the expected results and execute the tests.

# NOTE: Tables must be created prior to running the etl DAG.

## REPO CONTENTS

*etl_dag.py* has all the imports and task templates in place.

*operators folder* contains all operators

*helper class* for the SQL transformations

*create_tables.sql* creates the necessary tables for the etl process  
