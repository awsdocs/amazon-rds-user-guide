# Altering Tables in Amazon Aurora Using Fast DDL<a name="AuroraMySQL.Managing.FastDDL"></a>

In MySQL, many data definition language \(DDL\) operations have a significant performance impact\. Performance impacts occur even with recent online DDL improvements\.

For example, suppose that you use an ALTER TABLE operation to add a column to a table\. Depending on the algorithm specified for the operation, this operation can involve the following:
+ Creating a full copy of the table
+ Creating a temporary table to process concurrent data manipulation language \(DML\) operations
+ Rebuilding all indexes for the table
+ Applying table locks while applying concurrent DML changes
+ Slowing concurrent DML throughput

In Amazon Aurora, you can use fast DDL to execute an ALTER TABLE operation in place, nearly instantaneously\. The operation completes without requiring the table to be copied and without having a material impact on other DML statements\. Because the operation doesn't consume temporary storage for a table copy, it makes DDL statements practical even for large tables on small instance types\.

**Note**  
Fast DDL is available for Aurora version 1\.12 and later\. For more information about Aurora versions, see [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md)

## Limitations<a name="AuroraMySQL.Managing.FastDDL.Limitations"></a>

Currently, fast DDL has the following limitations:
+ Fast DDL only supports adding nullable columns, without default values, to the end of an existing table\.
+ Fast DDL does not support partitioned tables\.
+ Fast DDL does not support InnoDB tables that use the REDUNDANT row format\.
+ If the maximum possible record size for the DDL operation is too large, fast DDL is not used\. A record size is too large if it is greater than half the page size\. The maximum size of a record is computed by adding the maximum sizes of all columns\. For variable sized columns, according to InnoDB standards, extern bytes are not included for computation\.
**Note**  
The maximum record size check was added in Aurora 1\.15\.

## Syntax<a name="AuroraMySQL.Managing.FastDDL.Syntax"></a>

```
ALTER TABLE tbl_name ADD COLUMN col_name column_definition
```

## Options<a name="AuroraMySQL.Managing.FaultInjectionQueries.FastDDL.Options"></a>

This statement takes the following options:
+ **`tbl_name` — **The name of the table to be modified\.
+ **`col_name` — **The name of the column to be added\.
+ **`col_definition` — **The definition of the column to be added\.
**Note**  
You must specify a nullable column definition without a default value\. Otherwise, fast DDL isn't used\.