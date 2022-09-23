# Configuring an Amazon RDS DB instance<a name="CHAP_RDS_Configuring"></a>

This section shows how to set up your Amazon RDS DB instance\. Before creating a DB instance, decide on the DB instance class that will run the DB instance\. Also, decide where the DB instance will run by choosing an AWS Region\. Next, create the DB instance\.

You can configure a DB instance with an option group and a DB parameter group\.
+ An *option group* specifies features, called options, that are available for a particular Amazon RDS DB instance\.
+ A *DB parameter group* acts as a container for engine configuration values that are applied to one or more DB instances\.

The options and parameters that are available depend on the DB engine and DB engine version\. You can specify an option group and a DB parameter group when you create a DB instance\. You can also modify a DB instance to specify them\.

**Topics**
+ [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)
+ [Creating a Multi\-AZ DB cluster](create-multi-az-db-cluster.md)
+ [Creating Amazon RDS resources with AWS CloudFormation](creating-resources-with-cloudformation.md)
+ [Connecting to an Amazon RDS DB instance](CHAP_CommonTasks.Connect.md)
+ [Working with option groups](USER_WorkingWithOptionGroups.md)
+ [Working with parameter groups](USER_WorkingWithParamGroups.md)