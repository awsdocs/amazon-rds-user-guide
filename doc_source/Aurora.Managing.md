# Managing an Amazon Aurora DB Cluster<a name="Aurora.Managing"></a>

In the following sections, you can find information about managing performance, scaling, fault tolerance, backup, and restoring for an Amazon Aurora DB cluster\. 

## Managing Performance and Scaling for Aurora DB Clusters<a name="Aurora.Managing.Performance"></a>

You can use the following options to manage performance and scaling for Aurora DB clusters and DB instances:

+ [Storage Scaling](#Aurora.Managing.Performance.StorageScaling)

+ [Instance Scaling](#Aurora.Managing.Performance.InstanceScaling)

+ [Read Scaling](#Aurora.Managing.Performance.ReadScaling)

+ [Managing Connections](#Aurora.Managing.MaxConnections)

### Storage Scaling<a name="Aurora.Managing.Performance.StorageScaling"></a>

Aurora storage automatically scales with the data in your cluster volume\. As your data grows, your cluster volume storage grows in 10 gigabyte \(GB\) increments up to 64 TB\.

The size of your cluster volume is checked on an hourly basis to determine your storage costs\. For pricing information, see the [Amazon RDS product page](http://aws.amazon.com/rds/#pricing)\.

### Instance Scaling<a name="Aurora.Managing.Performance.InstanceScaling"></a>

You can scale your Aurora DB cluster as needed by modifying the DB instance class for each DB instance in the DB cluster\. Aurora supports several DB instance classes optimized for Aurora, depending on database engine compatibility\.


| Database Engine | Instance Scaling | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Scaling Aurora MySQL DB Instances](AuroraMySQL.Managing.md#AuroraMySQL.Managing.Performance.InstanceScaling)  | 
|  Amazon Aurora PostgreSQL  |  See [Scaling Aurora PostgreSQL DB Instances](AuroraPostgreSQL.Managing.md#AuroraPostgreSQL.Managing.Performance.InstanceScaling)  | 

### Read Scaling<a name="Aurora.Managing.Performance.ReadScaling"></a>

You can achieve read scaling for your Aurora DB cluster by creating up to 15 Aurora Replicas in the DB cluster\. Each Aurora Replica returns the same data from the cluster volume with minimal replica lag—usually considerably less than 100 milliseconds after the primary instance has written an update\. As your read traffic increases, you can create additional Aurora Replicas and connect to them directly to distribute the read load for your DB cluster\. Aurora Replicas don't have to be of the same DB instance class as the primary instance\.

### Managing Connections<a name="Aurora.Managing.MaxConnections"></a>

The maximum number of connections allowed to an Aurora DB instance is determined by the `max_connections` parameter in the instance\-level parameter group for the DB instance\. The default value of that parameter varies depends on the DB instance class used for the DB instance and database engine compatibility\.


| Database engine | max\_connections default value | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Maximum Connections to an Aurora MySQL DB Instance](AuroraMySQL.Managing.md#AuroraMySQL.Managing.MaxConnections)  | 
|  Amazon Aurora PostgreSQL  |  See [Maximum Connections to an Aurora PostgreSQL DB Instance](AuroraPostgreSQL.Managing.md#AuroraPostgreSQL.Managing.MaxConnections)  | 

## Fault Tolerance for an Aurora DB Cluster<a name="Aurora.Managing.FaultTolerance"></a>

An Aurora DB cluster is fault tolerant by design\. The cluster volume spans multiple Availability Zones in a single AWS Region, and each Availability Zone contains a copy of the cluster volume data\. This functionality means that your DB cluster can tolerate a failure of an Availability Zone without any loss of data and only a brief interruption of service\.

If the primary instance in a DB cluster fails, Aurora automatically fails over to a new primary instance in one of two ways:

+ By promoting an existing Aurora Replica to the new primary instance

+ By creating a new primary instance

If the DB cluster has one or more Aurora Replicas, then an Aurora Replica is promoted to the primary instance during a failure event\. A failure event results in a brief interruption, during which read and write operations fail with an exception\. However, service is typically restored in less than 120 seconds, and often less than 60 seconds\. To increase the availability of your DB cluster, we recommend that you create at least one or more Aurora Replicas in two or more different Availability Zones\. 

You can customize the order in which your Aurora Replicas are promoted to the primary instance after a failure by assigning each replica a priority\. Priorities range from 0 for the highest priority to 15 for the lowest priority\. If the primary instance fails, Amazon RDS promotes the Aurora Replica with the highest priority to the new primary instance\. You can modify the priority of an Aurora Replica at any time\. Modifying the priority doesn't trigger a failover\. 

More than one Aurora Replica can share the same priority, resulting in promotion tiers\. If two or more Aurora Replicas share the same priority, then Amazon RDS promotes the replica that is largest in size\. If two or more Aurora Replicas share the same priority and size, then Amazon RDS promotes an arbitrary replica in the same promotion tier\. 

If the DB cluster doesn't contain any Aurora Replicas, then the primary instance is recreated during a failure event\. A failure event results in an interruption during which read and write operations fail with an exception\. Service is restored when the new primary instance is created, which typically takes less than 10 minutes\. Promoting an Aurora Replica to the primary instance is much faster than creating a new primary instance\.

**Note**  
Amazon Aurora also supports replication with an external MySQL database, or an RDS MySQL DB instance\. For more information, see [Replication Between Aurora and MySQL or Between Aurora and Another Aurora DB Cluster](AuroraMySQL.Replication.MySQL.md)\.

## Backing Up and Restoring an Aurora DB Cluster<a name="Aurora.Managing.Backups"></a>

In the following sections, you can find information about Aurora backups and how to restore your Aurora DB cluster using the AWS Management Console\.

### Backups<a name="Aurora.Managing.Backups.Backup"></a>

Aurora backs up your cluster volume automatically and retains restore data for the length of the *backup retention period*\. Aurora backups are continuous and incremental so you can quickly restore to any point within the backup retention period\. No performance impact or interruption of database service occurs as backup data is being written\. You can specify a backup retention period, from 1 to 35 days, when you create or modify a DB cluster\.

If you want to retain a backup beyond the backup retention period, you can also take a snapshot of the data in your cluster volume\. Storing snapshots incurs the standard storage charges for Amazon RDS\. For more information about RDS storage pricing, see [Amazon Relational Database Service Pricing](http://aws.amazon.com/rds/pricing/)\. 

Because Aurora retains incremental restore data for the entire backup retention period, you only need to create a snapshot for data that you want to retain beyond the backup retention period\. You can create a new DB cluster from the snapshot\.

### Restoring Data<a name="Aurora.Managing.Backups.Restore"></a>

You can recover your data by creating a new Aurora DB cluster from the backup data that Aurora retains, or from a DB cluster snapshot that you have saved\. You can quickly restore a new copy of a DB cluster created from backup data to any point in time during your backup retention period\. The continuous and incremental nature of Aurora backups during the backup retention period means you don't need to take frequent snapshots of your data to improve restore times\.

To determine the latest or earliest restorable time for a DB instance, look for the `Latest Restorable Time` or `Earliest Restorable Time` values on the RDS console\. For information about viewing these values, see [Viewing an Amazon Aurora DB Cluster](Aurora.Viewing.md)\. The latest restorable time for a DB cluster is the most recent point at which you can restore your DB cluster, typically within 5 minutes of the current time\. The earliest restorable time specifies how far back within the backup retention period that you can restore your cluster volume\.

You can determine when the restore of a DB cluster is complete by checking the `Latest Restorable Time` and `Earliest Restorable Time` values\. The `Latest Restorable Time` and `Earliest Restorable Time` values return NULL until the restore operation is complete\. You can't request a backup or restore operation if `Latest Restorable Time` or `Earliest Restorable Time` returns NULL\.

**To restore a DB cluster to a specified time using the AWS Management Console**

1. Open the Amazon Aurora console at [https://console\.aws\.amazon\.com/rds](https://console.aws.amazon.com/rds)\.

1. In the navigation pane, choose **Instances**\. Choose the primary instance for the DB cluster that you want to restore\.

1. Choose **Instance actions**, and then choose **Restore to point in time**\.

   In the **Launch DB Instance** window, choose **Custom** under **Restore time**\.

1. Specify the date and time that you want to restore to under **Custom**\.

1. Type a name for the new, restored DB instance for **DB instance identifier** under **Settings**\.

1. Choose **Launch DB Instance** to launch the restored DB instance\.

   A new DB instance is created with the name you specified, and a new DB cluster is created\. The DB cluster name is the new DB instance name followed by `–cluster`\. For example, if the new DB instance name is `myrestoreddb`, the new DB cluster name is `myrestoreddb-cluster`\.

#### Database Cloning for Aurora<a name="Aurora.Managing.Backups.Restore.Cloning"></a>

You can also use database cloning to clone the databases of your Aurora DB cluster to a new DB cluster, instead of restoring a DB cluster snapshot\. The clone databases use only minimal additional space when first created\. Data is copied only as data changes, either on the source databases or the clone databases\. You can make multiple clones from the same DB cluster, or create additional clones even from other clones\. For more information, see [Cloning Databases in an Aurora DB Cluster](Aurora.Managing.Clone.md)\.

## Amazon Aurora DB Cluster and DB Instance Parameters<a name="Aurora.Managing.ParameterGroups"></a>

You manage your Amazon Aurora DB cluster in the same way that you manage other Amazon RDS DB instances, by using parameters in a DB parameter group\. Amazon Aurora differs from other DB engines in that you have a cluster of DB instances\. As a result, some of the parameters that you use to manage your Amazon Aurora DB cluster apply to the entire cluster\. Other parameters apply only to a particular DB instance in the DB cluster\.

Cluster\-level parameters are managed in DB cluster parameter groups\. Instance\-level parameters are managed in DB parameter groups\. 

Although each DB instance in an Aurora DB cluster is compatible with a specific database engine, some of the database engine parameters must be applied at the cluster level\. You manage these using DB cluster parameter groups\. Cluster\-level parameters are not found in the DB parameter group for an instance in an Aurora DB cluster and are listed later in this topic\.

The DB cluster and DB instance parameters available to you in Aurora vary depending on database engine compatibility\.


| Database Engine | Parameters | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Amazon Aurora MySQL Parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.ParameterGroups)  | 
|  Amazon Aurora PostgreSQL  |  See [Amazon Aurora PostgreSQL Parameters](AuroraPostgreSQL.Reference.md#AuroraPostgreSQL.Reference.ParameterGroups)  | 

## Related Topics<a name="Aurora.Managing.RelatedTopics"></a>

+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)