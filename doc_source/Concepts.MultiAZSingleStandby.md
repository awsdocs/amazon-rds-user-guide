# Multi\-AZ DB instance deployments<a name="Concepts.MultiAZSingleStandby"></a>

Amazon RDS provides high availability and failover support for DB instances using Multi\-AZ deployments with a single standby DB instance\. This type of deployment is called a *Multi\-AZ DB instance deployment*\. Amazon RDS uses several different technologies to provide this failover support\. Multi\-AZ deployments for MariaDB, MySQL, Oracle, and PostgreSQL DB instances use the Amazon failover technology\. Microsoft SQL Server DB instances use SQL Server Database Mirroring \(DBM\) or Always On Availability Groups \(AGs\)\. For information on SQL Server version support for Multi\-AZ, see [Multi\-AZ deployments for Amazon RDS for Microsoft SQL Server](USER_SQLServerMultiAZ.md)\.

In a Multi\-AZ DB instance deployment, Amazon RDS automatically provisions and maintains a synchronous standby replica in a different Availability Zone\. The primary DB instance is synchronously replicated across Availability Zones to a standby replica to provide data redundancy and minimize latency spikes during system backups\. Running a DB instance with high availability can enhance availability during planned system maintenance\. It can also help protect your databases against DB instance failure and Availability Zone disruption\. For more information on Availability Zones, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\.

**Note**  
The high availability option isn't a scaling solution for read\-only scenarios\. You can't use a standby replica to serve read traffic\. To serve read\-only traffic, use a Multi\-AZ DB cluster or a read replica instead\. For more information about Multi\-AZ DB clusters, see [Multi\-AZ DB cluster deployments](multi-az-db-clusters-concepts.md)\. For more information about read replicas, see [Working with read replicas](USER_ReadRepl.md)\.

![\[High availability scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/con-multi-AZ.png)

Using the RDS console, you can create a Multi\-AZ DB instance deployment by simply specifying Multi\-AZ when creating a DB instance\. You can use the console to convert existing DB instances to Multi\-AZ DB instance deployments by modifying the DB instance and specifying the Multi\-AZ option\. You can also specify a Multi\-AZ DB instance deployment with the AWS CLI or Amazon RDS API\. Use the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) or [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command, or the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) or [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) API operation\.

The RDS console shows the Availability Zone of the standby replica \(called the secondary AZ\)\. You can also use the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) CLI command or the [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) API operation to find the secondary AZ\.

DB instances using Multi\-AZ DB instance deployments can have increased write and commit latency compared to a Single\-AZ deployment\. This can happen because of the synchronous data replication that occurs\. You might have a change in latency if your deployment fails over to the standby replica, although AWS is engineered with low\-latency network connectivity between Availability Zones\. For production workloads, we recommend that you use Provisioned IOPS \(input/output operations per second\) for fast, consistent performance\. For more information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.

## Modifying a DB instance to be a Multi\-AZ DB instance deployment<a name="Concepts.MultiAZ.Migrating"></a>

If you have a DB instance in a Single\-AZ deployment and modify it to a Multi\-AZ DB instance deployment \(for engines other than Amazon Aurora\), Amazon RDS performs several actions:

1. Takes a snapshot of the primary DB instance's Amazon Elastic Block Store \(EBS\) volumes\.

1. Creates new volumes for the standby replica from the snapshot\. These volumes initialize in the background, and maximum volume performance is achieved after the data is fully initialized\.

1. Turns on synchronous block\-level replication between the volumes of the primary and standby replicas\.

**Important**  
Using a snapshot to create the standby instance avoids downtime when you convert from Single\-AZ to Multi\-AZ, but you can experience a performance impact during and after converting to Multi\-AZ\. This impact can be significant for workloads that are sensitive to write latency\.  
While this capability lets large volumes be restored from snapshots quickly, it can cause a significant increase in the latency of I/O operations because of the synchronous replication\. This latency can impact your database performance\. We highly recommend as a best practice not to perform Multi\-AZ conversion on a production DB instance\.  
To avoid the performance impact on the DB instance currently serving the sensitive workload, create a read replica and enable backups on the read replica\. Convert the read replica to Multi\-AZ, and run queries that load the data into the read replica's volumes \(on both AZs\)\. Then promote the read replica to be the primary DB instance\. For more information, see [Working with read replicas](USER_ReadRepl.md)\.

There are two ways to modify a DB instance to be a Multi\-AZ DB instance deployment:

