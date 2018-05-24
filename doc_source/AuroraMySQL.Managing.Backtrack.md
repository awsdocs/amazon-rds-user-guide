# Backtracking an Aurora DB Cluster<a name="AuroraMySQL.Managing.Backtrack"></a>

With Amazon Aurora with MySQL compatibility, you can backtrack a DB cluster to a specific time, without restoring data from a backup\.

## Overview of Backtracking<a name="AuroraMySQL.Managing.Backtrack.Overview"></a>

Backtracking "rewinds" the DB cluster to the time you specify\. Backtracking is not a replacement for backing up your DB cluster so that you can restore it to a point in time\. However, backtracking provides the following advantages over traditional backup and restore:
+ You can easily undo mistakes\. If you mistakenly perform a destructive action, such as a DELETE without a WHERE clause, you can backtrack the DB cluster to a time before the destructive action with minimal interruption of service\.
+ You can backtrack a DB cluster quickly\. Restoring a DB cluster to a point in time launches a new DB cluster and restores it from backup data or a DB cluster snapshot, which can take hours\. Backtracking a DB cluster doesn't require a new DB cluster and rewinds the DB cluster in minutes\.
+ You can explore earlier data changes\. You can repeatedly backtrack a DB cluster back and forth in time to help determine when a particular data change occurred\. For example, you can backtrack a DB cluster three hours and then backtrack forward in time one hour\. In this case, the backtrack time is two hours before the original time\.

