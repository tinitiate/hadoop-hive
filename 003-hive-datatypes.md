# Hive DataTypes
## Numeric Types

* TINYINT (1-byte signed integer, from -128 to 127)
* SMALLINT (2-byte signed integer, from -32,768 to 32,767)
* INT/INTEGER (4-byte signed integer, from -2,147,483,648 to 2,147,483,647)
* BIGINT (8-byte signed integer, from -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807)
* FLOAT (4-byte single precision floating point number)
* DOUBLE (8-byte double precision floating point number)
* DOUBLE PRECISION (alias for DOUBLE, available with Hive 2.2.0)
* DECIMAL user-definable precision and scale
* NUMERIC (same as DECIMAL, Available with Hive 3.0.0)

## Date/Time Types

* TIMESTAMP (Available with Hive 0.8.0+)
* DATE (Available with Hive 0.12.0+)
* INTERVAL (Available with Hive 1.2.0+)

## String Types

* STRING
* VARCHAR (Available with Hive 0.12.0)
* CHAR (Available with Hive 0.13.0)

## Misc Types

* BOOLEAN
* BINARY (Available with Hive 0.8.0)

## Complex Types

* ARRAY<data_type> (from Hive 0.14.+)
* MAP<primitive_type, data_type> (from Hive 0.14.+)
* STRUCT<col_name : data_type [COMMENT col_comment], ...>
* UNIONTYPE<data_type, data_type, ...> (from Hive 0.7.0.+)