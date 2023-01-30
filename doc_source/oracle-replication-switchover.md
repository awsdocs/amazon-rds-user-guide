# Performing an Oracle Data Guard switchover<a name="oracle-replication-switchover"></a>

In an Oracle Data Guard environment, a primary database supports one or more standby databases\. You can perform a managed, switchover\-based role transition from a primary database to a standby database\.

**Topics**
+ [Overview of Oracle Data Guard switchover](#oracle-replication-switchover.overview)
+ [Preparing for the Oracle Data Guard switchover](#oracle-switchover.preparing)
+ [Initiating the Oracle Data Guard switchover](#oracle-switchover.initiating)
+ [Monitoring the Oracle Data Guard switchover](#oracle-switchover.monitoring)

## Overview of Oracle Data Guard switchover<a name="oracle-replication-switchover.overview"></a>

A *switchover* is a role reversal between a primary database and a standby database\. During a switchover, the original primary database transitions to a standby role, while the original standby database transitions to the primary role\.

![\[Switch over a standby instance to make it the primary instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/read-replica-switchover.png)

Amazon RDS supports a fully managed, switchover\-based role transition for Oracle Database replicas\. The replicas can reside in separate AWS Regions or in different Availability Zones \(AZs\) of a single Region\. You can only initiate a switchover to a standby database that is mounted or open read\-only\.

**Topics**
+ [Benefits of Oracle Data Guard switchover](#oracle-replication-switchover.overview.benefits)
+ [Supported Oracle Database versions](#oracle-replication-switchover.overview.engine-support)
+ [AWS Region support](#oracle-replication-switchover.overview.Region)
+ [Cost of Oracle Data Guard switchover](#oracle-replication-switchover.overview.cost)
+ [How Oracle Data Guard switchover works](#oracle-replication-switchover.overview.how-it-works)

### Benefits of Oracle Data Guard switchover<a name="oracle-replication-switchover.overview.benefits"></a>

Just as for RDS for Oracle read replicas, a managed switchover relies on Oracle Data Guard\. The operation is designed to have zero data loss\. Amazon RDS automates the following aspects of the switchover:
+ Reverses the roles of your primary database and specified standby database, putting the new standby database in the same state \(mounted or read\-only\) as the original standby
+ Ensures data consistency
+ Maintains your replication configuration after the transition
+ Supports repeated reversals, allowing your new standby database to return to its original primary role

### Supported Oracle Database versions<a name="oracle-replication-switchover.overview.engine-support"></a>

Oracle Data Guard switchover is supported for the following releases:
+ Oracle Database 19c
+ Oracle Database 12c Release 2 \(12\.2\)
+ Oracle Database 12c Release 1 \(12\.1\) using PSU 12\.1\.0\.2\.v10 or higher

### AWS Region support<a name="oracle-replication-switchover.overview.Region"></a>

Oracle Data Guard switchover is available in the following AWS Regions:
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Osaka\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Canada \(Central\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)
+ Europe \(Paris\)
+ Europe \(Stockholm\)
+ South America \(São Paulo\)
+ US East \(N\. Virginia\)
+ US East \(Ohio\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

### Cost of Oracle Data Guard switchover<a name="oracle-replication-switchover.overview.cost"></a>

The Oracle Data Guard switchover feature doesn't incur additional costs\. Oracle Database Enterprise Edition includes support for standby databases in mounted mode\. To open standby databases in read\-only mode, you need the Oracle Active Data Guard option\.

### How Oracle Data Guard switchover works<a name="oracle-replication-switchover.overview.how-it-works"></a>

Oracle Data Guard switchover is a fully managed operation\. You initiate the switchover for a standby database by issuing the CLI command `switchover-read-replica`\. Then Amazon RDS modifies the primary and standby roles in your replication configuration\.

The *original standby* and *original primary* are the roles that exist before the switchover\. The *new standby* and *new primary* are the roles that exist after the switchover\. A *bystander replica* is a replica database that serves as a standby database in the Oracle Data Guard environment but is not switching roles\.

**Topics**
+ [Stages of the Oracle Data Guard switchover](#oracle-replication-switchover.overview.how-it-works.during-switchover)
+ [After the Oracle Data Guard switchover](#oracle-replication-switchover.overview.how-it-works.after-switchover)

#### Stages of the Oracle Data Guard switchover<a name="oracle-replication-switchover.overview.how-it-works.during-switchover"></a>

To perform the switchover, Amazon RDS must take the following steps:

1. Block new transactions on the original primary database\. During the switchover, Amazon RDS interrupts replication for all databases in your Oracle Data Guard configuration\. During the switchover, the original primary database can't process write requests\.

1. Ship unapplied transactions to the original standby database, and apply them\.

1. Restart the new standby database in read\-only or mounted mode\. The mode depends on the open state of the original standby database before the switchover\.

1. Open the new primary database in read/write mode\.

#### After the Oracle Data Guard switchover<a name="oracle-replication-switchover.overview.how-it-works.after-switchover"></a>

Amazon RDS switches the roles of the primary and standby database\. You are responsible for reconnecting your application and performing any other desired configuration\.

**Topics**
+ [Success criteria](#oracle-replication-switchover.overview.how-it-works.after-switchover.success)
+ [Connection to the new primary database](#oracle-replication-switchover.overview.how-it-works.after-switchover.connection)
+ [Configuration of the new primary database](#oracle-replication-switchover.overview.how-it-works.after-switchover.success.configuration)

##### Success criteria<a name="oracle-replication-switchover.overview.how-it-works.after-switchover.success"></a>

The Oracle Data Guard switchover is successful when the original standby database does the following:
+ Transitions to its role as new primary database
+ Completes its reconfiguration

To limit downtime, your new primary database becomes active as soon as possible\. Because Amazon RDS configures bystander replicas asynchronously, these replicas might become active after the original primary database\.

##### Connection to the new primary database<a name="oracle-replication-switchover.overview.how-it-works.after-switchover.connection"></a>

Amazon RDS won't propagate your current database connections to the new primary database after the switchover\. After the Oracle Data Guard switchover completes, reconnect your application to the new primary database\.

##### Configuration of the new primary database<a name="oracle-replication-switchover.overview.how-it-works.after-switchover.success.configuration"></a>

To perform a switchover to the new primary database, Amazon RDS changes the mode of the original standby database to open\. The change in role is the only change to the database\. Amazon RDS doesn't set up features such as Multi\-AZ replication\.

If you perform a switchover to a cross\-Region replica with different options, the new primary database keeps its own options\. Amazon RDS won't migrate the options on the original primary database\. If the original primary database had options such as SSL, NNE, OEM, and OEM\_AGENT, Amazon RDS doesn't propagate them to the new primary database\.

## Preparing for the Oracle Data Guard switchover<a name="oracle-switchover.preparing"></a>

Before initiating the Oracle Data Guard switchover, make sure that your replication environment meets the following requirements:
+ The original standby database is mounted or open read\-only\.
+ Automatic backups are enabled on the original standby database\.
+ The original primary database and the original standby database are in an available state\.
+ The original primary database and the original standby database have no pending maintenance actions\.
+ The original standby database is in the replicating state\.
+ You aren't attempting to initiate a switchover when either the primary database or standby database is currently in a switchover lifecycle\. If a replica database is reconfiguring after a switchover, Amazon RDS prevents you from initiating another switchover\.
**Note**  
A *bystander replica* is a replica in the Oracle Data Guard configuration that isn't the target of the switchover\. Bystander replicas can be in any state during the switchover\.
+ The original standby database has a configuration that is as close as desired to the original primary database\. Assume a scenario where the original primary and original standby databases have different options\. After the switchover completes, Amazon RDS doesn't automatically reconfigure the new primary database to have the same options as the original primary database\.
+ You configure your desired Multi\-AZ deployment before initiating a switchover\. Amazon RDS doesn't manage Multi\-AZ as part of the switchover\. The Multi\-AZ deployment remains as it is\.

  Assume that db\_maz is the primary database in a Multi\-AZ deployment, and db\_saz is a Single\-AZ replica\. You initiate a switchover from db\_maz to db\_saz\. Afterward, db\_maz is a Multi\-AZ replica database, and db\_saz is a Single\-AZ primary database\. The new primary database is now unprotected by a Multi\-AZ deployment\.
+ In preparation for a cross\-Region switchover, the primary database doesn't use the same option group as a DB instance outside of the replication configuration\. For a cross\-Region switchover to succeed, the current primary database and its read replicas must be the only DB instances to use the option group of the current primary database\. Otherwise, Amazon RDS prevents the switchover\.

## Initiating the Oracle Data Guard switchover<a name="oracle-switchover.initiating"></a>

You can switch over an RDS for Oracle read replica to the primary role, and the former primary DB instance to a replica role\.

### Console<a name="USER_ReadRepl.Promote.Console"></a>

**To switch over an Oracle read replica to the primary DB role**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the Amazon RDS console, choose **Databases**\.

   The **Databases** pane appears\. Each read replica shows **Replica** in the **Role** column\.

1. Choose the read replica that you want to switch over to the primary role\.

1. For **Actions**, choose **Switch over replica**\.

1. Choose **I acknowledge**\. Then choose **Switch over replica**\.

1. On the **Databases** page, monitor the progress of the switchover\.  
![\[Monitor the progress of the Oracle Data Guard switchover.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-switchover-progress.png)

   When the switchover completes, the role of the switchover target changes from **Replica** to **Source**\.  
![\[The source and replica databases change roles.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-switchover-complete.png)

### AWS CLI<a name="USER_ReadRepl.Promote.CLI"></a>

To switch over an Oracle replica to the primary DB role, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/switchover-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/switchover-read-replica.html) command\. The following examples make the Oracle replica named *replica\-to\-be\-made\-primary* into the new primary database\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds switchover-read-replica \
    --db-instance-identifier replica-to-be-made-primary
```
For Windows:  

```
aws rds switchover-read-replica ^
    --db-instance-identifier replica-to-be-made-primary
```

### RDS API<a name="USER_ReadRepl.Promote.API"></a>

To switch over an Oracle replica to the primary DB role, call the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_SwitchoverReadReplica.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_SwitchoverReadReplica.html) operation with the required parameter `DBInstanceIdentifier`\. This parameter specifies the name of the Oracle replica that you want to assume the primary DB role\.

## Monitoring the Oracle Data Guard switchover<a name="oracle-switchover.monitoring"></a>

To check the status of your instances, use the AWS CLI command `describe-db-instances`\. The following command checks the status of the DB instance *orcl2*\. This database was a standby database before the switchover, but is the new primary database after the switchover\.

```
aws rds describe-db-instances \
    --db-instance-identifier orcl2
```

To confirm that the switchover completed successfully, query `V$DATABASE.OPEN_MODE`\. Check that the value for the new primary database is `READ WRITE`\.

```
SELECT OPEN_MODE FROM V$DATABASE;
```

To look for switchover\-related events, use the AWS CLI command `describe-events`\. The following example looks for events on the *orcl2* instance\.

```
aws rds describe-events \
    --source-identifier orcl2 \
    --source-type db-instance
```