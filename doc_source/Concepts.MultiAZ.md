# High Availability \(Multi\-AZ\) for Amazon RDS<a name="Concepts.MultiAZ"></a>

Amazon RDS provides high availability and failover support for DB instances using Multi\-AZ deployments\. Amazon RDS uses several different technologies to provide failover support\. Multi\-AZ deployments for MariaDB, MySQL, Oracle, and PostgreSQL DB instances use Amazon's failover technology\. SQL Server DB instances use SQL Server Database Mirroring \(DBM\) or Always On Availability Groups \(AGs\)\.

In a Multi\-AZ deployment, Amazon RDS automatically provisions and maintains a synchronous standby replica in a different Availability Zone\. The primary DB instance is synchronously replicated across Availability Zones to a standby replica to provide data redundancy, eliminate I/O freezes, and minimize latency spikes during system backups\. Running a DB instance with high availability can enhance availability during planned system maintenance, and help protect your databases against DB instance failure and Availability Zone disruption\. For more information on Availability Zones, see [ Regions, Availability Zones, and Local Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

**Note**  
The high\-availability feature is not a scaling solution for read\-only scenarios; you cannot use a standby replica to serve read traffic\. To service read\-only traffic, you should use a read replica\. For more information, see [Working with Read Replicas](USER_ReadRepl.md)\.

![\[High Availability Scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/con-multi-AZ.png)

Using the RDS console, you can create a Multi\-AZ deployment by simply specifying Multi\-AZ when creating a DB instance\. You can use the console to convert existing DB instances to Multi\-AZ deployments by modifying the DB instance and specifying the Multi\-AZ option\. You can also specify a Multi\-AZ deployment with the AWS CLI or Amazon RDS API\. Use the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) or [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command, or the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) or [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) API operation\.

The RDS console shows the Availability Zone of the standby replica \(called the secondary AZ\)\. You can also use the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) CLI command or the [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) API operation to find the secondary AZ\.

DB instances using Multi\-AZ deployments can have increased write and commit latency compared to a Single\-AZ deployment, due to the synchronous data replication that occurs\. You might have a change in latency if your deployment fails over to the standby replica, although AWS is engineered with low\-latency network connectivity between Availability Zones\. For production workloads, we recommend that you use Provisioned IOPS and DB instance classes that are optimized for Provisioned IOPS for fast, consistent performance\. For more information about DB instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.

## Modifying a DB Instance to Be a Multi\-AZ Deployment<a name="Concepts.MultiAZ.Migrating"></a>

If you have a DB instance in a Single\-AZ deployment and modify it to a Multi\-AZ deployment \(for engines other than Amazon Aurora\), Amazon RDS takes several steps\. First, Amazon RDS takes a snapshot of the primary DB instance from your deployment and then restores the snapshot into another Availability Zone\. Amazon RDS then sets up synchronous replication between your primary DB instance and the new instance\. 

For information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

**Important**  
This action avoids downtime when you convert from Single\-AZ to Multi\-AZ, but you can experience a performance impact during and after converting to Multi\-AZ\. This impact can be significant for large write\-intensive DB instances\.  
To enable Multi\-AZ for a DB instance, RDS takes a snapshot of the primary DB instance's EBS volume and restores it on the newly created standby replica, and then synchronizes both volumes\. New volumes created from existing EBS snapshots load lazily in the background\. This capability permits large volumes to be restored from a snapshot quickly, but there is the possibility of added latency during and after the modification is complete\. For more information, see [ Restoring an Amazon EBS Volume from a Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-restoring-volume.html) in the Amazon EC2 documentation\. 

After the modification is complete, Amazon RDS triggers an event \(RDS\-EVENT\-0025\) that indicates the process is complete\. You can monitor Amazon RDS events; for more information about events, see [Using Amazon RDS Event Notification](USER_Events.md)\.

## Failover Process for Amazon RDS<a name="Concepts.MultiAZ.Failover"></a>

In the event of a planned or unplanned outage of your DB instance, Amazon RDS automatically switches to a standby replica in another Availability Zone if you have enabled Multi\-AZ\. The time it takes for the failover to complete depends on the database activity and other conditions at the time the primary DB instance became unavailable\. Failover times are typically 60–120 seconds\. However, large transactions or a lengthy recovery process can increase failover time\. When the failover is complete, it can take additional time for the RDS console to reflect the new Availability Zone\.

**Note**  
You can force a failover manually when you reboot a DB instance\. For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\.

Amazon RDS handles failovers automatically so you can resume database operations as quickly as possible without administrative intervention\. The primary DB instance switches over automatically to the standby replica if any of the following conditions occur: 
+ An Availability Zone outage 
+ The primary DB instance fails 
+ The DB instance's server type is changed 
+ The operating system of the DB instance is undergoing software patching 
+ A manual failover of the DB instance was initiated using **Reboot with failover**

There are several ways to determine if your Multi\-AZ DB instance has failed over:
+  DB event subscriptions can be set up to notify you by email or SMS that a failover has been initiated\. For more information about events, see [Using Amazon RDS Event Notification](USER_Events.md)\. 
+ You can view your DB events by using the Amazon RDS console or API operations\.
+  You can view the current state of your Multi\-AZ deployment by using the Amazon RDS console and API operations\. 

For information on how you can respond to failovers, reduce recovery time, and other best practices for Amazon RDS, see [Best Practices for Amazon RDS](CHAP_BestPractices.md)\.

### Setting the JVM TTL for DNS Name Lookups<a name="Concepts.MultiAZ.Failover.Java-DNS"></a>

The failover mechanism automatically changes the Domain Name System \(DNS\) record of the DB instance to point to the standby DB instance\. As a result, you need to re\-establish any existing connections to your DB instance\. In a Java virtual machine \(JVM\) environment, due to how the Java DNS caching mechanism works, you might need to reconfigure JVM settings\.

The JVM caches DNS name lookups\. When the JVM resolves a hostname to an IP address, it caches the IP address for a specified period of time, known as the *time\-to\-live* \(TTL\)\. 

Because AWS resources use DNS name entries that occasionally change, we recommend that you configure your JVM with a TTL value of no more than 60 seconds\. Doing this makes sure that when a resource's IP address changes, your application can receive and use the resource's new IP address by requerying the DNS\. 

On some Java configurations, the JVM default TTL is set so that it never refreshes DNS entries until the JVM is restarted\. Thus, if the IP address for an AWS resource changes while your application is still running, it can't use that resource until you manually restart the JVM and the cached IP information is refreshed\. In this case, it's crucial to set the JVM's TTL so that it periodically refreshes its cached IP information\. 

**Note**  
The default TTL can vary according to the version of your JVM and whether a security manager is installed\. Many JVMs provide a default TTL less than 60 seconds\. If you're using such a JVM and not using a security manager, you can ignore the rest of this topic\. For more information on security managers in Oracle, see [The Security Manager](https://docs.oracle.com/javase/tutorial/essential/environment/security.html) in the Oracle documentation\.

To modify the JVM's TTL, set the [ `networkaddress.cache.ttl`](https://docs.oracle.com/javase/7/docs/technotes/guides/net/properties.html) property value\. Use one of the following methods, depending on your needs: 
+  To set the property value globally for all applications that use the JVM, set `networkaddress.cache.ttl` in the `$JAVA_HOME/jre/lib/security/java.security` file\.

  ```
  networkaddress.cache.ttl=60					
  ```
+  To set the property locally for your application only, set `networkaddress.cache.ttl` in your application's initialization code before any network connections are established\.

  ```
  java.security.Security.setProperty("networkaddress.cache.ttl" , "60");					
  ```