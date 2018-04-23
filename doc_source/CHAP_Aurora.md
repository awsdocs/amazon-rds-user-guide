# Amazon Aurora on Amazon RDS<a name="CHAP_Aurora"></a>

Amazon Aurora \(Aurora\) is a fully managed, MySQL\- and PostgreSQL\-compatible, relational database engine\. It combines the speed and reliability of high\-end commercial databases with the simplicity and cost\-effectiveness of open\-source databases\.

Aurora makes it simple and cost\-effective to set up, operate, and scale your MySQL and PostgreSQL deployments, freeing you to focus on your business and applications\. Amazon RDS provides administration for Aurora by handling routine database tasks such as provisioning, patching, backup, recovery, failure detection, and repair\. Amazon RDS also provides push\-button migration tools to convert your existing Amazon RDS for MySQL and Amazon RDS for PostgreSQL applications to Aurora\.

Amazon Aurora is a drop\-in replacement for MySQL and PostgreSQL\. The code, tools, and applications you use today with your existing MySQL and PostgreSQL databases can be used with Amazon Aurora\. 

Before using Amazon Aurora, you should complete the steps in [Setting Up for Amazon RDS](CHAP_SettingUp.md), and then review the concepts and features of Aurora in [Overview of Amazon Aurora](Aurora.Overview.md)

## Common Management Tasks for Amazon Aurora<a name="Aurora.CommonTasks"></a>

The following are the common management tasks you perform for an Amazon Aurora DB cluster, with links to relevant documentation for each task\.


| Task Area | Relevant Documentation | 
| --- | --- | 
|  **Setting up Amazon RDS for first\-time use** Set up Amazon RDS so that you can use Amazon Aurora\.  |  [Setting Up for Amazon RDS](CHAP_SettingUp.md)  | 
|  **Understanding Amazon Aurora** Learn about basic Amazon Aurora concepts and features, such as DB clusters, primary instances, and Aurora Replicas\.  |  [Overview of Amazon Aurora](Aurora.Overview.md)  | 
|  **Create an Amazon Aurora DB Cluster** Create Amazon Aurora DB clusters and Aurora Replicas using the AWS Management Console or the AWS Command Line Interface \(AWS CLI\)\.  |  [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)  | 
|  **Migrating from another database or RDS DB instance to an Amazon Aurora DB cluster** You have several options for migrating data from an on\-premises database or other RDS DB instance to your Aurora DB cluster\.  |  [Migrating Data to an Amazon Aurora DB Cluster](Aurora.Migrate.md)  | 
|  **Managing access permissions to Amazon RDS resources and databases** Learn how to manage access to your Aurora DB clusters and other Amazon RDS capabilities\.  |  [Security in Amazon RDS](UsingWithRDS.md) [Overview of Managing Access Permissions to Your Amazon RDS Resources](UsingWithRDS.IAM.AccessControl.Overview.md)  | 
|  **Configuring specific Amazon Aurora database parameters** If your DB cluster is going to require specific database parameters, you can create a DB parameter group and a DB cluster parameter group before you create the DB cluster\.  |  [Amazon Aurora DB Cluster and DB Instance Parameters](Aurora.Managing.md#Aurora.Managing.ParameterGroups) [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)  | 
|  **Connecting to your Amazon Aurora DB cluster** Learn how to connect to your Aurora DB cluster using a standard client application\.  |  [Aurora Endpoints](Aurora.Overview.md#Aurora.Overview.Endpoints) [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)  | 
|  **Integrating your Amazon Aurora DB cluster with other AWS services** Learn how to extend your Aurora DB cluster to use additional capabilities in the AWS Cloud\.  |  [Integrating Aurora with Other AWS Services](Aurora.Integrating.md)  | 
|  **Configuring database backup and restore** Amazon Aurora automatically backs up your data\. You can configure how long Aurora keeps backups for, and you can restore from a DB cluster snapshot, from a specific point in time, or from other files\.  |  [Backing Up and Restoring an Aurora DB Cluster](Aurora.Managing.md#Aurora.Managing.Backups) [Migrating Data to an Amazon Aurora DB Cluster](Aurora.Migrate.md)  | 
|  **Monitoring an Amazon Aurora DB cluster** You can monitor your Aurora DB cluster by using Amazon CloudWatch, RDS events, and Enhanced Monitoring \(CloudWatch Logs\)\.   |  [Monitoring an Amazon Aurora DB Cluster](Aurora.Monitoring.md) [Monitoring Amazon RDS](CHAP_Monitoring.md)  | 
|  **Updating the database engine or operating system for your Amazon Aurora DB cluster** Learn how to manage database engine and operating system updates for your Aurora DB cluster\.   |  [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md) [Amazon Aurora PostgreSQL Database Engine Updates](AuroraPostgreSQL.Updates.md) [Maintaining an Amazon RDS DB Instance](USER_UpgradeDBInstance.Maintenance.md)  | 
|  **Set up replication with an Amazon Aurora DB cluster** Learn how to replicate data between your Amazon Aurora DB cluster and an on\-premises database, an Aurora DB cluster in a different AWS Region, or another RDS DB instance\.  |  [Replication with Amazon Aurora](Aurora.Replication.md)  | 
|  **Copy or share an Amazon Aurora DB cluster snapshot** Learn how to copy an Aurora DB cluster snapshot to the same AWS Region, or a different AWS Region\. Learn how to share an Aurora DB cluster snapshot with other AWS accounts\.   |  [Copying a DB Snapshot or DB Cluster Snapshot](USER_CopySnapshot.md) [Sharing a DB Snapshot or DB Cluster Snapshot](USER_ShareSnapshot.md)  | 