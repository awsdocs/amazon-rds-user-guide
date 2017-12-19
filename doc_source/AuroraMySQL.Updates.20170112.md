# Amazon Aurora MySQL Database Engine Updates: 2017\-01\-12<a name="AuroraMySQL.Updates.20170112"></a>

**Version:** 1\.10\.1

Version 1\.10\.1 of Amazon Aurora MySQL is an opt\-in version and is not used to patch your database instances\. It is available for creating new Aurora instances and for upgrading existing instances\. You can apply the patch by choosing a cluster in the [Amazon RDS console](https://console.aws.amazon.com/rds/), choosing **Cluster Actions**, and then choosing **Upgrade Now**\. Patching requires a database restart with downtime typically lasting 5\-30 seconds, after which you can resume using your DB clusters\. This patch is using a cluster patching model where all nodes in an Aurora cluster are patched at the same time\.

## New Features<a name="AuroraMySQL.Updates.20170112.New"></a>

+ **Advanced Auditing** – Amazon Aurora MySQL provides a high\-performance Advanced Auditing feature, which you can use to audit database activity\. For more information about enabling and using Advanced Auditing, see [Using Advanced Auditing with an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Auditing.md)\.

## Improvements<a name="AuroraMySQL.Updates.20170112.Improvements"></a>

+ Fixed an issue with spatial indexing when creating a column and adding an index on it in the same statement\.

+ Fixed an issue where spatial statistics aren't persisted across DB cluster restart\.