**Topics**
+ [Convert to a Multi\-AZ DB instance deployment with the RDS console](#Concepts.MultiAZ.Migrating.Convert)
+ [Modifying a DB instance to be a Multi\-AZ DB instance deployment](#Concepts.MultiAZ.Migrating.Modify)

### Convert to a Multi\-AZ DB instance deployment with the RDS console<a name="Concepts.MultiAZ.Migrating.Convert"></a>

You can use the RDS console to convert a DB instance to a Multi\-AZ DB instance deployment\.

You can only use the console to complete the conversion\. To use the AWS CLI or RDS API, follow the instructions in [Modifying a DB instance to be a Multi\-AZ DB instance deployment](#Concepts.MultiAZ.Migrating.Modify)\.

**To convert to a Multi\-AZ DB instance deployment with the RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to modify\.

1. From **Actions**, choose **Convert to Multi\-AZ deployment**\.

1. On the confirmation page, choose **Apply immediately** to apply the changes immediately\. Choosing this option doesn't cause downtime, but there is a possible performance impact\. Alternatively, you can choose to apply the update during the next maintenance window\. For more information, see [Using the Apply Immediately setting](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\.

1. Choose **Convert to Multi\-AZ**\.

### Modifying a DB instance to be a Multi\-AZ DB instance deployment<a name="Concepts.MultiAZ.Migrating.Modify"></a>

You can modify a DB instance to be a MultiAZ DB instance deployment in the following ways:
+ Using the RDS console, modify the DB instance, and set **Multi\-AZ deployment** to **Yes**\.
+ Using the AWS CLI, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command, and set the `--multi-az` option\.
+ Using the RDS API, call the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation, and set the `MultiAZ` parameter to `true`\.

For information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. After the modification is complete, Amazon RDS triggers an event \(RDS\-EVENT\-0025\) that indicates the process is complete\. You can monitor Amazon RDS events\. For more information about events, see [Working with Amazon RDS event notification](USER_Events.md)\.

## Failover process for Amazon RDS<a name="Concepts.MultiAZ.Failover"></a>

If a planned or unplanned outage of your DB instance results from an infrastructure defect, Amazon RDS automatically switches to a standby replica in another Availability Zone if you have turned on Multi\-AZ\. The time that it takes for the failover to complete depends on the database activity and other conditions at the time the primary DB instance became unavailable\. Failover times are typically 60–120 seconds\. However, large transactions or a lengthy recovery process can increase failover time\. When the failover is complete, it can take additional time for the RDS console to reflect the new Availability Zone\.

**Note**  
You can force a failover manually when you reboot a DB instance\. For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\.

Amazon RDS handles failovers automatically so you can resume database operations as quickly as possible without administrative intervention\. The primary DB instance switches over automatically to the standby replica if any of the conditions described in the following table occurs\. You can view these failover reasons in the event log\.


| Failover reason | Description | 
| --- | --- | 
| The operating system underlying the RDS database instance is being patched in an offline operation\. |  A failover was triggered during the maintenance window for an OS patch or a security update\. For more information, see [Maintaining a DB instance](USER_UpgradeDBInstance.Maintenance.md)\.  | 
| The primary host of the RDS Multi\-AZ instance is unhealthy\. | The Multi\-AZ DB instance deployment detected an impaired primary DB instance and failed over\. | 
| The primary host of the RDS Multi\-AZ instance is unreachable due to loss of network connectivity\. |  RDS monitoring detected a network reachability failure to the primary DB instance and triggered a failover\.  | 
| The RDS instance was modified by customer\. |  An RDS DB instance modification triggered a failover\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.  | 
| The RDS Multi\-AZ primary instance is busy and unresponsive\. |  The primary DB instance is unresponsive\. We recommend that you do the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZSingleStandby.html) For more information on these recommendations, see [Overview of monitoring metrics in Amazon RDS](MonitoringOverview.md) and [Best practices for Amazon RDS](CHAP_BestPractices.md)\.  | 
| The storage volume underlying the primary host of the RDS Multi\-AZ instance experienced a failure\. | The Multi\-AZ DB instance deployment detected a storage issue on the primary DB instance and failed over\. | 
| The user requested a failover of the DB instance\. |  You rebooted the DB instance and chose **Reboot with failover**\. For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\.  | 

To determine if your Multi\-AZ DB instance has failed over, you can do the following:
+ Set up DB event subscriptions to notify you by email or SMS that a failover has been initiated\. For more information about events, see [Working with Amazon RDS event notification](USER_Events.md)\.
+ View your DB events by using the RDS console or API operations\.
+ View the current state of your Multi\-AZ DB instance deployment by using the RDS console or API operations\.

For information on how you can respond to failovers, reduce recovery time, and other best practices for Amazon RDS, see [Best practices for Amazon RDS](CHAP_BestPractices.md)\.

### Setting the JVM TTL for DNS name lookups<a name="Concepts.MultiAZ.Failover.Java-DNS"></a>

The failover mechanism automatically changes the Domain Name System \(DNS\) record of the DB instance to point to the standby DB instance\. As a result, you need to re\-establish any existing connections to your DB instance\. In a Java virtual machine \(JVM\) environment, due to how the Java DNS caching mechanism works, you might need to reconfigure JVM settings\.

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