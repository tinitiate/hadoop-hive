| Hive Tables           |
| --------------------- |
| (c) Venkata Bhattaram |



# Hive Table Storage
* Hive Database supports various storage parameters and types for hive tables
* Where are Hive Tables stored
  * Here are the steps to find the details
  * Step 1. Get the Hive Data using the command, run the following in the 
    IDE or HIVE prompt
```sql
set hive.metastore.warehouse.dir;
-- hive.metastore.warehouse.dir=/warehouse/tablespace/managed/hive
```
* Using the hadoop command see the table files from the location above

  * Run the following at the command prompt after SSH to the hadoop terminal
```bash  
# Check on Hadoop server
# Log on to the Hadoop VM / Server
# ALT + F5
# user: root
# passwd: Tinitiate!23
# Enter the following
[root@sandbox-hdp ~]# hadoop fs -ls /warehouse/tablespace/managed/hive
```

## Types of Hive Table Storage

### **STORED IN TEXTFILE**

* Store as a Delimited file, where we specify the details of ROW format 
  and delimiter formats.
* TEXTFILE Supports ONLY **INSERT**, it doesn't support Updates and Deletes of rows.

```sql
create table emp_textfile (
    empid    int
   ,empname  string
   ,salary   decimal
   ,joindate date)
comment 'emp details as textfile'
row format delimited
fields terminated by '\t'
lines terminated by '\n'
stored as textfile;

-- Insert TWO Rows
insert into emp_textfile values 
(1,'AA',10000.00,from_unixtime(unix_timestamp('02-13-2019' , 'dd-MM-yyyy')));

insert into emp_textfile values 
(2,'BB',9000.00,from_unixtime(unix_timestamp('02-13-2019' , 'dd-MM-yyyy')));
```
  * Navigating through the Hadoop File System to see the data, using the Hadoop 
    command navigate to the `hive.metastore.warehouse.dir`
  * Use the `hadoop fs -ls <hive.metastore.warehouse.dir>` from there further 
    navigate all the folders and you will find the text file for each row.
  * Example below
```bash
# Check on Hadoop server
# Log on to the Hadoop VM / Server
# ALT + F5
# user: root
# passwd: Tinitiate!23
# Enter the following

[root@sandbox-hdp ~]# hadoop fs -ls /warehouse/tablespace/managed/hive/tinitiate.db/emp_textfile
Found 2 items
drwxrwxrwx+  - hive hadoop 0 2020-05-06 14:53 /warehouse/tablespace/managed/hive/tinitiate.db/emp_textfile/delta_0000001_0000001_0000
drwxrwxrwx+  - hive hadoop 0 2020-05-06 14:53 /warehouse/tablespace/managed/hive/tinitiate.db/emp_textfile/delta_0000002_0000002_0000

[root@sandbox-hdp ~]# hadoop fs -ls /warehouse/tablespace/managed/hive/tinitiate.db/emp_textfile/delta_0000001_0000001_0000
Found 1 items
-rw-rw-rw-+  1 hive hadoop  22 2020-05-06 14:53 /warehouse/tablespace/managed/hive/tinitiate.db/emp_textfile/delta_0000001_0000001_0000/000000_0

[root@sandbox-hdp ~]# hadoop fs -cat /warehouse/tablespace/managed/hive/tinitiate.db/emp_textfile/delta_0000001_0000001_0000/000000_0
1       AA      10000   2020-01-02
```

### **STORED IN SEQUENCE FILE**

* **Sequence files** are Hadoop flat files, where in the data is stored as binary key-value pairs.

* Sequence files are stored as binary format and provide features of file **Split** into multiple files and **Merge** into one file.

* Sequence files support block-level compression, data can be compressed, while maintaining the ability to split the file into segments for multiple map reduce tasks.

* Sequence file Supports ONLY **INSERT**, it doesn't support Updates and Deletes of rows.

  ```sql
  create table emp_sequencefile (
      empid    int
     ,empname  string
     ,salary   decimal
     ,joindate date)
  comment 'emp details as sequencefile'
  row format delimited 
  fields terminated by '\t'
  stored as sequencefile;
  
  -- Insert Data
  insert into emp_sequencefile values (1,'AA',10000.00,from_unixtime(unix_timestamp('02-13-2019' , 'dd-MM-yyyy')));
  insert into emp_sequencefile values (2,'BB',9000.00,from_unixtime(unix_timestamp('02-13-2019' , 'dd-MM-yyyy')));
  ```


### **STORED IN ORC**

* This is the Default storage for the Hive table.
* **Optimized Row Columnar** (ORC) file format, where in the data is stored as a 
  columnar as file. Data Blocks called as **stripe** is divided into Header, body and footer.
* Supports compression
* Supports Concurrency on the file for operations.
* Supports ACID Transactions & Cost-based Optimizer (CBO), Supports Insert, 
  Update and Delete.
