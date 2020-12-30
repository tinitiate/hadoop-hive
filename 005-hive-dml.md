# Hive DML
* DML is data manipulation language
* DML has 3 commands 
  * **INSERT** Adds ROWS of data to a table
  * **UPDATE** Changes existing ROWS data
  * **DELETE** Removeds existing ROWS from a table


## Hive Insert Command
* Here we create a EMP table with various datatypes
* Insert data of various datatypes
```sql
-- Create EMP Table
create table emp (
	empid     integer
   ,ename     varchar(100)
   ,sal       decimal(7,2)
   ,deptid    integer
   ,joindate  date )
row format delimited 
fields terminated by ',';


-- ----------------------------------------------------------------------------------
-- Insert Rows
-- ----------------------------------------------------------------------------------
-- * For the Date value use the 
--   from_unixtime(unix_timestamp(data_value,date_format))
--
insert into emp values(2,'B',2000.00,2,from_unixtime(unix_timestamp('02-13-2019' , 'dd-MM-yyyy')));
insert into emp values(3,'C',1000.00,3,from_unixtime(unix_timestamp('04-23-2018' , 'dd-MM-yyyy')));
insert into emp values(4,'D',2000.00,4,from_unixtime(unix_timestamp('07-03-2017' , 'dd-MM-yyyy')));
insert into emp values(5,'A',1000.00,1,from_unixtime(unix_timestamp('11-01-2019' , 'dd-MM-yyyy')));

-- Check the values by running a select command
select * from emp;

-- ----------------------------------------------------------------------------------
-- 1. Insert multiple values with one statement
-- ----------------------------------------------------------------------------------
insert into table emp
select stack
       ( 3       -- This indicates the number of rows below
        ,11,'AAA',2000.00,2,from_unixtime(unix_timestamp('02-13-2019' , 'dd-MM-yyyy'))
        ,22,'BBB',1000.00,3,from_unixtime(unix_timestamp('04-23-2018' , 'dd-MM-yyyy'))
        ,33,'CCC',2000.00,4,from_unixtime(unix_timestamp('07-03-2017' , 'dd-MM-yyyy'))
       );

-- Check the values by running a select command
select * from emp;


-- ----------------------------------------------------------------------------------
-- 2. Insert multiple values with one statement
-- ----------------------------------------------------------------------------------
insert into table emp
select inline(array
       (
           struct (77,'XXX',2000.00,2,from_unixtime(unix_timestamp('02-13-2019' , 'dd-MM-yyyy')))
          ,struct (88,'YYY',1000.00,3,from_unixtime(unix_timestamp('04-23-2018' , 'dd-MM-yyyy')))
          ,struct (99,'ZZZ',2000.00,4,from_unixtime(unix_timestamp('07-03-2017' , 'dd-MM-yyyy')))
       ))

-- Check the values by running a select command
select * from emp;

```


## Hive Update Command
* Update command is used to change the values of an existing column of a ROW or 
  multiple ROWS of data in Hive.
```sql
-- Update a single column, salary column of empid 1
-- ------------------------------------------------

-- Check before update
select * from emp where empid = 3;

-- The Update Statement
-- --------------------
update emp set sal = 4000 where empid = 3;

-- Check after update
select * from emp where empid = 3;

```


## Hive Delete Command
* Deletes or removes ROWS of data
```sql
-- Delete a single ROW, where empid is 3
-- ------------------------------------------------

-- Check before delete
select * from emp where empid = 3;

-- The delete Statement
-- --------------------
delete from emp where empid = 3;

-- Check after delete
select * from emp where empid = 3;

```

## Hive Merge Command

* Merge is Update if data present else insert.

```sql
-- Merge demo
-- ----------------------------

-- Create Products table and load 3 ROWS
-- --------------------------------------
create table products (
	 prod_id			int
	,prod_name       string 
	,prod_price      decimal
	,prod_category   string
);

insert into products values (1,'apple',1.00,'fruits');
insert into products values (2,'pen',2.00,'office supplies');
insert into products values (3,'soda',1.00,'fruits');


-- Create product_updates table and load 3 ROWS
-- Notice prod_id 3, The prod_category column is different
-- -------------------------------------------------------
create table product_updates (
	 prod_id			int
	,prod_name          string 
	,prod_price         decimal
	,prod_category      string
);

insert into products values (3,'soda',1.00,'drinks');
insert into products values (4,'onion',1.00,'vegetables');
insert into products values (5,'dishwashing liquid',4.00,'cleaning supplies');


-- Merge Statement
-- * Merge will Update when ROW is present, based on the 
--   JOIN condition in the "ON" clause
-- * Here the prod_id 4 and 5 will be selected from
--   product_updates and inserted into products table
-- * And prod_id 3 will be updated as it exists in both tables
-- --------------------------------------------------------
merge into products p
using product_updates pu
on (    p.prod_id = pu.prod_id
    and p.prod_name = pu.prod_name )
when matched then
update set  prod_price    = pu.prod_price
	       ,prod_category = pu.prod_category
when not matched then
insert 
values ( pu.prod_id
		,pu.prod_name 
		,pu.prod_price
		,pu.prod_category);


```

create table stores
store_id
store_address
store_zone

create table products
prod_id
prod_name
prod_price
prod_category

create table sales
sale_id
store_id
prod_id

