# Overview of Amazon RDS Blue/Green Deployments<a name="blue-green-deployments-overview"></a>

By using Amazon RDS Blue/Green Deployments, you can create a blue/green deployment for managed database changes\. A *blue/green deployment* creates a staging environment that copies the production environment\. In a blue/green deployment, the *blue environment* is the current production environment\. The *green environment* is the staging environment\. The staging environment stays in sync with the current production environment using logical replication\.

You can make changes to the RDS DB instances in the green environment without affecting production workloads\. For example, you can upgrade the major or minor DB engine version, change database parameters, or make schema changes in the staging environment\. You can thoroughly test changes in the green environment\. When ready, you can *switch over* the environments to promote the green environment to be the new production environment\. The switchover typically takes under a minute with no data loss and no need for application changes\.

Because the green environment is a copy of the topology of the production environment, the green environment includes the features used by the DB instance\. These features include the read replicas, the storage configuration, DB snapshots, automated backups, Performance Insights, and Enhanced Monitoring\. If the blue DB instance is a Multi\-AZ DB instance deployment, then the green DB instance is also a Multi\-AZ DB instance deployment\.

**Note**  
Currently, blue/green deployments are supported only for RDS for MariaDB and RDS for MySQL\. 

**Topics**
+ [Benefits of using Amazon RDS Blue/Green Deployments](#blue-green-deployments-benefits)
+ [Workflow of a blue/green deployment](#blue-green-deployments-major-steps)
+ [Authorizing access to blue/green deployment operations](#blue-green-deployments-authorizing-access)
+ [Considerations for blue/green deployments](#blue-green-deployments-considerations)
+ [Best practices for blue/green deployments](#blue-green-deployments-best-practices)
+ [Region and version availability](#blue-green-deployments-region-version-availability)
+ [Limitations for blue/green deployments](#blue-green-deployments-limitations)

## Benefits of using Amazon RDS Blue/Green Deployments<a name="blue-green-deployments-benefits"></a>

By using Amazon RDS Blue/Green Deployments, you can stay current on security patches, improve database performance, and adopt newer database features with short, predictable downtime\. Blue/green deployments reduce the risks and downtime for database updates, such as major or minor engine version upgrades\.

Blue/green deployments provide the following benefits:
+ Easily create a production\-ready staging environment\.
+ Automatically replicate database changes from the production environment to the staging environment\.
+ Test database changes in a safe staging environment without affecting the production environment\.
+ Stay current with database patches and system updates\.
+ Implement and test newer database features\.
+ Switch over your staging environment to be the new production environment without changes to your application\.
+ Safely switch over through the use of built\-in switchover guardrails\.
+ Eliminate data loss during switchover\.
+ Switch over quickly, typically under a minute depending on your workload\.

## Workflow of a blue/green deployment<a name="blue-green-deployments-major-steps"></a>

Complete the following major steps when you use a blue/green deployment for database updates\.

1. Identify a production environment that requires updates\.

   For example, the production environment in this image has a Multi\-AZ DB instance deployment \(mydb1\) and a read replica \(mydb2\)\.  
![\[Production (blue) environment in a blue/green deployment\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/blue-green-deployment-blue-environment.png)

1. Create the blue/green deployment\. For instructions, see [Creating a blue/green deployment](blue-green-deployments-creating.md)\.

   The following image shows an example of a blue/green deployment of the production environment from step 1\. While creating the blue/green deployment, RDS copies the complete topology and configuration of the primary DB instance to create the green environment\. The copied DB instance names are appended with `-green-random-characters`\. The staging environment in the image contains a Multi\-AZ DB instance deployment \(mydb1\-green\-**abc123**\) and a read replica \(mydb2\-green\-**abc123**\)\.  
![\[Blue/green deployment\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/blue-green-deployment.png)

   When you create the blue/green deployment, you can upgrade your DB engine version and specify a different DB parameter group for the DB instances in the green environment\. RDS also configures logical replication from the primary DB instance in the blue environment to the primary DB instance in the green environment\.

   After you create the blue/green deployment, the DB instance in the green environment is read\-only by default\.

1. Make additional changes to the staging environment, if required\.

   For example, you might make schema changes to your database or change the DB instance class used by one or more DB instances in the green environment\.

   For information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

1. Test your staging environment\.

   During testing, we recommend that you keep your databases in the green environment read only\. We recommend that you enable write operations on the green environment with caution because they can result in replication conflicts in the green environment\. They can also result in unintended data in the production databases after switchover\.

1. When ready, switch over to promote the staging environment to be the new production environment\. For instructions, see [Switching a blue/green deployment](blue-green-deployments-switching.md)\.

   The switchover results in downtime\. The downtime is usually under one minute, but it can be longer depending on your workload\.

   The following image shows the DB instances after the switchover\.  
![\[DB instances after switching over a blue/green deployment\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/blue-green-deployment-switchover.png)

   After the switchover, the DB instances that were in the green environment become the new production DB instances\. The names and endpoints in the current production environment are assigned to the newly promoted production environment, requiring no changes to your application\. As a result, your production traffic now flows to the new production environment\. The DB instances in the previous blue environment are renamed by appending `-oldn` to the current name, where `n` is a number\. For example, assume the name of the DB instance in the blue environment is `mydb1`\. After switchover, the DB instance name might be `mydb1-old1`\.

   In the example in the image, the following changes occur during switchover:
   + The green environment Multi\-AZ DB instance deployment named `mydb1-green-abc123` becomes the production Multi\-AZ DB instance deployment named `mydb1`\.
   + The green environment read replica named `mydb2-green-abc123` becomes the production read replica `mydb2`\.
   + The blue environment Multi\-AZ DB instance deployment named `mydb1` becomes `mydb1-old1`\.
   + The blue environment read replica named `mydb2` becomes `mydb2-old1`\.

1. If you no longer need a blue/green deployment, you can delete it\. For instructions, see [Deleting a blue/green deployment](blue-green-deployments-deleting.md)\.

   After switchover, the previous production environment isn't deleted so that you can use it for regression testing, if necessary\.

## Authorizing access to blue/green deployment operations<a name="blue-green-deployments-authorizing-access"></a>

Users must have the required permissions to perform operations related to blue/green deployments\. You can create IAM policies that grant users and roles permission to perform specific API operations on the specified resources they need\. You can then attach those policies to the IAM permission sets or roles that require those permissions\. For more information, see [Identity and access management for Amazon RDS](UsingWithRDS.IAM.md)\.

The user who creates a blue/green deployment must have permissions to perform the following RDS operations:
+ `rds:AddTagsToResource` 
+ `rds:CreateDBInstanceReadReplica` 

The user who switches over a blue/green deployment must have permissions to perform the following RDS operations:
+ `rds:ModifyDBInstance` 
+ `rds:PromoteDBInstance` 

The user who deletes a blue/green deployment must have permissions to perform the following RDS operation:
+ `rds:DeleteDBInstance` 

## Considerations for blue/green deployments<a name="blue-green-deployments-considerations"></a>

Amazon RDS tracks resources in blue/green deployments with the `DbiResourceId` and \(resource ID\) of each resource\. This resource ID is an AWS Region\-unique, immutable identifier for the resource\.

The *resource* ID is separate from the DB ***instance* ID:

![\[Create blue/green deployment\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/blue-green-deployment-resource-id.png)

The name \(instance ID\) of a resource changes when you switch over a blue/green deployment, but each resource keeps the same resource ID\. For example, a DB instance identifier might be `mydb` in the blue environment\. After switchover, the same DB instance might be renamed to `mydb-old1`\. However, the resource ID of the DB instance doesn't change during switchover\. So, when the green resources are promoted to be the new production resources, their resource IDs don't match the blue resource IDs that were previously in production\.

After switching over a blue/green deployment, consider updating the resource IDs to those of the newly promoted production resources for integrated features and services that you used with the production resources\. Specifically, consider the following updates:
+ If you perform filtering using the RDS API and resource IDs, adjust the resource IDs used in filtering after switchover\.
+ If you use CloudTrail for auditing resources, adjust the consumers of the CloudTrail to track the new resource IDs after switchover\. For more information, see [Monitoring Amazon RDS API calls in AWS CloudTrail](logging-using-cloudtrail.md)\.
+ If you use the Performance Insights API, adjust the resource IDs in calls to the API after switchover\. For more information, see [Monitoring DB load with Performance Insights on Amazon RDS](USER_PerfInsights.md)\.

  You can monitor a database with the same name after switchover, but it doesn't contain the data from before the switchover\.
+ If you use resource IDs in IAM policies, make sure you add the resource IDs of the newly promoted resources when necessary\. For more information, see [Identity and access management for Amazon RDS](UsingWithRDS.IAM.md)\.
+ If you use AWS Backup to manage automated backups of resources in a blue/green deployment, adjust the resource IDs used by AWS Backup after switchover\. For more information, see [Using AWS Backup to manage automated backups](USER_WorkingWithAutomatedBackups.md#AutomatedBackups.AWSBackup)\.
+ If you want to restore a manual or automated DB snapshot for a DB instance that was part of a blue/green deployment, make sure you restore the correct DB snapshot by examining the time when the snapshot was taken\. For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.
+ If you want to describe a previous blue environment DB instance automated backup or restore it to a point in time, use the resource ID for the operation\.

  Because the name of the DB instance changes during switchover, you can't use its previous name for `DescribeDBInstanceAutomatedBackups` or `RestoreDBInstanceToPointInTime` operations\.

  For more information, see [Restoring a DB instance to a specified time](USER_PIT.md)\.
+ When you add a read replica to a DB instance in the green environment of a blue/green deployment, the new read replica won't replace a read replica in the blue environment when you switch over\. However, the new read replica is retained in the new production environment after switchover\.
+ When you delete a DB instance in the green environment of a blue/green deployment, you can't create a new DB instance to replace it in the blue/green deployment\.

  If you create a new DB instance with the same name and Amazon Resource Name \(ARN\) as the deleted DB instance, it has a different `DbiResourceId`, so it isn't part of the green environment\.

  The following behavior results if you delete a DB instance in the green environment:
  + If the DB instance in the blue environment with the same name exists, it won't be switched over to the DB instance in the green environment\. This DB instance won't be renamed by adding `-oldn` to the DB instance name\.
  + Any application that points to the DB instance in the blue environment continues to use the same DB instance after switchover\.

  The same behavior applies to DB instances and read replicas\.

## Best practices for blue/green deployments<a name="blue-green-deployments-best-practices"></a>

The following are best practices for blue/green deployments:
+ Avoid using non\-transactional storage engines, such as MyISAM, that aren't optimized for replication\.
+ Optimize read replicas for binary log replication\.

  For example, if your DB engine version supports it, consider using GTID replication, parallel replication, and crash\-safe replication in your production environment before deploying your blue/green deployment\. These options promote consistency and durability of your data before you switch over your blue/green deployment\. For more information about GTID replication for read replicas, see [Using GTID\-based replication for Amazon RDS for MySQL](mysql-replication-gtid.md)\.
+ Thoroughly test the DB instances in the green environment before switching over\.
+ Keep your databases in the green environment read only\. We recommend that you enable write operations on the green environment with caution because they can result in replication conflicts in the green environment\. They can also result in unintended data in the production databases after switchover\.
+ Identify the best time for the switchover\.

  During the switchover, writes are cut off from databases in both environments\. Identify a time when traﬃc is lowest on your production environment\. Long\-running transactions, such as active DDLs, can increase your switchover time, resulting in longer downtime for your production workloads\.
+ When using a blue/green deployment to implement schema changes, make only replication\-compatible changes\.

  For example, you can add new columns at the end of a table, create indexes, or drop indexes without disrupting replication from the blue deployment to the green deployment\. However, schema changes, such as renaming columns or renaming tables, break binary log replication to the green deployment\.

  For more information about replication\-compatible changes, see [Replication with Differing Table Definitions on Source and Replica](https://dev.mysql.com/doc/refman/8.0/en/replication-features-differing-tables.html) in the MySQL documentation\.
+ After you create the blue/green deployment, handle lazy loading if necessary\. Make sure data loading is complete before switching over\. For more information, see [Handling lazy loading when you create a blue/green deployment](blue-green-deployments-creating.md#blue-green-deployments-creating-lazy-loading)\.

## Region and version availability<a name="blue-green-deployments-region-version-availability"></a>

Feature availability and support varies across specific versions of each database engine, and across AWS Regions\. For more information on version and Region availability with Amazon RDS Blue/Green Deployments, see [Blue/Green Deployments](Concepts.RDS_Fea_Regions_DB-eng.Feature.BlueGreenDeployments.md)\. 

## Limitations for blue/green deployments<a name="blue-green-deployments-limitations"></a>

The following limitations apply to blue/green deployments:
+ MySQL version 8\.0\.13 and lower have a [community bug](https://bugs.mysql.com/bug.php?id=93901) that prevents RDS from supporting them for blue/green deployments\.
+ Blue/green deployments aren't supported for the following features:
  + Amazon RDS Proxy
  + Cascading read replicas
  + Cross\-Region read replicas
  + AWS CloudFormation
  + Multi\-AZ DB cluster deployments

    Blue/green deployments are supported for Multi\-AZ DB instance deployments\. For more information about Multi\-AZ deployments, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)\.
+ The following are limitations for changes in a blue/green deployment:
  + You can't change an unencrypted DB instance into an encrypted DB instance\.
  + You can't change an encrypted DB instance into an unencrypted DB instance\.
  + You can't change a blue environment DB instance to a higher engine version than its corresponding green environment DB instance\.
  + The resources in the blue environment and green environment must be in the same AWS account\.
  + During switchover, the blue primary DB instance can't be the target of external replication\.
  + If the source database is associated with a custom option group, you can't specify a major version upgrade when you create the blue/green deployment\.

    In this case, you can create a blue/green deployment without specifying a major version upgrade\. Then, you can upgrade the database in the green environment\. For more information, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.