* File Structure is made up of Header, Body and Footer (**Stripe**) with a PostScript
  * **Header** has the index information
  * **Body** has the actual rowdata and 
  * **Footer** has the number of rows per stripe, column data type and column-level 
    aggregates count, min, max, and sum.
  * **PostScript** holds compression parameters and the size of the compressed footer.
* Default stripe size is 250 MB.

```sql
-- ORC File format table
create table emp_orcfile (
    empid    int
   ,empname  string
   ,salary   decimal
   ,joindate date)
comment 'emp details as orc file'
stored as orc tblproperties ("orc.compress"="NONE");

-- Load data
insert into emp_orcfile values (1,'AA',10000.00,from_unixtime(unix_timestamp('02-13-2019' , 'dd-MM-yyyy')));
insert into emp_orcfile values (2,'BB',9000.00,from_unixtime(unix_timestamp('02-13-2019' , 'dd-MM-yyyy')));

-- Check data
select * from emp_orcfile;

-- Default Storage Format, when no storage format is specified
create table emp_orcfile_1 (
    empid    int
   ,empname  string
   ,salary   decimal
   ,joindate date)
comment 'emp details as orc file';

-- Load data
insert into emp_orcfile_1 values (1,'AA',10000.00,from_unixtime(unix_timestamp('02-13-2019' , 'dd-MM-yyyy')));
insert into emp_orcfile_1 values (2,'BB',9000.00,from_unixtime(unix_timestamp('02-13-2019' , 'dd-MM-yyyy')));

-- Check data
select * from emp_orcfile_1;

```

Check on Hadoop server

```bash
# Check on Hadoop server
# Log on to the Hadoop VM / Server
# ALT + F5
# user: root
# passwd: Tinitiate!23
# Enter the following
[root@sandbox-hdp ~]# hadoop fs -ls /warehouse/tablespace/managed/hive/tinitiate.db
```



### STORED IN PARQUET

* **Apache Parquet** is data storage format supported by Hive and Hadoop to store complex nested data structures.
* Data can be stored as MAPs, ARRAYs (java like arrays and maps), these are nested data structures.
* Updates are not supported by **Parquet** files

```sql
create table bill_parquet
   ( billid       int
    ,bill_details array< struct<product_name:string, quantity:int, unitprice:int, totalprice:int> >
    ,bill_total   decimal)
comment 'bill_parquet as parquet file'
stored as parquet;

insert into bill_parquet
select  1
  	   ,ARRAY(  NAMED_STRUCT('product_name','Apples','quantity',10,'unitprice',1,'totalprice',5)
,NAMED_STRUCT('product_name','Milk','quantity',1,'unitprice',3,'totalprice',3)
,NAMED_STRUCT('product_name','Bread','quantity',1,'unitprice',4,'totalprice',4)) as data
       ,12
FROM   (select '1' ) t;

-- Insert Nested Data
INSERT INTO temp4 
select ARRAY(NAMED_STRUCT('attribute_value','null','key','null','value','null'),NAMED_STRUCT('attribute_value','null','key','null','value','null')) as additionalattribute
FROM   (select '1' ) t;
-- Check Data
select * from bill_parquet;
```

Check on Hadoop server

```bash
# Check on Hadoop server
# Log on to the Hadoop VM / Server
# ALT + F5
# user: root
# passwd: Tinitiate!23
# Enter the following
[root@sandbox-hdp ~]# hadoop fs -ls /warehouse/tablespace/managed/hive/tinitiate.db
```



## STORED IN AVRO

* **Apache AVRO** is a row-based data format. 
* Data schema is stored as JSON in the header, while the rest of the data is stored in binary format.

```sql
-- DB Code to be run in an IDE such as DBBeaver
-- ---------------------------------------------
drop table bill_avro;

create table bill_avro
   ( billid       int
    ,bill_details array< struct<product_name:string, quantity:int, unitprice:int, totalprice:int> >
    ,bill_total   decimal)
comment 'bill_avro as avro file'
stored as avro;

insert into bill_avro
select  1
  	   ,ARRAY( NAMED_STRUCT('product_name','Apples','quantity',10,'unitprice',1,'totalprice',5)
		      ,NAMED_STRUCT('product_name','Milk','quantity',1,'unitprice',3,'totalprice',3)
		      ,NAMED_STRUCT('product_name','Bread','quantity',1,'unitprice',4,'totalprice',4)) as data
       ,12
FROM   (select '1' ) t;

select * from bill_avro;
```

* Check on Hadoop server

  ```bash
  # Check on Hadoop server
  # Log on to the Hadoop VM / Server
  # ALT + F5
  # user: root
  # passwd: Tinitiate!23
  # Enter the following
  [root@sandbox-hdp ~]# hadoop fs -ls /warehouse/tablespace/managed/hive/tinitiate.db
  ```

  

### Other Supported formats

* RCFILE
* JSON-FILE formats

