# High Availability \(Multi\-AZ\)<a name="Concepts.MultiAZ"></a>

Amazon RDS provides high availability and failover support for DB instances using Multi\-AZ deployments\. Amazon RDS uses several different technologies to provide failover support\. Multi\-AZ deployments for Oracle, PostgreSQL, MySQL, and MariaDB DB instances use Amazon's failover technology\. SQL Server DB instances use SQL Server Mirroring\. Amazon Aurora instances stores copies of the data in a DB cluster across multiple Availability Zones in a single AWS Region, regardless of whether the instances in the DB cluster span multiple Availability Zones\. For more information on Amazon Aurora, see [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)\.

In a Multi\-AZ deployment, Amazon RDS automatically provisions and maintains a synchronous standby replica in a different Availability Zone\. The primary DB instance is synchronously replicated across Availability Zones to a standby replica to provide data redundancy, eliminate I/O freezes, and minimize latency spikes during system backups\. Running a DB instance with high availability can enhance availability during planned system maintenance, and help protect your databases against DB instance failure and Availability Zone disruption\. For more information on Availability Zones, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.

**Note**  
The high\-availability feature is not a scaling solution for read\-only scenarios; you cannot use a standby replica to serve read traffic\. To service read\-only traffic, you should use a Read Replica\. For more information, see [Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances](USER_ReadRepl.md)\.

![\[High Availability Scenario \]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/con-multi-AZ.png)

Using the RDS console, you can create a Multi\-AZ deployment by simply specifying Multi\-AZ when creating a DB instance\. You can also use the console to convert existing DB instances to Multi\-AZ deployments by modifying the DB instance and specifying the Multi\-AZ option\. The RDS console shows the Availability Zone of the standby replica, called the secondary AZ\.

You can specify a Multi\-AZ deployment using the CLI as well\. Use the AWS CLI [describe\-db\-instances](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command, or the Amazon RDS API [DescribeDBInstances](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) action to show the Availability Zone of the standby replica \(called the secondary AZ\)\. 

The RDS console shows the Availability Zone of the standby replica \(called the secondary AZ\), or you can use the AWS CLI [describe\-db\-instances](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command, or the Amazon RDS API [DescribeDBInstances](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) action to find the secondary AZ\.

DB instances using Multi\-AZ deployments may have increased write and commit latency compared to a Single\-AZ deployment, due to the synchronous data replication that occurs\. You may have a change in latency if your deployment fails over to the standby replica, although AWS is engineered with low\-latency network connectivity between Availability Zones\. For production workloads, we recommend that you use Provisioned IOPS and DB instance classes \(m1\.large and larger\) that are optimized for Provisioned IOPS for fast, consistent performance\.

## Modifying a DB Instance to Be a Multi\-AZ Deployment<a name="Concepts.MultiAZ.Migrating"></a>

If you have a DB instance in a Single\-AZ deployment and you modify it to be a Multi\-AZ deployment \(for engines other than SQL Server or Amazon Aurora\), Amazon RDS takes several steps\. First, Amazon RDS takes a snapshot of the primary DB instance from your deployment and then restores the snapshot into another Availability Zone\. Amazon RDS then sets up synchronous replication between your primary DB instance and the new instance\. This action avoids downtime when you convert from Single\-AZ to Multi\-AZ, but you can experience a significant performance impact when first converting to Multi\-AZ\. This impact is more noticeable for large and write\-intensive DB instances\.

Once the modification is complete, Amazon RDS triggers an event \(RDS\-EVENT\-0025\) that indicates the process is complete\. You can monitor Amazon RDS events; for more information about events, see [Using Amazon RDS Event Notification](USER_Events.md)\.

## Failover Process for Amazon RDS<a name="Concepts.MultiAZ.Failover"></a>

In the event of a planned or unplanned outage of your DB instance, Amazon RDS automatically switches to a standby replica in another Availability Zone if you have enabled Multi\-AZ\. The time it takes for the failover to complete depends on the database activity and other conditions at the time the primary DB instance became unavailable\. Failover times are typically 60\-120 seconds\. However, large transactions or a lengthy recovery process can increase failover time\. When the failover is complete, it can take additional time for the RDS console UI to reflect the new Availability Zone\.

The failover mechanism automatically changes the DNS record of the DB instance to point to the standby DB instance\. As a result, you need to re\-establish any existing connections to your DB instance\. Due to how the Java DNS caching mechanism works, you may need to reconfigure your JVM environment\. For more information on how to manage a Java application that caches DNS values in the case of a failover, see the [AWS SDK for Java](http://docs.aws.amazon.com/AWSSdkDocsJava/latest/DeveloperGuide/java-dg-jvm-ttl.html)\. 

Amazon RDS handles failovers automatically so you can resume database operations as quickly as possible without administrative intervention\. The primary DB instance switches over automatically to the standby replica if any of the following conditions occur: 

+ An Availability Zone outage 

+ The primary DB instance fails 

+ The DB instance's server type is changed 

+ The operating system of the DB instance is undergoing software patching 

+ A manual failover of the DB instance was initiated using **Reboot with failover**

There are several ways to determine if your Multi\-AZ DB instance has failed over:

+  DB event subscriptions can be setup to notify you via email or SMS that a failover has been initiated\. For more information about events, see [Using Amazon RDS Event Notification](USER_Events.md) 

+ You can view your DB events by using the Amazon RDS console or API actions\.

+  You can view the current state of your Multi\-AZ deployment by using the Amazon RDS console and API actions\. 

For information on how you can respond to failovers, reduce recovery time, and other best practices for Amazon RDS, see [Best Practices for Amazon RDS](CHAP_BestPractices.md)\.

## Related Topics<a name="Concepts.MultiAZ.Related"></a>

+ [Multi\-AZ Deployments for Microsoft SQL Server with Database Mirroring](USER_SQLServerMultiAZ.md)

+ [Licensing Microsoft SQL Server Multi\-AZ Deployments](SQLServer.Concepts.General.Licensing.md#SQLServer.Concepts.General.Licensing.MAZ)

+ [Licensing Oracle Multi\-AZ Deployments](CHAP_Oracle.md#Oracle.Concepts.Licensing.MAZ)