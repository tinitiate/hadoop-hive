| Hive Partitioning     |
| --------------------- |
| (c) Venkata Bhattaram |

# Hive Table Partitions

* Hive Tables can be divided based on the data **column values** to different  storage buckets called **partitions**, This is very useful when the data volumes grows to very high levels such as tens of millions to billions of rows of data.

* Partitioning Only affects the way data is stored internally in a table, other aspects of the table/column stay the same, such as data types or column order.

* The column on which the partition is created is called the **partition key**, partition on a column is done on a **LIST of values** of a partition key, such as Date or a Category of values.

* **ALWAYS** choose a column, where the **cardinality** is **very low**, meaning, there are low number of repeating values.

* Consider the below sales dataset.

  ![Un-Partitioned Table](E:\code\HADOOP_TRAINING\CODING_HiveLabs\hive-basics\partition-basedata.png)

* The above can be **partitioned** on the **SaleDate** range, by **Month** values

  ![hive partition](E:\code\HADOOP_TRAINING\CODING_HiveLabs\hive-basics\partition-keycol.png)


##  

## Creating Hive Dynamic Partitioned Table

* Here we demonstrate creation of a **partitioned table**.

* This table creates partitions dynamically based on the values supplied to the **saledate** column.

* To enable the Dynamic Partition we must use the SET parameters for `dynamic.partition`

  ```sql
  -- Create Sales Table Partitioned by sale date
  drop table sales;
  
  -- Create Partitioned Table
  create table sales (
      region           string
     ,product          string
     ,productcategory  string
     ,quantity         integer
  ) PARTITIONED BY (saledate date);
  
  -- Set Session Parameter to Non Strict
  -- This will enable Dynamic Partition creation and enable INSERTs,UPDATEs, DELETEs
  set hive.exec.dynamic.partition=true;
  set hive.exec.dynamic.partition.mode=nonstrict
  
  -- Load Data
  insert into sales partition(saledate='2020-01-01') 
  values ('EAST','A','FROZEN FOOD',100);
  insert into sales partition(saledate='2020-01-01')
  values ('EAST','B','SEA FOOD',300);
  insert into sales partition(saledate='2020-01-01')
  values ('WEST','C','PROCESSED FOOD',300);
  insert into sales partition(saledate='2020-01-01')
  values ('WEST','A','FROZEN FOOD',100);
  insert into sales partition(saledate='2020-01-01')
  values ('SOUTH','A','FROZEN FOOD',500);
  insert into sales partition(saledate='2020-02-01')
  values ('EAST','B','SEA FOOD',300);
  insert into sales partition(saledate='2020-02-01')
  values ('EAST','C','PROCESSED FOOD',100);
  insert into sales partition(saledate='2020-02-01')
  values ('WEST','A','FROZEN FOOD',200);
  insert into sales partition(saledate='2020-02-01') 
  values ('SOUTH','B','SEA FOOD',300);
  insert into sales partition(saledate='2020-02-01')
  values ('NORTH','A','FROZEN FOOD',600);
  insert into sales partition(saledate='2020-03-01')
  values ('EAST','A','FROZEN FOOD',200);
  insert into sales partition(saledate='2020-03-01')
  values ('EAST','B','SEA FOOD',300);
  insert into sales partition(saledate='2020-03-01')
  values ('WEST','B','SEA FOOD',100);
insert into sales partition(saledate='2020-03-01')
  values ('SOUTH','C','PROCESSED FOOD',200);
  insert into sales partition(saledate='2020-03-01')
  values ('NORTH','C','PROCESSED FOOD',300);
  ```
  
  ```sql
  -- Check data
  select * from sales;
  
  -- Show all partitions of Sales Table
SHOW PARTITIONS sales;
  
  -- Show specific Partition information of sales table
  DESCRIBE EXTENDED sales PARTITION (saledate='2020-01-01');
  ```
  
  

## Creating Hive Static Partitioned Table

* Static partitions are partitions created ahead of the data insert into the partitioned table

* Static partitions are done using the `alter table add partition clause`

