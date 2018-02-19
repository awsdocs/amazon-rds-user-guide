# Amazon Aurora MySQL Database Engine Updates 2018\-02\-06<a name="AuroraMySQL.Updates.20180206"></a>

**Version:** 2\.01

Amazon Aurora MySQL 2\.01 is generally available\. 

Going forward, Aurora MySQL 2\.x versions will be compatible with MySQL 5\.7\.12 and Aurora MySQL 1\.x versions will be compatible with MySQL 5\.6\.10\. When creating a new Aurora MySQL DB cluster, including those restored from snapshots, you have the option of choosing compatibility with either MySQL 5\.7\.12 or MySQL 5\.6\.10\.

You can restore snapshots of Aurora MySQL 1\.14\*, 1\.15\*, and 1\.16\* into Aurora MySQL 2\.01\.

We do not allow in\-place upgrade of Aurora MySQL 1\.x clusters into Aurora MySQL 2\.01 or restore to Aurora MySQL 2\.01 from an Amazon S3 backup\. We plan to remove these restrictions in a later Aurora MySQL 2\.x release\.

## Comparison with Aurora MySQL 5\.6<a name="AuroraMySQL.Updates.20180206.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL 5\.6, but these features are currently not supported in Aurora MySQL 5\.7\.

+ Asynchronous key prefetch \(AKP\)\. For more information, see [Working with Asynchronous Key Prefetch in Amazon Aurora](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.

+ Hash joins\. For more information, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.

+ Native functions for synchronously invoking AWS Lambda functions\. You can asynchronously invoke AWS Lambda functions from Aurora MySQL 5\.7\. For more information, see [Invoking a Lambda Function with an Aurora MySQL Native Function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.

+ Scan batching\. For more information, see [Amazon Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.

+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

Currently, Aurora MySQL 2\.01 does not support features added in Aurora MySQL version 1\.16 and later\. For information about Aurora MySQL version 1\.16, see [Amazon Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.

The performance schema is disabled for Aurora MySQL 5\.7\.

## MySQL 5\.7\.12 compatibility<a name="AuroraMySQL.Updates.20180206.Compatibility"></a>

Amazon Aurora MySQL 2\.01 is wire\-compatible with MySQL 5\.7\.12 and includes features such as JSON support, spatial indexes, and generated columns\. Amazon Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

Amazon Aurora MySQL 2\.01 does not currently support the following MySQL 5\.7\.12 features:

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

## CLI differences between Aurora MySQL 2\.x and Aurora MySQL 1\.x<a name="AuroraMySQL.Updates.20180206.CLI"></a>

+ The engine name for Aurora MySQL 2\.x is `aurora-mysql`; the engine name for Aurora MySQL 1\.x continues to be `aurora`\.

+ The engine version for Aurora MySQL 2\.x is `5.7.12`; the engine version for Aurora MySQL 1\.x continues to be `5.6.10ann`\.

+ The default parameter group for Aurora MySQL 2\.x is `default.aurora-mysql5.7`; the default parameter group for Aurora MySQL 1\.x continues to be `default.aurora5.6`\.

+ The DB cluster parameter group family name for Aurora MySQL 2\.x is `aurora-mysql5.7`; the DB cluster parameter group family name for Aurora MySQL 1\.x continues to be `aurora5.6`\.

Refer to the Aurora documentation for the full set of CLI commands and differences between Aurora MySQL 2\.x and Aurora MySQL 1\.x\.