# Snowflake_projects
Loading data from AWS S3 bucket to snowflake database table

![Screenshot 2025-02-15 004937](https://github.com/user-attachments/assets/e01f43e7-3517-450f-9c46-9be818e5114c)


CREATE
or replace DATABASE aws_integration;

CREATE TABLE CUSTOMER (
    FName varchar(80),
    LName varchar(80),
    Email varchar(100),
    Date_Of_Birth DATE,
    City varchar(100),
    Country varchar(100)
);

##Create a file format 
CREATE FILE FORMAT PIPE_DELIMITER TYPE = CSV FIELD_DELIMITER = '|' FIELD_OPTIONALLY_ENCLOSED_BY = '"' SKIP_HEADER = 1 DATE_FORMAT = 'YYYY-MM-DD';

SHOW file formats;

CREATE STAGE CUSTOMER_STAGE FILE_FORMAT = PIPE_DELIMITER;

SHOW STAGES;

##Create an Storage integration
create
or replace storage integration s3_integration type = external_stage storage_provider = s3 enabled = true storage_aws_role_arn = 'AWS Storage ARN ID' storage_allowed_locations = ('s3://ajayawssnowflake/csv/');

DESC INTEGRATION s3_integration;

##Create a external stage
create
or replace stage EXTERNAL_CSV_STAGE URL = 's3://ajayawssnowflake/csv/' STORAGE_INTEGRATION = s3_integration file_format = PIPE_DELIMITER;

list @EXTERNAL_CSV_STAGE;

##Copy command to load data fro s3 to snowflake database
copy into "AWS_INTEGRATION"."PUBLIC"."CUSTOMER"
from
    @EXTERNAL_CSV_STAGE/customers.csv on_error = CONTINUE;

select
    *
from
    customer;
