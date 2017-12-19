# Overview of Amazon Aurora<a name="Aurora.Overview"></a>

Amazon Aurora \(Aurora\) is a fully managed, MySQL\- and PostgreSQL\-compatible, relational database engine\. It combines the speed and reliability of high\-end commercial databases with the simplicity and cost\-effectiveness of open\-source databases\. It delivers up to five times the throughput of MySQL and up to three times the throughput of PostgreSQL without requiring changes to most of your existing applications\.

Aurora makes it simple and cost\-effective to set up, operate, and scale your MySQL and PostgreSQL deployments, freeing you to focus on your business and applications\. Amazon RDS provides administration for Aurora by handling routine database tasks such as provisioning, patching, backup, recovery, failure detection, and repair\. Amazon RDS also provides push\-button migration tools to convert your existing Amazon RDS for MySQL and Amazon RDS for PostgreSQL applications to Aurora\.

Amazon Aurora is a drop\-in replacement for MySQL and PostgreSQL\. The code, tools and applications you use today with your existing MySQL and PostgreSQL databases can be used with Amazon Aurora\.

When you create an Amazon Aurora instance, you create a DB cluster\. A DB cluster consists of one or more DB instances, and a cluster volume that manages the data for those instances\. An Aurora cluster volume is a virtual database storage volume that spans multiple Availability Zones, with each Availability Zone having a copy of the DB cluster data\. Two types of DB instances make up an Aurora DB cluster:

+ **Primary instance** – Supports read and write operations, and performs all of the data modifications to the cluster volume\. Each Aurora DB cluster has one primary instance\.

+ **Aurora Replica** – Supports only read operations\. Each Aurora DB cluster can have up to 15 Aurora Replicas in addition to the primary instance\. Multiple Aurora Replicas distribute the read workload, and by locating Aurora Replicas in separate Availability Zones you can also increase database availability\.

The following diagram illustrates the relationship between the cluster volume, the primary instance, and Aurora Replicas in an Aurora DB cluster\.

