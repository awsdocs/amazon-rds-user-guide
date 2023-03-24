# Multi\-AZ DB cluster deployments<a name="multi-az-db-clusters-concepts"></a>

A *Multi\-AZ DB cluster deployment* is a high availability deployment mode of Amazon RDS with two readable standby DB instances\. A Multi\-AZ DB cluster has a writer DB instance and two reader DB instances in three separate Availability Zones in the same AWS Region\. Multi\-AZ DB clusters provide high availability, increased capacity for read workloads, and lower write latency when compared to Multi\-AZ DB instance deployments\.

You can import data from an on\-premises database to a Multi\-AZ DB cluster by following the instructions in [Importing data to an Amazon RDS MariaDB or MySQL database with reduced downtime](MySQL.Procedural.Importing.NonRDSRepl.md)\.

**Topics**
+ [Region and version availability](#multi-az-db-clusters-concepts.RegionVersionAvailability)
+ [Instance class availability](#multi-az-db-clusters-concepts.InstanceAvailability)
+ [Overview of Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-overview)
+ [Limitations for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts.Limitations)
+ [Managing a Multi\-AZ DB cluster with the AWS Management Console](#multi-az-db-clusters-concepts-managing-console)
+ [Working with parameter groups for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-parameter-groups)
+ [Replica lag and Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-replica-lag)
+ [Failover process for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-failover)
+ [Creating a Multi\-AZ DB cluster](create-multi-az-db-cluster.md)
+ [Connecting to a Multi\-AZ DB cluster](multi-az-db-clusters-concepts-connection-management.md)
+ [Connecting an EC2 instance and a Multi\-AZ DB cluster automatically](multiaz-ec2-rds-connect.md)
+ [Modifying a Multi\-AZ DB cluster](modify-multi-az-db-cluster.md)
+ [Renaming a Multi\-AZ DB cluster](multi-az-db-cluster-rename.md)
+ [Rebooting a Multi\-AZ DB cluster and reader DB instances](multi-az-db-clusters-concepts-rebooting.md)
+ [Working with Multi\-AZ DB cluster read replicas](USER_MultiAZDBCluster_ReadRepl.md)
+ [Deleting a Multi\-AZ DB cluster](USER_DeleteMultiAZDBCluster.Deleting.md)

**Important**  
Multi\-AZ DB clusters aren't the same as Aurora DB clusters\. For information about Aurora DB clusters, see the [Amazon Aurora User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)\.

## Region and version availability<a name="multi-az-db-clusters-concepts.RegionVersionAvailability"></a>

Feature availability and support varies across specific versions of each database engine, and across AWS Regions\. For more information on version and Region availability of Amazon RDS with Multi\-AZ DB clusters, see [Multi\-AZ DB clusters](Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.md)\.

## Instance class availability<a name="multi-az-db-clusters-concepts.InstanceAvailability"></a>

Multi\-AZ DB cluster deployments are supported for a subset of DB instance classes\. For a list of supported instance classes, see the **DB instance class** row in [Settings for creating Multi\-AZ DB clusters](create-multi-az-db-cluster.md#create-multi-az-db-cluster-settings)\.

For more information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.

## Overview of Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-overview"></a>

With a Multi\-AZ DB cluster, Amazon RDS replicates data from the writer DB instance to both of the reader DB instances using the DB engine's native replication capabilities\. When a change is made on the writer DB instance, it's sent to each reader DB instance\. Acknowledgment from at least one reader DB instance is required for a change to be committed\.

Reader DB instances act as automatic failover targets and also serve read traffic to increase application read throughput\. If an outage occurs on your writer DB instance, RDS manages failover to one of the reader DB instances\. RDS does this based on which reader DB instance has the most recent change record\.

The following diagram shows a Multi\-AZ DB cluster\.

![\[Multi-AZ DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster.png)

Multi\-AZ DB clusters typically have lower write latency when compared to Multi\-AZ DB instance deployments\. They also allow read\-only workloads to run on reader DB instances\. The RDS console shows the Availability Zone of the writer DB instance and the Availability Zones of the reader DB instances\. You can also use the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) CLI command or the [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html) API operation to find this information\. 

**Important**  
To prevent replication errors in RDS for MySQL Multi\-AZ DB clusters, we strongly recommend that all tables have a primary key\.

## Limitations for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts.Limitations"></a>

The following limitations apply to Multi\-AZ DB clusters: 
+ Multi\-AZ DB clusters only support Provisioned IOPS storage\.
+ You can't change a Single\-AZ DB instance deployment or Multi\-AZ DB instance deployment into a Multi\-AZ DB cluster\. As an alternative, you can restore a snapshot of a Single\-AZ DB instance deployment or Multi\-AZ DB instance deployment to a Multi\-AZ DB cluster\.
+ Multi\-AZ DB clusters don't support modifications at the DB instance level because all modifications are done at the DB cluster level\.
+ Multi\-AZ DB clusters don't support the following features:
  + Amazon RDS Proxy
  + Support for IPv6 connections \(dual\-stack mode\)
  + Exporting Multi\-AZ DB cluster snapshot data to an Amazon S3 bucket
  + IAM DB authentication
  + Kerberos authentication
  + Modifying the port

    As an alternative, you can restore a Multi\-AZ DB cluster to a point in time and specify a different port\.
  + Option groups
  + Creating a read replica with a Multi\-AZ DB cluster source
  + Reserved DB instances
  + Restoring a Multi\-AZ DB cluster snapshot from an Amazon S3 bucket
  + Storage autoscaling by setting the maximum allocated storage

    As an alternative, you can scale storage manually\.
  + Stopping and starting the DB cluster
  + Copying a snapshot of a Multi\-AZ DB cluster
  + Encrypting an unencrypted Multi\-AZ DB cluster
+ RDS for MySQL Multi\-AZ DB clusters don't support replication to an external target database\.
+ RDS for MySQL Multi\-AZ DB clusters support only the following system stored procedures:
  + `mysql.rds_rotate_general_log`
  + `mysql.rds_rotate_slow_log`
  + `mysql.rds_show_configuration`

  RDS for MySQL Multi\-AZ DB clusters don't support other system stored procedures\. For information about these procedures, see [RDS for MySQL stored procedure reference](Appendix.MySQL.SQLRef.md)\.
+ RDS for PostgreSQL Multi\-AZ DB clusters don't support the following PostgreSQL extensions: `aws_s3`, `pg_transport`, and `pglogical`\.
+ RDS for PostgreSQL Multi\-AZ DB clusters don't support using a custom DNS server for outbound network access\.
+ RDS for PostgreSQL Multi\-AZ DB clusters don't support logical replication\.

## Managing a Multi\-AZ DB cluster with the AWS Management Console<a name="multi-az-db-clusters-concepts-managing-console"></a>

You can manage a Multi\-AZ DB cluster with the console\.

**To manage a Multi\-AZ DB cluster with the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the Multi\-AZ DB cluster that you want to manage\.

The following image shows a Multi\-AZ DB cluster in the console\.

![\[Multi-AZ DB cluster in the AWS Management Console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-view-console-postgresql.png)

The available actions in the **Actions** menu depend on whether the Multi\-AZ DB cluster is selected or a DB instance in the cluster is selected\.

Choose the Multi\-AZ DB cluster to view the cluster details and perform actions at the cluster level\.

![\[Multi-AZ DB cluster actions in the AWS Management Console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-actions-console-postgresql.png)

Choose a DB instance in a Multi\-AZ DB cluster to view the DB instance details and perform actions at the DB instance level\.

![\[DB instance actions in a Multi-AZ DB cluster in the AWS Management Console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-instance-actions-console-postgresql.png)

## Working with parameter groups for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-parameter-groups"></a>

In a Multi\-AZ DB cluster, a *DB cluster parameter group* acts as a container for engine configuration values that are applied to every DB instance in the Multi\-AZ DB cluster\.

In a Multi\-AZ DB cluster, a *DB parameter group* is set to the default DB parameter group for the DB engine and DB engine version\. The settings in the DB cluster parameter group are used for all of the DB instances in the cluster\.

For information about parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

## Replica lag and Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-replica-lag"></a>

*Replica lag* is the difference in time between the latest transaction on the writer DB instance and the latest applied transaction on a reader DB instance\. The Amazon CloudWatch metric `ReplicaLag` represents this time difference\. For more information about CloudWatch metrics, see [Monitoring Amazon RDS metrics with Amazon CloudWatch](monitoring-cloudwatch.md)\.

Although Multi\-AZ DB clusters allow for high write performance, replica lag can still occur due to the nature of engine\-based replication\. Because any failover must first resolve the replica lag before it promotes a new writer DB instance, monitoring and managing this replica lag is a consideration\.

For RDS for MySQL Multi\-AZ DB clusters, failover time depends on replica lag of both remaining reader DB instances\. Both the reader DB instances must apply unapplied transactions before one of them is promoted to the new writer DB instance\. 

For RDS for PostgreSQL Multi\-AZ DB clusters, failover time depends on the lowest replica lag of the two remaining reader DB instances\. The reader DB instance with the lowest replica lag must apply unapplied transactions before it is promoted to the new writer DB instance\.

For a tutorial that shows you how to create a CloudWatch alarm when replica lag exceeds a set amount of time, see [Tutorial: Creating an Amazon CloudWatch alarm for Multi\-AZ DB cluster replica lag](multi-az-db-cluster-cloudwatch-alarm.md)\.

### Common causes of replica lag<a name="multi-az-db-clusters-concepts-replica-lag-causes"></a>

In general, replica lag occurs when the write workload is too high for the reader DB instances to apply the transactions efficiently\. Various workloads can incur temporary or continuous replica lag\. Some examples of common causes are the following:
+ High write concurrency or heavy batch updating on the writer DB instance, causing the apply process on the reader DB instances to fall behind\.
+ Heavy read workload that is using resources on one or more reader DB instances\. Running slow or large queries can affect the apply process and can cause replica lag\.
+ Transactions that modify large amounts of data or DDL statements can sometimes cause a temporary increase in replica lag because the database must preserve commit order\.

### Mitigating replica lag<a name="multi-az-db-clusters-concepts-replica-lag-mitigating"></a>

For Multi\-AZ DB clusters for RDS for MySQL and RDS for PostgreSQL, you can mitigate replica lag by reducing the load on your writer DB instance\. You can also use flow control to reduce replica lag\. *Flow control* works by throttling writes on the writer DB instance, which ensures that replica lag doesn't continue to grow unbounded\. Write throttling is accomplished by adding a delay into the end of a transaction, which decreases the write throughput on the writer DB instance\. Although flow control doesn't guarantee lag elimination, it can help reduce overall lag in many workloads\. The following sections provide information about using flow control with RDS for MySQL and RDS for PostgreSQL\.

#### Mitigating replica lag with flow control for RDS for MySQL<a name="multi-az-db-clusters-concepts-replica-lag-mitigating.mysql"></a>

When you are using RDS for MySQL Multi\-AZ DB clusters, flow control is turned on by default using the dynamic parameter `rpl_semi_sync_master_target_apply_lag`\. This parameter specifies the upper limit that you want for replica lag\. As replica lag approaches this configured limit, flow control throttles the write transactions on the writer DB Instance to try to contain the replica lag below the specified value\. In some cases, replica lag can exceed the specified limit\. By default, this parameter is set to 120 seconds\. To turn off flow control, set this parameter to its maximum value of 86400 seconds \(one day\)\.

To view the current delay injected by flow control, show the parameter `Rpl_semi_sync_master_flow_control_current_delay` by running the following query\.

```
SHOW GLOBAL STATUS like '%flow_control%';
```

Your output looks similar to the following\.

```
+-------------------------------------------------+-------+
| Variable_name                                   | Value |
+-------------------------------------------------+-------+
| Rpl_semi_sync_master_flow_control_current_delay | 2010  |
+-------------------------------------------------+-------+
1 row in set (0.00 sec)
```

**Note**  
The delay is shown in microseconds\.

When you have Performance Insights turned on for an RDS for MySQL Multi\-AZ DB cluster, you can monitor the wait event corresponding to a SQL statement indicating that the queries were delayed by a flow control\. When a delay was introduced by a flow control, you can view the wait event `/wait/synch/cond/semisync/semi_sync_flow_control_delay_cond` corresponding to the SQL statement on the Performance Insights dashboard\. To view these metrics, make sure that the Performance Schema is turned on\. For information about Performance Insights, see [Monitoring DB load with Performance Insights on Amazon RDS](USER_PerfInsights.md)\.

#### Mitigating replica lag with flow control for RDS for PostgreSQL<a name="multi-az-db-clusters-concepts-replica-lag-mitigating.postgresql"></a>

When you are using RDS for PostgreSQL Multi\-AZ DB clusters, flow control is deployed as an extension\. It turns on a background worker for all DB instances in the DB cluster\. By default, the background workers on the reader DB instances communicate the current replica lag with the background worker on the writer DB instance\. If the lag exceeds two minutes on any reader DB instance, the background worker on the writer DB instance adds a delay at the end of a transaction\. To control the lag threshold, use the parameter `flow_control.target_standby_apply_lag`\.

When a flow control throttles a PostgreSQL process, the `Extension` wait event in `pg_stat_activity` and Performance Insights indicates that\. The function `get_flow_control_stats` displays details about how much delay is currently being added\.

Flow control can benefit most online transaction processing \(OLTP\) workloads that have short but highly concurrent transactions\. If the lag is caused by long\-running transactions, such as batch operations, flow control doesn't provide as strong a benefit\.

You can turn off flow control by removing the extension from the `preload_shared_libraries` and rebooting your DB instance\.

## Failover process for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-failover"></a>

If there is a planned or unplanned outage of your writer DB instance in a Multi\-AZ DB cluster, Amazon RDS automatically fails over to a reader DB instance in different Availability Zone\. The time it takes for the failover to complete depends on the database activity and other conditions when the writer DB instance became unavailable\. Failover times are typically under 35 seconds\. Failover completes when both reader DB instances have applied outstanding transactions from the failed writer\. When the failover is complete, it can take additional time for the RDS console to reflect the new Availability Zone\.

**Topics**
+ [Automatic failovers](#multi-az-db-clusters-concepts-failover-automatic)
+ [Manually failing over a Multi\-AZ DB cluster](#multi-az-db-clusters-concepts-failover-manual)
+ [Determining whether a Multi\-AZ DB cluster has failed over](#multi-az-db-clusters-concepts-failover-determining)
+ [Setting the JVM TTL for DNS name lookups](#multi-az-db-clusters-concepts-failover-java-dns)

### Automatic failovers<a name="multi-az-db-clusters-concepts-failover-automatic"></a>

Amazon RDS handles failovers automatically so you can resume database operations as quickly as possible without administrative intervention\. To fail over, the writer DB instance switches automatically to a reader DB instance\.

### Manually failing over a Multi\-AZ DB cluster<a name="multi-az-db-clusters-concepts-failover-manual"></a>

You can fail over a Multi\-AZ DB cluster manually using the AWS Management Console, the AWS CLI, or the RDS API\.

#### Console<a name="multi-az-db-clusters-concepts-failover-manual-con"></a>

**To fail over a Multi\-AZ DB cluster manually**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Multi\-AZ DB cluster that you want to fail over\.

1. For **Actions**, choose **Failover**\.

   The **Failover DB Cluster** page appears\.

1. Choose **Failover** to confirm the manual failover\.

#### AWS CLI<a name="multi-az-db-clusters-concepts-failover-manual-cli"></a>

To fail over a Multi\-AZ DB cluster manually, use the AWS CLI command [ failover\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/failover-db-cluster.html)\.

**Example**  

```
1. aws rds failover-db-cluster --db-cluster-identifier mymultiazdbcluster
```

#### RDS API<a name="multi-az-db-clusters-concepts-failover-manual-api"></a>

To fail over a Multi\-AZ DB cluster manually, call the Amazon RDS API [FailoverDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html) and specify the `DBClusterIdentifier`\.

### Determining whether a Multi\-AZ DB cluster has failed over<a name="multi-az-db-clusters-concepts-failover-determining"></a>

To determine if your Multi\-AZ DB cluster has failed over, you can do the following:
+ Set up DB event subscriptions to notify you by email or SMS that a failover has been initiated\. For more information about events, see [Working with Amazon RDS event notification](USER_Events.md)\.
+ View your DB events by using the Amazon RDS console or API operations\.
+ View the current state of your Multi\-AZ DB cluster by using the Amazon RDS console, the AWS CLI, and the RDS API\.

For information on how you can respond to failovers, reduce recovery time, and other best practices for Amazon RDS, see [Best practices for Amazon RDS](CHAP_BestPractices.md)\.

### Setting the JVM TTL for DNS name lookups<a name="multi-az-db-clusters-concepts-failover-java-dns"></a>

The failover mechanism automatically changes the Domain Name System \(DNS\) record of the DB instance to point to the reader DB instance\. As a result, you need to re\-establish any existing connections to your DB instance\. In a Java virtual machine \(JVM\) environment, due to how the Java DNS caching mechanism works, you might need to reconfigure JVM settings\.

The JVM caches DNS name lookups\. When the JVM resolves a host name to an IP address, it caches the IP address for a specified period of time, known as the *time\-to\-live* \(TTL\)\.

Because AWS resources use DNS name entries that occasionally change, we recommend that you configure your JVM with a TTL value of no more than 60 seconds\. Doing this makes sure that when a resource's IP address changes, your application can receive and use the resource's new IP address by requerying the DNS\.

On some Java configurations, the JVM default TTL is set so that it never refreshes DNS entries until the JVM is restarted\. Thus, if the IP address for an AWS resource changes while your application is still running, it can't use that resource until you manually restart the JVM and the cached IP information is refreshed\. In this case, it's crucial to set the JVM's TTL so that it periodically refreshes its cached IP information\.

**Note**  
The default TTL can vary according to the version of your JVM and whether a security manager is installed\. Many JVMs provide a default TTL less than 60 seconds\. If you're using such a JVM and not using a security manager, you can ignore the rest of this topic\. For more information on security managers in Oracle, see [The security manager](https://docs.oracle.com/javase/tutorial/essential/environment/security.html) in the Oracle documentation\.

To modify the JVM's TTL, set the [https://docs.oracle.com/javase/7/docs/technotes/guides/net/properties.html](https://docs.oracle.com/javase/7/docs/technotes/guides/net/properties.html) property value\. Use one of the following methods, depending on your needs:
+ To set the property value globally for all applications that use the JVM, set `networkaddress.cache.ttl` in the `$JAVA_HOME/jre/lib/security/java.security` file\.

  ```
  networkaddress.cache.ttl=60					
  ```
+ To set the property locally for your application only, set `networkaddress.cache.ttl` in your application's initialization code before any network connections are established\.

  ```
  java.security.Security.setProperty("networkaddress.cache.ttl" , "60");					
  ```