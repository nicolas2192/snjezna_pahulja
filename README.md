# Snježna Pahulja

Snowflake Certification Courses and Notes.

## :snowflake: Snowflake

Snowflake Web UI: https://app.snowflake.com/ca-central-1.aws/zv07890/worksheets
Snowflake Course: https://learn.snowflake.com/dashboard

## :book: Courses Notes

Users and Roles
Users can have multiple roles, each role allows the user to perform different actions. Users can change roles without login out of the system. AccountAdmin is the most powerful role. SysAdmin is the one used to create databases and tables.
Difference between Identity and Access
Identity is about confirming that you are you and access is about checking if you have the permit to see the data. Your identity is authenticated and your access is authorized. These processes are two different tasks. You are identified only once, when you submit username and password. Roles however, can be changed at any moment. Rights and privileges as well as ownership are awarded to roles, not users. 
Warehouses
In Snowflake, warehouses are not where data is stored. Warehouses are computing power. The bigger the warehouse the faster the code will run. The smallest or less powerful one is the extra small or XS.
Databases
Databases are comprised by schemas which in turn have other objects within them, such as tables and views. Similar to other databases such as PostgreSQL. 
When a new database is created, snowflake creates two schemas inside it; Information schema and public. Information schema holds a collection of views. This schema cannot be deleted or modified. Public schema holds tables, triggers, views, etc. Similar to other databases.
USE statement
You can use the USE statement to set some parameters before running a query. Some uses are: 
USE WAREHOUSE COMPUTE_WH;
USE DATABASE GARDEN_PLANTS;
USE SCHEMA VEGGIES;
Copy data from files
Upload files using the COPY INTO command. When the data loader wizard is used, files are uploaded to a temporary folder called dataloader. Once loaded, files from this folder will be deleted if purge was set to True in the COPY INTO command. Check the datafolder content running the following command: list @~/uploads/dataloader;
COPY INTO IDENTIFIER('"GARDEN_PLANTS"."VEGGIES"."VEGETABLE_DETAILS"') FROM '@~/uploads/dataloader/6dba363802c31f818b5f3829a85f07bf' file_format = (TYPE=csv, FIELD_OPTIONALLY_ENCLOSED_BY='"', ESCAPE_UNENCLOSED_FIELD=None, SKIP_HEADER=1, FIELD_DELIMITER='|') purge = true ON_ERROR=CONTINUE;
You can improve the copy into command usability by: creating an own staging area and adding copying parameters to a file. 
This copy into command would like something like this:
COPY INTO add_to_this_table
FROM @my_s3_bucket/staging/
FILES = (`table_in_csv.txt`)
FILE_FORMAT = (FORMAT_NAME = `file_format_parameters`);

Create a stage using the create stage command:


More info here: https://docs.snowflake.com/en/sql-reference/sql/create-file-format.html

Semi Structured data files
Snowflakes accepts the following 5 file types when it comes to semi structured data: AVRO, Parquet, ROC, JSON and XML. The data should be dumped into a column with a data type set as VARIANT.
CREATE TABLE database.public.table_xml ("RAW_AUTHOR" VARIANT); 

COPY INTO could be used to load the data. To do so, use the following file format:
CREATE OR REPLACE FILE FORMAT LIBRARY_CARD_CATALOG.PUBLIC.XML_FILE_FORMAT 
TYPE = 'XML' 
COMPRESSION = 'AUTO' 
PRESERVE_SPACE = FALSE 
STRIP_OUTER_ELEMENT = TRUE 
DISABLE_SNOWFLAKE_DATA = FALSE 
DISABLE_AUTO_CONVERT = FALSE 
IGNORE_UTF8_ERRORS = FALSE; 
Sequences
Sequences are counter objects. It comes in handy when you need to create a primary key like column where each row has a unique value. Keep in mind that snowflakes allows you two create two identical rows. You can name your sequences whatever you like, however, as a common convention, we name sequences with the table and column name they are assigned to: seq_table_column
CREATE SEQUENCE seq_author_uid
START = 1 INCREMENT = 1 COMMENT = 'Add a comment here';




S3 bucket for Snowflake course: https://uni-lab-files.s3.us-west-2.amazonaws.com/



Data Vault
3NF – 3 Normal Form. Good for writing tables
Dimension Model – Star Schema. Good for readings. Fact table surrounded by dimension tables.
Data vault. Good for reading.



Creating File Format CSV
CREATE OR REPLACE FILE FORMAT database.schema.my_csv_file_format
  type = csv
  field_delimiter = '\t' // or comma , or semicolon ;
  skip_header = 1
  TRIM_SPACE = TRUE
  ERROR_ON_COLUMN_COUNT_MISMATCH = FALSE // if there are more or less rows than expected
  FIELD_OPTIONALLY_ENCLOSED_BY = '"'; // additional character for escaping special fields
  
Create File Format for JSON 
CREATE FILE FORMAT LIBRARY_CARD_CATALOG.PUBLIC.JSON_FILE_FORMAT 
TYPE = 'JSON' 
COMPRESSION = 'AUTO' 
ENABLE_OCTAL = FALSE
ALLOW_DUPLICATE = FALSE
STRIP_OUTER_ARRAY = TRUE
STRIP_NULL_VALUES = FALSE
IGNORE_UTF8_ERRORS = FALSE; 

Copy data from window container into an actual table
COPY INTO AUTHOR_INGEST_JSON
FROM @like_a_window_into_an_s3_bucket
FILES = ('author_with_header.json')
FILE_FORMAT = (FORMAT_NAME = 'JSON_FILE_FORMAT');


//Create File Format for XML Data
CREATE FILE FORMAT LIBRARY_CARD_CATALOG.PUBLIC.XML_FILE_FORMAT 
TYPE = 'XML' 
STRIP_OUTER_ELEMENT = FALSE; 

COPY INTO author_ingest_xml
FROM @like_a_window_into_an_s3_bucket
FILES = ('author_with_header.xml')
FILE_FORMAT = (FORMAT_NAME = 'XML_FILE_FORMAT');


Create or replace sequences, unique ids
// Create a new sequence, this one will be a counter for the book table
CREATE OR REPLACE SEQUENCE "LIBRARY_CARD_CATALOG"."PUBLIC"."SEQ_BOOK_UID" 
START 1 
INCREMENT 1 
COMMENT = 'Use this to fill in the BOOK_UID everytime you add a row';