**Note**  
For information about restoring a DB cluster to a point in time, see [Backing Up and Restoring an Aurora DB Cluster](Aurora.Managing.md#Aurora.Managing.Backups)\.

### Backtrack Window<a name="AuroraMySQL.Managing.Backtrack.Overview.BacktrackWindow"></a>

With backtracking, there is a target backtrack window and an actual backtrack window:
+ The target backtrack window is the amount of time you want to be able to backtrack your DB cluster\. When you enable backtracking, you specify a target backtrack window\. For example, you might specify a target backtrack window of 24 hours if you want to be able to backtrack the DB cluster one day\.
+ The actual backtrack window is the actual amount of time you can backtrack your DB cluster, which can be smaller than the target backtrack window\. The actual backtrack window is based on your workload and the storage available for storing information about database changes, called *change records\.*

As you make updates to your Aurora DB cluster with backtracking enabled, you generate change records\. Aurora retains change records for the target backtrack window, and you pay an hourly rate for storing them\. Both the target backtrack window and the workload on your DB cluster determine the number of change records you store\. The workload is the number of changes you make to your DB cluster in a given amount of time\. If your workload is heavy, you store more change records in your backtrack window than you do if your workload is light\.

You can think of your target backtrack window as the goal for the maximum amount of time you want to be able to backtrack your DB cluster\. In most cases, you can backtrack the maximum amount of time that you specified\. However, in some cases, the DB cluster can't store enough change records to backtrack the maximum amount of time, and your actual backtrack window is smaller than your target\. Typically, the actual backtrack window is smaller than the target when you have extremely heavy workload on your DB cluster\. When your actual backtrack window is smaller than your target, we send you a notification\.

When backtracking is enabled for a DB cluster, and you delete a table stored in the DB cluster, Aurora keeps that table in the backtrack change records\. It does this so that you can revert back to a time before you deleted the table\. If you don't have enough space in your backtrack window to store the table, the table might be removed from the backtrack change records eventually\.

### Backtrack Time<a name="AuroraMySQL.Managing.Backtrack.Overview.BacktrackTime"></a>

Aurora always backtracks to a time that is consistent for the DB cluster\. Doing so eliminates the possibility of uncommitted transactions when the backtrack is complete\. When you specify a time for a backtrack, Aurora automatically chooses the nearest possible consistent time\. This approach means that the completed backtrack might not exactly match the time you specify, but you can determine the exact time for a backtrack by using the [describe\-db\-cluster\-backtracks ](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-backtracks.html) AWS CLI command\. For more information, see [Retrieving Existing Backtracks](#AuroraMySQL.Managing.Backtrack.Retrieving)\.

### Backtrack Limitations<a name="AuroraMySQL.Managing.Backtrack.Limitations"></a>

The following limitations apply to backtracking:
+ Backtracking is only available on DB clusters that were created with the Backtrack feature enabled\. You can enable the Backtrack feature when you create a new DB cluster, restore a snapshot of a DB cluster, or clone a DB cluster\. Currently, backtracking is not possible on DB clusters that were created with the Backtrack feature disabled\.
+ The limit for a backtrack window is 72 hours\.
+ Backtracking affects the entire DB cluster\. For example, you can't selectively backtrack a single table or a single data update\.
+ Backtracking is not supported with binary log \(binlog\) replication\. Cross\-region replication must be disabled before you can configure or use backtracking\.
+ You can't backtrack a database clone to a time before that database clone was created\. However, you can use the original database to backtrack to a time before the clone was created\. For more information about database cloning, see [Cloning Databases in an Aurora DB Cluster](Aurora.Managing.Clone.md)\.
+ Backtracking causes a brief DB instance disruption\. You must stop or pause your applications before starting a backtrack operation to ensure that there are no new read or write requests\. During the backtrack operation, Aurora pauses the database, closes any open connections, and drops any uncommitted reads and writes\. It then waits for the backtrack operation to complete\.
+ Backtracking is only supported for Aurora MySQL 5\.6\. It is not supported for Aurora MySQL 5\.7\. Because of this limitation, you can't restore a snapshot of an Aurora MySQL 5\.6 DB cluster with Backtrack enabled to Aurora MySQL 5\.7, and you can't perform point\-in\-time recovery on an Aurora MySQL 5\.6 DB cluster with Backtrack enabled to restore the DB cluster to Aurora MySQL 5\.7\.

## Configuring Backtracking<a name="AuroraMySQL.Managing.Backtrack.Configuring"></a>

To use the Backtrack feature, you must enable backtracking and specify a target backtrack window\. Otherwise, backtracking is disabled\.

For the target backtrack window, specify the amount of time that you want to be able to rewind your database using Backtrack\. Aurora tries to retain enough change records to support that window of time\.

### AWS Management Console<a name="AuroraMySQL.Managing.Backtrack.Configuring.Console"></a>

You can use the console to configure backtracking when you create a new DB cluster\. You can also modify a DB cluster to enable backtracking\.

**Topics**
+ [Configuring Backtracking with the Console When Creating a DB Cluster](#AuroraMySQL.Managing.Backtrack.Configuring.Console.Creating)
+ [Configuring Backtrack with the Console When Modifying a DB Cluster](#AuroraMySQL.Managing.Backtrack.Configuring.Console.Modifying)

#### Configuring Backtracking with the Console When Creating a DB Cluster<a name="AuroraMySQL.Managing.Backtrack.Configuring.Console.Creating"></a>

When you create a new Aurora MySQL DB cluster, backtracking is configured when you choose **Enable Backtrack** and specify a **Target Backtrack window** value that is greater than zero in the **Backtrack** section\.

To create a DB cluster, follow the instructions in [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\. The following image shows the **Backtrack** section\.

![\[Enable Backtrack during DB cluster creation with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-backtrack-create.png)

When you create a new DB cluster, Aurora has no data for the DB cluster's workload\. So it can't estimate a cost specifically for the new DB cluster\. Instead, the console presents a typical user cost for the specified target backtrack window based on a typical workload\. The typical cost is meant to provide a general reference for the cost of the Backtrack feature\.

**Important**  
Your actual cost might not match the typical cost, because your actual cost is based on your DB cluster's workload\.

#### Configuring Backtrack with the Console When Modifying a DB Cluster<a name="AuroraMySQL.Managing.Backtrack.Configuring.Console.Modifying"></a>

You can modify backtracking for a DB cluster using the console\.

**To modify backtracking for a DB cluster using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Clusters**\.

1. Choose the cluster that you want to modify, and choose **Modify cluster** in **Actions**\.

1. If backtracking is disabled in the **Backtrack** section, choose **Enable Backtrack**\.
**Note**  
Currently, you can enable backtracking only for a DB cluster that was created with the Backtrack feature enabled\. The **Backtrack **section doesn't appear for a DB cluster that was created with the Backtrack feature disabled\.

1. For **Target Backtrack window**, modify the amount of time that you want to be able to backtrack\. The limit is 72 hours\.  
![\[Modify Backtrack with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-backtrack-modify.png)

   The console shows the estimated cost for the amount of time you specified based on the DB cluster's past workload:
   + If backtracking was disabled on the DB cluster, the cost estimate is based on the `VolumeWriteIOPS` metric for the DB cluster in Amazon CloudWatch\.
   + If backtracking was enabled previously on the DB cluster, the cost estimate is based on the `BacktrackChangeRecordsCreationRate` metric for the DB cluster in Amazon CloudWatch\.

1. Choose **Continue**\.

1. For **Scheduling of Modifications**, choose one of the following:
   + **Apply during the next scheduled maintenance window** – Wait to apply the **Target Backtrack window** modification until the next maintenance window\.
   + **Apply immediately** – Apply the **Target Backtrack window** modification as soon as possible\.

1. Choose **Modify Cluster**\.

### CLI<a name="AuroraMySQL.Managing.Backtrack.Configuring.CLI"></a>

When you create a new Aurora MySQL DB cluster using the [create\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) AWS CLI command, backtracking is configured when you specify a `--backtrack-window` value that is greater than zero\. The `--backtrack-window` value specifies the target backtrack window\. For more information, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

You can also specify the `--backtrack-window` value using the following AWS CLI commands:
+  [modify\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) 
+  [restore\-db\-cluster\-from\-s3](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-s3.html) 
+  [restore\-db\-cluster\-from\-snapshot](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html) 
+  [restore\-db\-cluster\-to\-point\-in\-time](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) 

The following procedure describes how to modify the target backtrack window for a DB cluster using the AWS CLI\.

**To modify the target backtrack window for a DB cluster using the AWS CLI**
+ Call the [modify\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command and supply the following values:
  + `--db-cluster-identifier` – The name of the DB cluster\.
  + `--backtrack-window` – The maximum number of seconds that you want to be able to backtrack the DB cluster\.

  The following example sets the target backtrack window for `sample-cluster` to one day \(86,400 seconds\)\.

  For Linux, OS X, or Unix:

  ```
  aws rds modify-db-cluster \
      --db-cluster-identifier sample-cluster \
      --backtrack-window 86400
  ```

  For Windows:

  ```
  aws rds modify-db-cluster ^
      --db-cluster-identifier sample-cluster ^
      --backtrack-window 86400
  ```

**Note**  
Currently, you can enable backtracking only for a DB cluster that was created with the Backtrack feature enabled\.

### API<a name="AuroraMySQL.Managing.Backtrack.Configuring.API"></a>

When you create a new Aurora MySQL DB cluster using the [CreateDBCluster](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) action Amazon RDS API action, backtracking is configured when you specify a `BacktrackWindow` value that is greater than zero\. The `BacktrackWindow` value specifies the target backtrack window for the DB cluster specified in the `DBClusterIdentifier` value\. For more information, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

You can also specify the `BacktrackWindow` value using the following API actions:
+  [ModifyDBCluster](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) 
+  [RestoreDBClusterFromS3](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromS3.html) 
+  [RestoreDBClusterFromSnapshot](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html) 
+  [RestoreDBClusterToPointInTime](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html) 

**Note**  
Currently, you can enable backtracking only for a DB cluster that was created with the Backtrack feature enabled\.

## Performing a Backtrack<a name="AAuroraMySQL.Managing.Backtrack.Performing"></a>

You can backtrack a DB cluster to a specified backtrack time stamp\. If the backtrack time stamp isn't earlier than the earliest possible backtrack time, and isn't in the future, the DB cluster is backtracked to that time stamp\. 

Otherwise, an error typically occurs\. Also, if you try to backtrack a DB cluster for which binary logging is enabled, an error typically occurs unless you've chosen to force the backtrack to occur\. Forcing a backtrack to occur can interfere with other operations that use binary logging\.

**Important**  
Backtracking doesn't generate binlog entries for the changes that it makes\. If you have binary logging enabled for the DB cluster, backtracking might not be compatible with your binlog implementation\.

**Note**  
For database clones, you can't backtrack the DB cluster earlier than the date and time when the clone was created\. For more information about database cloning, see [Cloning Databases in an Aurora DB Cluster](Aurora.Managing.Clone.md)\.

### AWS Management Console<a name="AuroraMySQL.Managing.Backtrack.Performing.Console"></a>

The following procedure describes how to perform a backtrack operation for a DB cluster using the console\.

**To perform a backtrack operation using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the primary instance for the DB cluster that you want to backtrack\.

1. Choose **Instance actions**, and then choose **Backtrack DB cluster**\.

1. On the **Backtrack DB cluster** page, type the backtrack time stamp to backtrack the DB cluster to\.  
![\[Backtrack DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-backtrack-db-cluster.png)

1. Choose **Backtrack DB cluster**\.

### CLI<a name="AuroraMySQL.Managing.Backtrack.Performing.CLI"></a>

The following procedure describes how to backtrack a DB cluster using the AWS CLI\.

**To backtrack a DB cluster using the AWS CLI**
+ Call the [backtrack\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/backtrack-db-cluster.html) AWS CLI command and supply the following values:
  + `--db-cluster-identifier` – The name of the DB cluster\.
  + `--backtrack-to` – The backtrack time stamp to backtrack the DB cluster to, specified in ISO 8601 format\.

  The following example backtracks the DB cluster `sample-cluster` to March 19, 2018, at 10 a\.m\.

  For Linux, OS X, or Unix:

  ```
  aws rds backtrack-db-cluster \
      --db-cluster-identifier sample-cluster \
      --backtrack-to 2018-03-19T10:00:00+00:00
  ```

  For Windows:

  ```
  aws rds backtrack-db-cluster ^
      --db-cluster-identifier sample-cluster ^
      --backtrack-to 2018-03-19T10:00:00+00:00
  ```

### API<a name="AuroraMySQL.Managing.Backtrack.Performing.API"></a>

To backtrack a DB cluster using the Amazon RDS API, use the [BacktrackDBCluster](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_BacktrackDBCluster.html) action\. This action backtracks the DB cluster specified in the `DBClusterIdentifier` value to the specified time\.

## Monitoring Backtracking<a name="AuroraMySQL.Managing.Backtrack.Monitoring"></a>

You can view backtracking information and monitor backtracking metrics for a DB cluster\.

### AWS Management Console<a name="AuroraMySQL.Managing.Backtrack.Describing.Console"></a>

**To view backtracking information and monitor backtracking metrics using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Clusters**\.

1. Choose the DB cluster name to open information about it\.

   The backtrack information is in the **Backtrack** section\.  
![\[Backtrack details for a DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-backtrack-details.png)

   When backtracking is enabled, the following information is available:
   + **Target window** – The current amount of time specified for the target backtrack window\. The target is the maximum amount of time that you can backtrack if there is sufficient storage\.
   + **Actual window** – The actual amount of time you can backtrack, which can be smaller than the target backtrack window\. The actual backtrack window is based on your workload and the storage available for retaining backtrack change records\.
   + **Earliest backtrack time** – The earliest possible backtrack time for the DB cluster\. You can't backtrack the DB cluster to a time before the displayed time\.

1. Do the following to view backtracking metrics for the DB cluster:

   1. In the navigation pane, choose **Instances**\.

   1. Choose the name of the primary instance for the DB cluster to display its details\.

   1. In the **CloudWatch** section, type **Backtrack** into the **CloudWatch** box to show only the Backtrack metrics\.  
![\[Backtrack metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-backtrack-metrics.png)

      The following metrics are displayed:
      + **Backtrack Change Records Creation Rate \(Count\)** – This metric shows the number of backtrack change records created over five minutes for your DB cluster\. You can use this metric to estimate the backtrack cost for your target backtrack window\.
      + **\[Billed\] Backtrack Change Records Stored \(Count\)** – This metric shows the actual number of backtrack change records used by your DB cluster\.
      + **Backtrack Window Actual \(Minutes\)** – This metric shows whether there is a difference between the target backtrack window and the actual backtrack window\. For example, if your target backtrack window is 2 hours \(120 minutes\), and this metric shows that the actual backtrack window is 100 minutes, then the actual backtrack window is smaller than the target\.
      + **Backtrack Window Alert \(Count\)** – This metric shows how often the actual backtrack window is smaller than the target backtrack window for a given period of time\.
**Note**  
The following metrics might lag behind the current time:  
**Backtrack Change Records Creation Rate \(Count\)**
**\[Billed\] Backtrack Change Records Stored \(Count\)**

### CLI<a name="AuroraMySQL.Managing.Backtrack.Describing.CLI"></a>

The following procedure describes how to view backtrack information for a DB cluster using the AWS CLI\.

**To view backtrack information for a DB cluster using the AWS CLI**
+ Call the [describe\-db\-clusters](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) AWS CLI command and supply the following values:
  + `--db-cluster-identifier` – The name of the DB cluster\.

  The following example lists backtrack information for `sample-cluster`\.

  For Linux, OS X, or Unix:

  ```
  aws rds describe-db-clusters \
      --db-cluster-identifier sample-cluster
  ```

  For Windows:

  ```
  aws rds describe-db-clusters ^
      --db-cluster-identifier sample-cluster
  ```

### API<a name="AuroraMySQL.Managing.Backtrack.Describing.API"></a>

To view backtrack information for a DB cluster using the Amazon RDS API, use the [DescribeDBClusters](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html) action\. This action returns backtrack information for the DB cluster specified in the `DBClusterIdentifier` value\.

## Subscribing to a Backtrack Event with the Console<a name="AuroraMySQL.Managing.Backtrack.Event.Console"></a>

The following procedure describes how to subscribe to a backtrack event using the console\. The event sends you an email or text notification when your actual backtrack window is smaller than your target backtrack window\.

**To view backtrack information using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Event subscriptions**\.

1. Choose **Create event subscription**\.

1. In the **Name** box, type a name for the event subscription, and ensure that **Yes** is selected for **Enabled**\.

1. In the **Target** section, choose **New email topic**\.

1. For **Topic name**, type a name for the topic, and for **With these recipients**, enter the email addresses or phone numbers to receive the notifications\.

1. In the **Source** section, choose **Instances** for **Source type**\.

1. For **Instances to include**, choose **Select specific instances**, and choose your DB instance\.

1. For **Event categories to include**, choose **Select specific event categories**, and choose **backtrack**\.

   Your page should look similar to the following page\.  
![\[Backtrack event subscription\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-backtrack-event.png)

1. Choose **Create**\.

## Retrieving Existing Backtracks<a name="AuroraMySQL.Managing.Backtrack.Retrieving"></a>

You can retrieve information about existing backtracks for a DB cluster\. This information includes the unique identifier of the backtrack, the date and time backtracked to and from, the date and time the backtrack was requested, and the current status of the backtrack\.

**Note**  
Currently, you can't retrieve existing backtracks using the console\.

### CLI<a name="AuroraMySQL.Managing.Backtrack.Retrieving.CLI"></a>

The following procedure describes how to retrieve existing backtracks for a DB cluster using the AWS CLI\.

**To retrieve existing backtracks using the AWS CLI**
+ Call the [describe\-db\-cluster\-backtracks](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-backtracks.html) AWS CLI command and supply the following values:
  + `--db-cluster-identifier` – The name of the DB cluster\.

  The following example retrieves existing backtracks for `sample-cluster`\.

  For Linux, OS X, or Unix:

  ```
  aws rds describe-db-cluster-backtracks \
      --db-cluster-identifier sample-cluster
  ```

  For Windows:

  ```
  aws rds describe-db-cluster-backtracks ^
      --db-cluster-identifier sample-cluster
  ```

### API<a name="AuroraMySQL.Managing.Backtrack.Retrieving.API"></a>

To retrieve information about the backtracks for a DB cluster using the Amazon RDS API, use the [DescribeDBClusterBacktracks](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterBacktracks.html) action\. This action returns information about backtracks for the DB cluster specified in the `DBClusterIdentifier` value\.

## Disabling Backtracking for a DB Cluster<a name="AuroraMySQL.Managing.Backtrack.Disabling"></a>

You can disable the Backtrack feature for a DB cluster\.

### AWS Management Console<a name="AuroraMySQL.Managing.Backtrack.Disabling.Console"></a>

You can disable backtracking for a DB cluster using the console\.

**To disable the Backtrack feature for a DB cluster using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Clusters**\.

1. Choose the cluster you want to modify, and choose **Modify cluster** for **Actions**\.

1. In the **Backtrack** section, choose **Disable Backtrack**\.

1. Choose **Continue**\.

1. For **Scheduling of Modifications**, choose one of the following:
   + **Apply during the next scheduled maintenance window** – Wait to apply the modification until the next maintenance window\.
   + **Apply immediately** – Apply the modification as soon as possible\.

1. Choose **Modify Cluster**\.

### CLI<a name="AuroraMySQL.Managing.Backtrack.Disabling.CLI"></a>

You can disable the Backtrack feature for a DB cluster using the AWS CLI by setting the target backtrack window to `0` \(zero\)\.

**To modify the target backtrack window for a DB cluster using the AWS CLI**
+ Call the [modify\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command and supply the following values:
  + `--db-cluster-identifier` – The name of the DB cluster\.
  + `--backtrack-window` – `0`\.

  The following example disables the Backtrack feature for the `sample-cluster` by setting `--backtrack-window` to `0`\.

  For Linux, OS X, or Unix:

  ```
  aws rds modify-db-cluster \
      --db-cluster-identifier sample-cluster \
      --backtrack-window 0
  ```

  For Windows:

  ```
  aws rds modify-db-cluster ^
      --db-cluster-identifier sample-cluster ^
      --backtrack-window 0
  ```

### API<a name="AuroraMySQL.Managing.Backtrack.Disabling.API"></a>

To disable the Backtrack feature for a DB cluster using the Amazon RDS API, use the [ModifyDBCluster](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) action\. Set the `BacktrackWindow` value to `0` \(zero\), and specify the DB cluster in the `DBClusterIdentifier` value\.