![\[Amazon Aurora Architecture\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraArch001.png)

## Availability<a name="Aurora.Overview.Availability"></a>

Availability in AWS Regions for Amazon Aurora varies by database engine compatibility\.


| Database Engine | Availability | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Availability for Amazon Aurora MySQL](Aurora.AuroraMySQL.md#Aurora.AuroraMySQL.Availability)  | 
|  Amazon Aurora PostgreSQL  |  See [Availability for Amazon Aurora PostgreSQL](Aurora.AuroraPostgreSQL.md#Aurora.AuroraPostgreSQL.Availability)  | 

## Aurora Endpoints<a name="Aurora.Overview.Endpoints"></a>

You can connect to DB instances in an Amazon Aurora DB cluster by using an endpoint\. An endpoint is a URL that contains a host address and a port, separated by a colon\. The following endpoints are available from an Aurora DB cluster\.

Cluster endpoint  
An endpoint for a Aurora DB cluster that connects to the current primary instance for that DB cluster\. Each Aurora DB cluster has a cluster endpoint\.  
The cluster endpoint provides failover support for read/write connections to the DB cluster\. If the current primary instance of a DB cluster fails, Aurora automatically fails over to a new primary instance\. During a failover, the DB cluster continues to serve connection requests to the cluster endpoint from the new primary instance, with minimal interruption of service\.  
The following example illustrates a cluster endpoint for an Aurora MySQL DB cluster\.  
`mydbcluster.cluster-123456789012.us-east-1.rds.amazonaws.com:3306`

Reader endpoint  
An endpoint for an Aurora DB cluster that connects to one of the available Aurora Replicas for that DB cluster\. Each Aurora DB cluster has a reader endpoint\.  
The reader endpoint provides load balancing support for read\-only connections to the DB cluster\. The DB cluster distributes connection requests to the reader endpoint among available Aurora Replicas\. If the DB cluster contains only a primary instance, the reader endpoint serves connection requests from the primary instance\. If an Aurora Replica is created for that DB cluster, the reader endpoint continues to serve connection requests to the reader endpoint from the new Aurora Replica, with minimal interruption in service\.  
The following example illustrates a reader endpoint for an Aurora MySQL DB cluster\.  
`mydbcluster.cluster-ro-123456789012.us-east-1.rds.amazonaws.com:3306`

Instance endpoint  
An endpoint for a DB instance in an Aurora DB cluster that connects to that specific DB instance\. Each DB instance in a DB cluster, regardless of instance type, has its own unique instance endpoint\.  
The instance endpoint provides direct control over connections to the DB cluster, for scenarios where using the cluster endpoint or reader endpoint may not be appropriate\. For example, your client application may require load balancing by read workload, instead of by connections, in which case you can configure multiple clients to connect to different Aurora Replicas in a DB cluster to distribute read workloads\. For an example that uses instance endpoints to improve connection speed after a failover, see [Fast Failover with Amazon Aurora PostgreSQL](AuroraPostgreSQL.BestPractices.md#AuroraPostgreSQL.BestPractices.FastFailover)\.  
The following example illustrates an instance endpoint for a DB instance in an Aurora MySQL DB cluster\.  
`mydbinstance.123456789012.us-east-1.rds.amazonaws.com:3306`

### Endpoint Considerations<a name="Aurora.Overview.Endpoints.Considerations"></a>

Some considerations for working with Aurora endpoints are as follows:

+ Before using an instance endpoint to connect to a specific DB instance in a DB cluster, consider using the cluster endpoint or reader endpoint for the DB cluster instead\.

  The cluster endpoint and reader endpoint provide support for high\-availability scenarios\. If the primary instance of a DB cluster fails, Aurora automatically fails over to a new primary instance\. It does so by either promoting an existing Aurora Replica to a new primary instance or creating a new primary instance\. If a failover occurs, you can use the cluster endpoint to reconnect to the newly promoted or created primary instance, or use the reader endpoint to reconnect to one of the other Aurora Replicas in the DB cluster\. 

  If you don't take this approach, you can still make sure that you're connecting to the right DB instance in the DB cluster for the intended operation\. To do so, you can manually or programmatically discover the resulting set of available DB instances in the DB cluster and confirm their instance types after failover, before using the instance endpoint of a specific DB instance\.

  For more information about failovers, see [Fault Tolerance for an Aurora DB Cluster](Aurora.Managing.md#Aurora.Managing.FaultTolerance)\.

+ The reader endpoint only load\-balances connections to available Aurora Replicas in an Aurora DB cluster\. It does not load\-balance specific queries\. If you want to load\-balance queries to distribute the read workload for a DB cluster, you need to manage that in your application and use instance endpoints to connect directly to Aurora Replicas to balance the load\.

+ During a failover, the reader endpoint might direct connections to the new primary instance of a DB cluster for a short time, when an Aurora Replica is promoted to the new primary instance\.

## Amazon Aurora Storage<a name="Aurora.Overview.Storage"></a>

Aurora data is stored in the cluster volume, which is a single, virtual volume that utilizes solid state disk \(SSD\) drives\. A cluster volume consists of copies of the data across multiple Availability Zones in a single region\. Because the data is automatically replicated across Availability Zones, your data is highly durable with less possibility of data loss\. This replication also ensures that your database is more available during a failover because the data copies already exist in the other Availability Zones and continue to serve data requests to the instances in your DB cluster\.

Aurora cluster volumes automatically grow as the amount of data in your database increases\. An Aurora cluster volume can grow to a maximum size of 64 terabytes \(TB\)\. Table size is limited to the size of the cluster volume\. That is, the maximum table size for a table in an Aurora DB cluster is 64 TB\. Even though an Aurora cluster volume can grow to up to 64 TB, you are only charged for the space that you use in an Aurora cluster volume\. For pricing information, see [Amazon RDS for Aurora Pricing](http://aws.amazon.com/rds/aurora/pricing)\. 

## Amazon Aurora Replication<a name="Aurora.Overview.Replication"></a>

Aurora Replicas are independent endpoints in an Aurora DB cluster\. They provide read\-only access to the data in the DB cluster volume\. They enable you to scale the read workload for your data over multiple replicated instances, to both improve the performance of data reads and also increase the availability of the data in your Aurora DB cluster\. Aurora Replicas are also failover targets and are quickly promoted if the primary instance for your Aurora DB cluster fails\.

For more information on Aurora Replicas and other options for replicating data in an Aurora DB cluster, see [Replication with Amazon Aurora](Aurora.Replication.md)\. 

## Amazon Aurora Reliability<a name="Aurora.Overview.Reliability"></a>

Aurora is designed to be reliable, durable, and fault tolerant\. You can architect your Aurora DB cluster to improve availability by doing things such as adding Aurora Replicas and placing them in different Availability Zones, and also Aurora includes several automatic features that make it a reliable database solution\.

### Storage Auto\-Repair<a name="Aurora.Overview.AutoRepair"></a>

Because Aurora maintains multiple copies of your data in three Availability Zones, the chance of losing data as a result of a disk failure is greatly minimized\. Aurora automatically detects failures in the disk volumes that make up the cluster volume\. When a segment of a disk volume fails, Aurora immediately repairs the segment\. When Aurora repairs the disk segment, it uses the data in the other volumes that make up the cluster volume to ensure that the data in the repaired segment is current\. As a result, Aurora avoids data loss and reduces the need to perform a point\-in\-time restore to recover from a disk failure\.

### Survivable Cache Warming<a name="Aurora.Overview.CacheWarming"></a>

Aurora "warms" the buffer pool cache when a database starts up after it has been shut down or restarted after a failure\. That is, Aurora preloads the buffer pool with the pages for known common queries that are stored in an in\-memory page cache\. This provides a performance gain by bypassing the need for the buffer pool to "warm up" from normal database use\.

The Aurora page cache is managed in a separate process from the database, which allows the page cache to survive independently of the database\. In the unlikely event of a database failure, the page cache remains in memory, which ensures that the buffer pool is warmed with the most current state when the database restarts\.

### Crash Recovery<a name="Aurora.Overview.CrashRecovery"></a>

Aurora is designed to recover from a crash almost instantaneously and continue to serve your application data\. Aurora performs crash recovery asynchronously on parallel threads, so that your database is open and available immediately after a crash\.

For more information about crash recovery, see [Fault Tolerance for an Aurora DB Cluster](Aurora.Managing.md#Aurora.Managing.FaultTolerance)\.

The following are considerations for binary logging and crash recovery on Aurora MySQL:

+ Enabling binary logging on Aurora directly affects the recovery time after a crash, because it forces the DB instance to perform binary log recovery\. 

+ The type of binary logging used affects the size and efficiency of logging\. For the same amount of database activity, some formats log more information than others in the binary logs\. The following settings for the `binlog_format` parameter result in different amounts of log data:

  + `ROW` — The most log data

  + `STATEMENT` — The least log data

  + `MIXED` — A moderate amount of log data that usually provides the best combination of data integrity and performance

  The amount of binary log data affects recovery time\. If there is more data logged in the binary logs, the DB instance must process more data during recovery, which increases recovery time\. 

+ Aurora does not need the binary logs to replicate data within a DB cluster or to perform point in time restore \(PITR\)\.

+ If you don't need the binary log for external replication \(or an external binary log stream\), we recommend that you set the `binlog_format` parameter to `OFF` to disable binary logging\. Doing so reduces recovery time\. 

For more information about Aurora binary logging and replication, see [Replication with Amazon Aurora](Aurora.Replication.md)\. For more information about the implications of different MySQL replication types, see [Advantages and Disadvantages of Statement\-Based and Row\-Based Replication](https://dev.mysql.com/doc/refman/5.6/en/replication-sbr-rbr.html) in the MySQL documentation\.

## Amazon Aurora Performance Enhancements<a name="Aurora.Overview.Performance"></a>

Amazon Aurora includes performance enhancements to support the diverse needs of high\-end commercial databases\.

### Amazon Aurora MySQL Performance Enhancements<a name="Aurora.Overview.Performance.AuroraMySQL"></a>

Aurora MySQL includes the following performance enhancements:

**Fast insert**  
Fast insert accelerates parallel inserts sorted by primary key and applies specifically to `LOAD DATA` and `INSERT INTO ... SELECT ...` statements\.

For more information about performance enhancements for Aurora MySQL, see [Amazon Aurora MySQL Performance Enhancements](Aurora.AuroraMySQL.md#Aurora.AuroraMySQL.Performance)\.

## Amazon Aurora Security<a name="Aurora.Overview.Security"></a>

Security for Amazon Aurora is managed at three levels:

+ To control who can perform Amazon RDS management actions on Aurora DB clusters and DB instances, you use AWS Identity and Access Management \(IAM\)\. When you connect to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS management operations\. For more information, see [Authentication and Access Control for Amazon RDS](UsingWithRDS.IAM.md)\.

  If you are using an IAM account to access the Amazon RDS console, you must first log on to the AWS Management Console with your IAM account, and then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds](https://console.aws.amazon.com/rds)\.

+ Aurora DB clusters must be created in an Amazon Virtual Private Cloud \(VPC\)\. To control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance for Aurora DB clusters in a VPC, you use a VPC security group\. These endpoint and port connections can be made using Secure Sockets Layer \(SSL\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to a DB instance\. For more information on VPCs, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md)\.

+ To authenticate logins and permissions for an Amazon Aurora DB cluster, you can take either of the following approaches, or a combination of them\.

  + You can take the same approach as with a stand\-alone instance of MySQL or PostgreSQL\.

    Techniques for authenticating logins and permissions for stand\-alone instances of MySQL or PostgreSQL, such as using SQL commands or modifying database schema tables, also work with Aurora\. For more information, see [Security with Amazon Aurora MySQL](AuroraMySQL.Security.md) or [Security with Amazon Aurora PostgreSQL](AuroraPostgreSQL.Security.md)\.

  + You can also use IAM database authentication for Aurora MySQL\.

    With IAM database authentication, you authenticate to your Aurora MySQL DB cluster by using an IAM user or IAM role and an authentication token\. An *authentication token* is a unique value that is generated using the Signature Version 4 signing process\. By using IAM database authentication, you can use the same credentials to control access to your AWS resources and your databases\. For more information, see [IAM Database Authentication for MySQL and Amazon Aurora](UsingWithRDS.IAMDBAuth.md)\.

### Using SSL with Aurora DB Clusters<a name="Aurora.Overview.Security.SSL"></a>

Amazon Aurora DB clusters support Secure Sockets Layer \(SSL\) connections from applications using the same process and public key as Amazon RDS DB instances\. For more information, see [Security with Amazon Aurora MySQL](AuroraMySQL.Security.md) or [Security with Amazon Aurora PostgreSQL](AuroraPostgreSQL.Security.md)\.

## Local Time Zone for Amazon Aurora DB Clusters<a name="Aurora.Overview.LocalTimeZone"></a>

By default, the time zone for an Amazon Aurora DB cluster is Universal Time Coordinated \(UTC\)\. You can set the time zone for instances in your DB cluster to the local time zone for your application instead\.

To set the local time zone for a DB cluster, set the `time_zone` parameter in the cluster parameter group for your DB cluster to one of the supported values listed later in this section\. When you set the `time_zone` parameter for a DB cluster, all instances in the DB cluster change to use the new local time zone\. If other Aurora DB clusters are using the same cluster parameter group, then all instances in those DB clusters change to use the new local time zone also\. For information on cluster\-level parameters, see [Amazon Aurora DB Cluster and DB Instance Parameters](Aurora.Managing.md#Aurora.Managing.ParameterGroups)\.

After you set the local time zone, all new connections to the database reflect the change\. If you have any open connections to your database when you change the local time zone, you won't see the local time zone update until after you close the connection and open a new connection\.

If you are replicating across regions, then the replication master DB cluster and the replica use different parameter groups \(parameter groups are unique to a region\)\. To use the same local time zone for each instance, you must set the `time_zone` parameter in the parameter groups for both the replication master and the replica\.

When you restore a DB cluster from a DB cluster snapshot, the local time zone is set to UTC\. You can update the time zone to your local time zone after the restore is complete\. If you restore a DB cluster to a point in time, then the local time zone for the restored DB cluster is the time zone setting from the parameter group of the restored DB cluster\.

You can set your local time zone to one of the values listed in the following table\. For some time zones, time values for certain date ranges can be reported incorrectly as noted in the table\. For Australia time zones, the time zone abbreviation returned is an outdated value as noted in the table\. 


|  Time Zone  |  Notes  | 
| --- | --- | 
|  `Africa/Harare`  |  This time zone setting can return incorrect values from 28 Feb 1903 21:49:40 GMT to 28 Feb 1903 21:55:48 GMT\.  | 
|  `Africa/Monrovia`  |   | 
|  `Africa/Nairobi`  |  This time zone setting can return incorrect values from 31 Dec 1939 21:30:00 GMT to 31 Dec 1959 21:15:15 GMT\.  | 
|  `Africa/Windhoek`  |   | 
|  `America/Bogota `  |  This time zone setting can return incorrect values from 23 Nov 1914 04:56:16 GMT to 23 Nov 1914 04:56:20 GMT\.  | 
|  `America/Caracas`  |   | 
|  `America/Chihuahua`  |   | 
|  `America/Cuiaba`  |   | 
|  `America/Denver`  |   | 
|  `America/Fortaleza`  |   | 
|  `America/Guatemala`  |   | 
|  `America/Halifax`  |  This time zone setting can return incorrect values from 27 Oct 1918 05:00:00 GMT to 31 Oct 1918 05:00:00 GMT\.  | 
|  `America/Manaus`  |   | 
|  `America/Matamoros`  |   | 
|  `America/Monterrey`  |   | 
|  `America/Montevideo`  |   | 
|  `America/Phoenix`  |   | 
|  `America/Tijuana`  |   | 
|  `Asia/Ashgabat`  |   | 
|  `Asia/Baghdad`  |   | 
|  `Asia/Baku`  |   | 
|  `Asia/Bangkok`  |   | 
|  `Asia/Beirut`  |   | 
|  `Asia/Calcutta`  |   | 
|  `Asia/Kabul`  |   | 
|  `Asia/Karachi`  |   | 
|  `Asia/Kathmandu`  |   | 
|  `Asia/Muscat `  |  This time zone setting can return incorrect values from 31 Dec 1919 20:05:36 GMT to 31 Dec 1919 20:05:40 GMT\.  | 
|  `Asia/Riyadh `  |  This time zone setting can return incorrect values from 13 Mar 1947 20:53:08 GMT to 31 Dec 1949 20:53:08 GMT\.  | 
|  `Asia/Seoul`  |  This time zone setting can return incorrect values from 30 Nov 1904 15:30:00 GMT to 07 Sep 1945 15:00:00 GMT\.  | 
|  `Asia/Shanghai`  |  This time zone setting can return incorrect values from 31 Dec 1927 15:54:08 GMT to 02 Jun 1940 16:00:00 GMT\.  | 
|  `Asia/Singapore`  |   | 
|  `Asia/Taipei`  |  This time zone setting can return incorrect values from 30 Sep 1937 16:00:00 GMT to 29 Sep 1979 15:00:00 GMT\.  | 
|  `Asia/Tehran`  |   | 
|  `Asia/Tokyo`  |  This time zone setting can return incorrect values from 30 Sep 1937 15:00:00 GMT to 31 Dec 1937 15:00:00 GMT\.  | 
|  `Asia/Ulaanbaatar`  |   | 
|  `Atlantic/Azores`  |  This time zone setting can return incorrect values from 24 May 1911 01:54:32 GMT to 01 Jan 1912 01:54:32 GMT\.  | 
|  `Australia/Adelaide`  |  The abbreviation for this time zone is returned as CST instead of ACDT/ACST\.  | 
|  `Australia/Brisbane`  |  The abbreviation for this time zone is returned as EST instead of AEDT/AEST\.  | 
|  `Australia/Darwin `  |  The abbreviation for this time zone is returned as CST instead of ACDT/ACST\.  | 
|  `Australia/Hobart`  |  The abbreviation for this time zone is returned as EST instead of AEDT/AEST\.  | 
|  `Australia/Perth`  |  The abbreviation for this time zone is returned as WST instead of AWDT/AWST\.  | 
|  `Australia/Sydney `  |  The abbreviation for this time zone is returned as EST instead of AEDT/AEST\.  | 
|  `Brazil/East`  |   | 
|  `Canada/Saskatchewan`  |  This time zone setting can return incorrect values from 27 Oct 1918 08:00:00 GMT to 31 Oct 1918 08:00:00 GMT\.  | 
|  `Europe/Amsterdam`  |   | 
|  `Europe/Athens`  |   | 
|  `Europe/Dublin`  |   | 
|  `Europe/Helsinki`  |  This time zone setting can return incorrect values from 30 Apr 1921 22:20:08 GMT to 30 Apr 1921 22:20:11 GMT\.  | 
|  `Europe/Paris`  |   | 
|  `Europe/Prague`  |   | 
|  `Europe/Sarajevo`  |   | 
|  `Pacific/Auckland`  |   | 
|  `Pacific/Guam`  |   | 
|  `Pacific/Honolulu`  |  This time zone setting can return incorrect values from 21 May 1933 11:30:00 GMT to 30 Sep 1945 11:30:00 GMT\.  | 
|  `Pacific/Samoa`  |  This time zone setting can return incorrect values from 01 Jan 1911 11:22:48 GMT to 01 Jan 1950 11:30:00 GMT\.  | 
|  `US/Alaska`  |   | 
|  `US/Central`  |   | 
|  `US/Eastern`  |   | 
|  `US/East-Indiana`  |   | 
|  `US/Pacific`  |   | 
|  `UTC`  |   | 