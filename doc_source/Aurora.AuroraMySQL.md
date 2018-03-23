# Working with Amazon Aurora MySQL<a name="Aurora.AuroraMySQL"></a>

Amazon Aurora MySQL is a fully managed, MySQL\-compatible, relational database engine that combines the speed and reliability of high\-end commercial databases with the simplicity and cost\-effectiveness of open\-source databases\. Aurora MySQL is a drop\-in replacement for MySQL and makes it simple and cost\-effective to set up, operate, and scale your new and existing MySQL deployments, thus freeing you to focus on your business and applications\. Amazon RDS provides administration for Aurora by handling routine database tasks such as provisioning, patching, backup, recovery, failure detection, and repair\. Amazon RDS also provides push\-button migration tools to convert your existing Amazon RDS for MySQL applications to Aurora MySQL\.

## Availability for Amazon Aurora MySQL<a name="Aurora.AuroraMySQL.Availability"></a>

The following table shows the regions where Aurora MySQL is currently available\.


| Region | Console Link | 
| --- | --- | 
| US East \(N\. Virginia\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-east\-1](https://console.aws.amazon.com/rds/home?region=us-east-1) | 
| US East \(Ohio\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-east\-2](https://console.aws.amazon.com/rds/home?region=us-east-2) | 
| US West \(N\. California\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-west\-1](https://console.aws.amazon.com/rds/home?region=us-west-1) | 
| US West \(Oregon\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-west\-2](https://console.aws.amazon.com/rds/home?region=us-west-2) | 
| Canada \(Central\) | [https://console\.aws\.amazon\.com/rds/home?region=ca\-central\-1](https://console.aws.amazon.com/rds/home?region=ca-central-1) | 
| Asia Pacific \(Mumbai\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-south\-1](https://console.aws.amazon.com/rds/home?region=ap-south-1) | 
| Asia Pacific \(Tokyo\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-northeast\-1](https://console.aws.amazon.com/rds/home?region=ap-northeast-1) | 
| Asia Pacific \(Seoul\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-northeast\-2](https://console.aws.amazon.com/rds/home?region=ap-northeast-2) | 
| Asia Pacific \(Singapore\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-southeast\-1](https://console.aws.amazon.com/rds/home?region=ap-southeast-1) | 
| Asia Pacific \(Sydney\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-southeast\-2](https://console.aws.amazon.com/rds/home?region=ap-southeast-2) | 
| EU \(Frankfurt\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-central\-1](https://console.aws.amazon.com/rds/home?region=eu-central-1) | 
| EU \(Ireland\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-1](https://console.aws.amazon.com/rds/home?region=eu-west-1) | 
| EU \(London\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-2](https://console.aws.amazon.com/rds/home?region=eu-west-2) | 
| EU \(Paris\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-3](https://console.aws.amazon.com/rds/home?region=eu-west-3) | 

## Amazon Aurora MySQL Performance Enhancements<a name="Aurora.AuroraMySQL.Performance"></a>

Amazon Aurora includes performance enhancements to support the diverse needs of high\-end commercial databases\.

### Fast Insert<a name="Aurora.AuroraMySQL.Performance.FastInsert"></a>

Fast insert accelerates parallel inserts sorted by primary key and applies specifically to `LOAD DATA` and `INSERT INTO ... SELECT ...` statements\. Fast insert caches the position of a cursor in an index traversal while executing the statement\. This avoids unnecessarily traversing the index again\.

You can monitor the following metrics to determine the effectiveness of fast insert for your DB cluster:
+ `aurora_fast_insert_cache_hits`: A counter that is incremented when the cached cursor is successfully retrieved and verified\. 
+ `aurora_fast_insert_cache_misses`: A counter that is incremented when the cached cursor is no longer valid and Aurora performs a normal index traversal\.

You can retrieve the current value of the fast insert metrics using the following command:

```
mysql> show global status like 'Aurora_fast_insert%';                
```

You will get output similar to the following:

```
+---------------------------------+-----------+
| Variable_name                   | Value     |
+---------------------------------+-----------+
| Aurora_fast_insert_cache_hits   | 3598300   |
| Aurora_fast_insert_cache_misses | 436401336 |
+---------------------------------+-----------+
```

## Amazon Aurora MySQL and Spatial Data<a name="Aurora.AuroraMySQL.Spatial"></a>

Amazon Aurora MySQL supports the same spatial data types and spatial relation functions as the equivalent MySQL release\. For example, Amazon Aurora MySQL 5\.7 supports the same [spatial data types](https://dev.mysql.com/doc/refman/5.7/en/spatial-types.html) and [spatial relation functions](https://dev.mysql.com/doc/refman/5.7/en/spatial-relation-functions-object-shapes.html) as MySQL 5\.7\. 

Aurora MySQL also supports spatial indexing on InnoDB tables, similar to that offered by MySQL 5\.7\. Spatial indexing improves query performance on large datasets for queries that use spatial data\. Aurora MySQL uses a different indexing strategy than MySQL, using a space\-filling curve on a B\-tree instead of an R\-tree\.

The following data definition language \(DDL\) statements are supported for creating indexes on columns that use spatial data types\.

### CREATE TABLE<a name="Aurora.AuroraMySQL.Spatial.create_table"></a>

 You can use the SPATIAL INDEX keywords in a CREATE TABLE statement to add a spatial index to a column in a new table\. Following is an example\.

```
CREATE TABLE test (shape POLYGON NOT NULL, SPATIAL INDEX(shape)); 
```

### ALTER TABLE<a name="Aurora.AuroraMySQL.Spatial.alter_table"></a>

You can use the SPATIAL INDEX keywords in an ALTER TABLE statement to add a spatial index to a column in an existing table\. Following is an example\.

```
ALTER TABLE test ADD SPATIAL INDEX(shape); 
```

### CREATE INDEX<a name="Aurora.AuroraMySQL.Spatial.create_index"></a>

You can use the SPATIAL keyword in a CREATE INDEX statement to add a spatial index to a column in an existing table\. Following is an example\.

```
CREATE SPATIAL INDEX shape_index ON test (shape);
```

## Comparison of Aurora MySQL 5\.6 and Aurora MySQL 5\.7<a name="Aurora.AuroraMySQL.CompareReleases"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL 5\.6, but these features are currently not supported in Aurora MySQL 5\.7\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Working with Asynchronous Key Prefetch in Amazon Aurora](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. You can asynchronously invoke AWS Lambda functions from Aurora MySQL 5\.7\. For more information, see [Invoking a Lambda Function with an Aurora MySQL Native Function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Amazon Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

Currently, Aurora MySQL 5\.7 does not support features added in Aurora MySQL version 1\.16 and later\. For information about Aurora MySQL version 1\.16, see [Amazon Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.

The performance schema is disabled for Aurora MySQL 5\.7\.

## Comparison of Aurora MySQL 5\.7 and MySQL 5\.7<a name="Aurora.AuroraMySQL.CompareMySQL57"></a>

The following features are supported in MySQL 5\.7\.12 but are currently not supported in Aurora MySQL 5\.7:
+ Global transaction identifiers \(GTIDs\)
+ Group replication plugin
+ Increased page size
+ InnoDB buffer pool loading at startup
+ InnoDB full\-text parser plugin
+ Multisource replication
+ Online buffer pool resizing
+ Password validation plugin
+ Query rewrite plugins
+ Replication filtering
+ The `CREATE TABLESPACE` SQL statement
+ X Protocol

For more information about these features, see the [MySQL 5\.7 documentation](https://dev.mysql.com/doc/refman/5.7/en/)\.