# SnowSQL

- It is a legacy command line interface (CLI client) for Snowflake.
- It is used to execute SQL queries and conduct all DDL and DML operations, including loading and unloading bulk data.
- It can be run as an interactive shell or in batch mode.
- It supports both a configuration file and a command line option for specifying a default warehouse.
- The configutation file stores connection information.
- The `snowsql.exe` is an example of an application developed using the Snowflake Connector for Python.
- Snowsight and SnowSQL are not mutually exclusive i.e., access to one does not block or require special permission to use the other.
- It allows MFA authorization and key pair authentication when connecting to Snowflake.
- Command line flags such as -d or -a can be used to log into a Snowflake account using SnowSQL.
- `HOST` command line parameter value can be pre-specified as an environment variable in SnowSQL.
- Snowflake recommends to transition to Snowflake CLI which is an open-source command-line tool explicitly designed for developer-centric workloads.

## Uploading files using PUT

- The `PUT` command uploads one or more data files from a local file system onto an internal stage (Named, Table, and User) using SnowSQL.
- It does not support uploading files onto an external stage.
- It automatically encrypts file data in transit before it is sent from the machine and compresses them using Gzip.
- Example:
  ```SQL
  PUT 'file:///tmp/data/orders 001.csv' @my_int_stage
  AUTO_COMPRESS = FALSE;
  ```

## Downloading files using GET

- The `GET` command is used to download (unload) files from a Snowflake stage (internal or external) to a local file system.
- Example:
  ```SQL
  GET @%TBL_EMPLOYEE 'file://C:/folder with space/';
  ```
