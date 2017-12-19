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
| Asia Pacific \(Sydney\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-southeast\-2](https://console.aws.amazon.com/rds/home?region=ap-southeast-2) | 
| EU \(Frankfurt\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-central\-1](https://console.aws.amazon.com/rds/home?region=eu-central-1) | 
| EU \(Ireland\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-1](https://console.aws.amazon.com/rds/home?region=eu-west-1) | 
| EU \(London\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-2](https://console.aws.amazon.com/rds/home?region=eu-west-2) | 

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

Amazon Aurora MySQL supports the same [Spatial Data Types](https://dev.mysql.com/doc/refman/5.6/en/spatial-datatypes.html) and [Spatial Relation Functions](https://dev.mysql.com/doc/refman/5.6/en/spatial-relation-functions-object-shapes.html) as MySQL 5\.6\. Aurora MySQL also supports spatial indexing on InnoDB tables, similar to that offered by MySQL 5\.7, which improves query performance on large datasets for queries that use spatial data\. Note that Aurora MySQL uses a different indexing strategy than MySQL, using a space\-filling curve on a B\-tree instead of an R\-tree\.

The following data definition language \(DDL\) statements are supported for creating indexes on columns that use spatial data types\.

### CREATE TABLE<a name="Aurora.AuroraMySQL.Spatial.create_table"></a>

 You can use the SPATIAL INDEX keywords in a CREATE TABLE statement to add a spatial index to a column in a new table\. For example:

```
CREATE TABLE test (shape POLYGON NOT NULL, SPATIAL INDEX(shape)); 
```

### ALTER TABLE<a name="Aurora.AuroraMySQL.Spatial.alter_table"></a>

You can use the SPATIAL INDEX keywords in an ALTER TABLE statement to add a spatial index to a column in an existing table\. For example:

```
ALTER TABLE test ADD SPATIAL INDEX(shape); 
```

### CREATE INDEX<a name="Aurora.AuroraMySQL.Spatial.create_index"></a>

You can also use the SPATIAL keyword in a CREATE INDEX statement to add a spatial index to a column in an existing table\. For example:

```
CREATE SPATIAL INDEX shape_index ON test (shape);
```

## Comparison of Amazon Aurora MySQL and Amazon RDS for MySQL<a name="Aurora.AuroraMySQL.Compare"></a>

Although Aurora instances are compatible with MySQL client applications, Aurora has advantages over MySQL as well as limitations to the MySQL features that Aurora supports\. This functionality can influence your decision about whether Amazon Aurora or MySQL on Amazon RDS are the best cloud database for your solution\. The following table shows the differences between Amazon Aurora and Amazon RDS for MySQL\.


| Feature | Amazon Aurora | Amazon RDS for MySQL | 
| --- | --- | --- | 
| Read scaling | Supports up to 15 Aurora Replicas with minimal impact on the performance of write operations\. | Supports up to 5 Read Replicas with some impact on the performance of write operations\. | 
| Failover target | Aurora Replicas are automatic failover targets with no data loss\. | Read Replicas can be manually promoted to the master DB instance with potential data loss\. | 
| MySQL version | Supports only MySQL version 5\.6\. | Supports MySQL versions 5\.5, 5\.6, and 5\.7\. | 
| AWS Region | Aurora DB clusters can only be created in the following regions: US East \(N\. Virginia\) \(us\-east\-1\), US East \(Ohio\) \(us\-east\-2\), US West \(N\. California\) \(us\-west\-1\), US West \(Oregon\) \(us\-west\-2\), Canada \(Central\) \(ca\-central\-1\), Asia Pacific \(Mumbai\) \(ap\-south\-1\), Asia Pacific \(Tokyo\) \(ap\-northeast\-1\), Asia Pacific \(Seoul\) \(ap\-northeast\-2\), Asia Pacific \(Sydney\) \(ap\-southeast\-2\), EU \(Frankfurt\) \(eu\-central\-1\), EU \(Ireland\) \(eu\-west\-1\), EU \(London\) \(eu\-west\-2\)\.  | Available in all AWS regions\. | 
| MySQL storage engine |  Supports only InnoDB\. Tables from other storage engines are automatically converted to InnoDB\. For information on converting existing MySQL tables to InnoDB and importing into an Aurora cluster, see [Migrating Data to an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Migrating.md)\.  Because Amazon Aurora only supports the InnoDB engine, the `NO_ENGINE_SUBSTITUTION` option of the `SQL_MODE` database parameter is enabled\. This disables the ability to create an in\-memory table, unless that table is specified as `TEMPORARY`\.   | Supports both MyISAM and InnoDB\. | 
| Read Replicas with a different storage engine than the master instance | MySQL \(non\-RDS\) Read Replicas that replicate with an Aurora DB cluster can only use InnoDB\. | Read Replicas can use both MyISAM and InnoDB\. | 
| Database engine parameters | Some parameters apply to the entire Aurora DB cluster and are managed by DB cluster parameter groups\. Other parameters apply to each individual DB instance in a DB cluster and are managed by DB parameter groups\. For more information, see [Amazon Aurora DB Cluster and DB Instance Parameters](Aurora.Managing.md#Aurora.Managing.ParameterGroups)\. | Parameters apply to each individual DB instance or Read Replica and are managed by DB parameter groups\. | 