* By default the hive `hive.mapred.mode` parameter is set to **strict**, this will prevent inserts/updated happening on a partitioned table, unless the partitions already exist

  `set hive.mapred.mode = strict`

  ```sql
  -- Create Sales Table Partitioned by sale date
  drop table sales;
  
  -- Create Partitioned Table
  create table sales (region string, product string, productcategory string, quantity integer) PARTITIONED BY(saledate date);
  
  -- Add partitions using the alter statement
  alter sales add partition (saledate='2020-01-01');
  alter sales add partition (saledate='2020-02-01');
  alter sales add partition (saledate='2020-03-01');
  
  -- insert data into respective partitions
  insert into sales partition(saledate='2020-01-01') 
  values ('EAST','A','FROZEN FOOD',100);
  insert into sales partition(saledate='2020-01-01')
  values ('EAST','B','SEA FOOD',300);
  insert into sales partition(saledate='2020-02-01')
  values ('EAST','B','SEA FOOD',300);
  insert into sales partition(saledate='2020-02-01')
  values ('EAST','C','PROCESSED FOOD',100);
  insert into sales partition(saledate='2020-03-01')
  values ('EAST','A','FROZEN FOOD',200);
  insert into sales partition(saledate='2020-03-01')
  values ('EAST','B','SEA FOOD',300);
  ```

  

## Check the folder details in Hadoop

* Use the command below, to see the folder details of the table sales

  ```bash
  hadoop fs -ls /warehouse/tablespace/managed/hive/tinitiate.db/sales
  # Results:
  #[root@sandbox-hdp ~]# hadoop fs -ls #/warehouse/tablespace/managed/hive/tinitiate.db/sales
  #Found 3 items
  
  #drwxr-xr-x+  - hive hadoop          0 2020-06-06 02:40 #/warehouse/tablespace/managed/hive/tinitiate.db/sales/saledate=2020-01-01
  
  #drwxr-xr-x+  - hive hadoop          0 2020-06-06 02:41 #/warehouse/tablespace/managed/hive/tinitiate.db/sales/saledate=2020-02-01
  
  #drwxr-xr-x+  - hive hadoop          0 2020-06-06 02:42 #/warehouse/tablespace/managed/hive/tinitiate.db/sales/saledate=2020-03-01
  
  ```

  

## Drop Partition Command from Table

* Partitions can be dropped using the `alter table drop partition clause`

  ```sql
  alter table sales drop partition (saledate='2020-03-01');
  ```

  

## Partition for a Range of Values

* Hive supports only **LIST Partition** where in each partition is based on a value and not a range of values

* To create a Range Partition, we must use a case statement to generate a pseudo column to use for the partition key column

  ```sql
  create table emp_data_part (
     emp_id  int
    ,salary  double
  )
  partitioned by (sal_range int);
  
  -- Create salary_range psuedo column
  insert overwrite table emp_data_part partition (salary_range)   
  select emp_id, salary,  
         case when salary between 1000 and 2000 then 2000
              when salary between 2001 and 3000 then 3000
              else 0 -- default partition
         end as sal_range 
  from emp_source;
  ```




## Partition over multiple columns

```sql
-- Create Cities table partition by Country, State, County
-- -------------------------------------------------------
create table cities ( city_id     int
                     ,city_name   string)
partitioned by ( country     string
                ,state       string
                ,county      string);

-- Insert Data
-- -------------------------------------------------------
insert into cities partition( country='India'
                             ,state='Maharastra'
                             ,county='Mumbai') 
values (1,'Mumbai');

```



* Check the Hadoop file system to see the folders of the partition

  ```bash
  hadoop fs -ls /warehouse/tablespace/managed/hive/tinitiate.db/cities
  # drwxr-xr-x+  - hive hadoop          0 2020-06-07 16:20 /warehouse/tablespace/managed/hive/tinitiate.db/cities/country=India
  
  hadoop fs -ls /warehouse/tablespace/managed/hive/tinitiate.db/cities/country=India
  # drwxr-xr-x+  - hive hadoop          0 2020-06-07 16:20 /warehouse/tablespace/managed/hive/tinitiate.db/cities/country=India/state=Maharastra
  
  hadoop fs -ls /warehouse/tablespace/managed/hive/tinitiate.db/cities/country=India/state=Maharastra
  #drwxr-xr-x+  - hive hadoop          0 2020-06-07 16:21 /warehouse/tablespace/managed/hive/tinitiate.db/cities/country=India/state=Maharastra/county=Mumbai
  ```

  

### Hadoop File system - Hive Partitions

### Hive DDL BucketedSortedTables