# Amazon Aurora MySQL Database Engine Updates 2018\-03\-23<a name="AuroraMySQL.Updates.1171"></a>

**Version:** 1\.17\.1

Amazon Aurora MySQL 1\.17\.1 is generally available\. All new database clusters, including those restored from snapshots, will be created in Aurora MySQL v1\.17\.1\. You have the option, but are not required, to upgrade existing database clusters to Aurora MySQL v1\.17\.1\. If you wish to create new DB clusters in Aurora MySQL v1\.15\.1, Aurora MySQL 1\.16, or Aurora MySQL 1\.17, you can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. 

With version 1\.17\.1 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. This release fixes some known engine issues as well as regressions\. 

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
There is an issue in the latest version of the Aurora MySQL engine\. After upgrading to 1\.17\.1, the engine version is reported incorrectly as `1.17`\. If you upgraded to 1\.17\.1, you can confirm the upgrade by checking the **Maintenance** column for the DB cluster in the AWS Management Console\. If it displays `none`, then the engine is upgraded to 1\.17\.1\.

## Improvements<a name="AuroraMySQL.Updates.1171.Improvements"></a>
+ Fixed an issue in binary log recovery that resulted in longer recovery times for situations with large binary log index files which can happen if binary logs rotate very often\.
+ Fixed an issue in the query optimizer that generated an inefficient query plan for partitioned tables\.
+ Fixed an issue in the query optimizer due to which a range query resulted in a restart of the database engine\.