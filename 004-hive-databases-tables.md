

<img src="E:\code\HADOOP-TRAINING\CODING_HiveLabs\hive-basics\hive.svg" style="zoom:40%;" />

# Hive Databases

* In Hive a database is a namespace (collection) of tables.
* command to create a database
```
create database mydb;
```

* Another syntax to create database
```
create schema mydb;
```

* List all databases
```
SHOW DATABASES;
```

* Drop database
```
drop database if exists mydb;
```


# Hive Tables
* Hive tables are 2 dimensional Column - Row structure to store data on the Hadoop HDFS.



## Create Table in HIVE

* Here we demonstrate various types of Hive Tables
  * Internal Tables
  * External Tables
  * Temporary Tables



### Hive Internal Tables

* Hive owns the data for the internal tables.
* This is used by default, if the keyword external is not specified.
* Default folder path on Hadoop for internal tables is `/user/hive/warehouse` 
  directory of HADOOP File System.
* Default folder path can be overridden using the location property 
  during table creation.
* Internal tables support Truncate, ACID and Query result caching.
* All metadata is also deleted when a Hive internal table is dropped.
* Syntax to Create an Hive Internal Table and load data using insert
>
```sql
show databases;

-- Use a Database
use tinitiate;

-- Create a Hive internal table
create table hive_internal_table (
    empid      int
   ,ename      string
   ,salary     decimal(8,2)
   ,join_date  date)
row format delimited 
fields terminated by '\t';

-- Load Data
insert into hive_internal_table values (1,'AAA',1000.20, cast(date_format('2019-01-05','yyyy-MM-dd') as date) );
insert into hive_internal_table values (2,'BBB',2300.40, cast(date_format('2020-02-03','yyyy-MM-dd') as date) );
insert into hive_internal_table values (3,'CCC',3000.75, cast(date_format('2018-03-04','yyyy-MM-dd') as date) );
insert into hive_internal_table values (4,'DDD',4500.60, cast(date_format('2017-04-01','yyyy-MM-dd') as date) );

-- Check the ROW created
select * from hive_internal_table;

```

### Hive External Tables

* External Tables are flies stored in HADOOP folder.
* External Tables cannot be created using an existing table.
* External Tables are not owned by Hive, they are accessed for Read Only 
  operations, and enables drop that external table
* Data in External tables cannot be deleted even after they are dropped.
* Steps to create a external table
  * Get the name of HIVE folder where the current hive user has the 
    read-write privilege.
  * Use that folder as the base-folder and create a file in that folder 
```sql
-- Create an External table using the default folder 
-- "/warehouse/tablespace/managed/hive/" and the 
create External table hive_external_table (
    empid      int
   ,ename      string
   ,salary     decimal(8,2)
   ,join_date  date)
row format delimited 
FIELDS TERMINATED BY ','
LOCATION '/warehouse/tablespace/managed/hive/hive_external_table';

insert into hive_external_table values (1,'AAA',1000.20, cast(date_format('2019-01-05','yyyy-MM-dd') as date) );
insert into hive_external_table values (2,'BBB',2300.40, cast(date_format('2020-02-03','yyyy-MM-dd') as date) );
insert into hive_external_table values (3,'CCC',3000.75, cast(date_format('2018-03-04','yyyy-MM-dd') as date) );
insert into hive_external_table values (4,'DDD',4500.60, cast(date_format('2017-04-01','yyyy-MM-dd') as date) );

select * from hive_external_table;

```

### Hive Temporary Tables

* Temporary table is a table to store data temporarily, Hive drops the 
  table at the end of the session.
* The table and data in the Temporary Tables will be session specific.
* Partitioning is not supported on temporary tables.
* Indexes cannot be created on temporary table is not allowed.
* A temporary table will override a permanent table with the same name.
```sql
-- Create a Temporary Table
create temporary table hive_temp_table_1 (
    empid      int
   ,ename      string
   ,salary     decimal(8,2)
   ,join_date  date);

-- Insert data into Temp table
insert into hive_temp_table_1 values (1,'AAA',1000.20, cast(date_format('2019-01-05','yyyy-MM-dd') as date) );
insert into hive_temp_table_1 values (2,'BBB',2300.40, cast(date_format('2020-02-03','yyyy-MM-dd') as date) );
insert into hive_temp_table_1 values (3,'CCC',3000.75, cast(date_format('2018-03-04','yyyy-MM-dd') as date) );

-- Check Data 
select * from hive_temp_table_1;

-- Another way to create temp table
CREATE TEMPORARY TABLE hive_temp_table_2 AS Select * from hive_internal_table;

select * from hive_temp_table_2;
```



## Alter Table in HIVE

* Alter command on a hive table is is used when 
  * we have to rename table 
  * add a column
  * remove a column
  * change column datatype
* Here we demonstrate Alter command on a table

```sql
-- Create a table for Alter Demonstration
create table alter_demo (
    empid      int
   ,ename      string
   ,salary     decimal(8,2)
   ,join_date  date)
row format delimited 
FIELDS TERMINATED BY ',';

-- Rename a table
alter table alter_demo rename to alter_demo_tab;

-- Add Column to a table
alter table alter_demo_tab add columns (new_col date);

-- Add Multiple Columns to a table
alter table alter_demo_tab add columns (new_col1 date, new_col2 date);


-- Change the datatype of a column
alter table alter_demo_tab change new_col new_col string;

-- describe table to check
describe alter_demo_tab;

-- Remove / Drop Column from a table
  -- There is no Direct Way to Drop Columns in Hive,
  -- The way to drop column is to create a new table from the
  -- existing table without the column that needs to be dropped

-- 1. make a copy of the old table
alter table my_table to my_table_orig;

-- 2. make the new table without the columns to be deleted
create table my_table as
select columns_to_keep
from my_table_orig;

-- 3. 	drop the copy
DROP TABLE my_table_orig;
```

## Drop Table in HIVE

* To remove the table use the drop command.

  ```sql
  -- dump the table 
  DROP TABLE alter_demo_tab;
  ```

## Hive Table Operations

* Show all Hive Tables in Current Database
* Describe a Hive Table details
* Get DDL of a Hive Table
```sql
-- Use a Database
use tinitiate;

-- Show all Hive Tables in Current Database
show tables;

-- Describe the Table Name
describe emp;

-- Describe Extended properties of the Table
DESCRIBE EXTENDED emp;

-- Shows general table properties 
SHOW TBLPROPERTIES emp;

-- Describe a Hive Table details
describe formatted hive_internal_table;

-- Get DDL of a Hive Table
show create table hive_internal_table;
```

