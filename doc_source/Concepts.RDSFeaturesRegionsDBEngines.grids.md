# Supported features in Amazon RDS by AWS Region and DB engine<a name="Concepts.RDSFeaturesRegionsDBEngines.grids"></a>

Support for Amazon RDS features and options varies across AWS Regions and specific versions of each DB engine\. To identify RDS DB engine version support and availability in a given AWS Region, you can use the following sections\.

Amazon RDS\-native features are different from engine\-native features and options\. For more information on engine\-native features and options, see [Engine\-native features\.](#Concepts.RDS_Fea_Regions_DB-eng.Feature.EngineNativeFeatures)

**Topics**
+ [Feature quick reference](#Concepts.RDS_Fea_Regions_DB-eng.Feature.QuickReferenceTable)
+ [Cross\-Region automated backups](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups)
+ [Cross\-Region read replicas](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas)
+ [Database activity streams](#Concepts.RDS_Fea_Regions_DB-eng.Feature.DBActivityStreams)
+ [Dual\-stack mode](#Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode)
+ [Export snapshots to S3](#Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3)
+ [IAM database authentication](#Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication)
+ [Kerberos authentication](#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication)
+ [Multi\-AZ DB clusters](#Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters)
+ [Performance Insights](#Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights)
+ [RDS Custom](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom)
+ [Amazon RDS Proxy](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSProxy)
+ [Engine\-native features](#Concepts.RDS_Fea_Regions_DB-eng.Feature.EngineNativeFeatures)

The following tables use these patterns to specify version numbers and level of support:
+  **Version x\.y** – The specific version alone is supported\. 
+ **Version x\.y and higher** – The version and all higher minor versions are supported\. For example, "version 10\.11 and higher" means that versions 10\.11, 10\.11\.1, and 10\.12 are supported\.
+ **\-** – The feature isn't currently available for that particular RDS feature for the given RDS DB engine, or in that specific AWS Region\.

## Feature quick reference<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.QuickReferenceTable"></a>

The following quick reference table lists each feature and supported RDS DB engine\. Region and specific version availability appears in the later feature sections\.


| Feature | RDS for MariaDB | RDS for MySQL | RDS for Oracle | RDS for PostgreSQL | RDS for SQL Server | 
| --- | --- | --- | --- | --- | --- | 
| Cross\-Region automated backups | – | – | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.ora) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.pg) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.sq) | 
| Cross\-Region read replicas | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas) | – | 
| Database activity streams | – | – | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.DBActivityStreams) | – | – | 
| Dual\-stack mode | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode) | 
| Export Snapshot to Amazon S3 | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.mdb) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.my) | – | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.pg) | – | 
| AWS Identity and Access Management \(IAM\) database authentication | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.mdb) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.my) | – | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.pg) | – | 
| Kerberos authentication | – | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.my) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.ora) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.pg) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.sq) | 
| Multi\-AZ DB clusters | – | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.my) | – | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.pg) | – | 
| Performance Insights | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights) | 
| RDS Custom | – | – | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.ora) | – | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.sq) | 
| RDS Proxy | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.mdb) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.my) | – | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.pg) | [Available](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.sq) | 

## Cross\-Region automated backups<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups"></a>

By using backup replication in Amazon RDS, you can configure your RDS DB instance to replicate snapshots and transaction logs to a destination Region\. When backup replication is configured for a DB instance, RDS starts a cross\-Region copy of all snapshots and transaction logs when they're ready\. For more information, see [Replicating automated backups to another AWS Region](USER_ReplicateBackups.md)\.  

Backup replication isn't available with the following engines:
+ RDS for MariaDB
+ RDS for MySQL

Backup replication is available in all AWS Regions except the following:
+ Africa \(Cape Town\)
+ Asia Pacific \(Hong Kong\)
+ Asia Pacific \(Jakarta\)
+ Europe \(Milan\)

For more information on limitations for source and destination backup Regions, see [Replicating automated backups to another AWS Region](USER_ReplicateBackups.md)\.

**Topics**
+ [Backup replication with RDS for Oracle](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.ora)
+ [Backup replication with RDS for PostgreSQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.pg)
+ [Backup replication with RDS for SQL Server](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.sq)

### Backup replication with RDS for Oracle<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.ora"></a>

Backup replication for RDS for Oracle is available for the following versions:
+ For RDS for Oracle 21c, backup replication is available for all versions\.
+ For RDS for Oracle 19c, backup replication is available for all versions\.
+ For RDS for Oracle 12c, backup replication is available for Oracle Database version 12\.1\.0\.2\.v10 and higher\.

### Backup replication with RDS for PostgreSQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.pg"></a>

Amazon RDS supports backup replication for all versions of RDS for PostgreSQL\.

### Backup replication with RDS for SQL Server<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.sq"></a>

Amazon RDS supports backup replication for all versions of RDS for SQL Server\.

Backup replication isn't supported for encrypted SQL Server DB instances\. Make sure to clear the **Enable encryption** check box when you create a SQL Server DB instance for which you want to use backup replication\.

## Cross\-Region read replicas<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas"></a>

By using cross\-Region read replicas in Amazon RDS, you can create a MariaDB, MySQL, Oracle, or PostgreSQL read replica in a different Region from the source DB instance\. For more information, see [Creating a read replica in a different AWS Region](USER_ReadRepl.md#USER_ReadRepl.XRgn)\.

Cross\-Region read replicas aren't available with the following engines:
+ RDS for SQL Server

Cross\-Region read replicas are available for all Regions and RDS DB engine versions for the following engines:
+ RDS for MariaDB
+ RDS for MySQL
+ RDS for PostgreSQL

Cross\-Region read replicas for RDS for Oracle have the following version and Region limitations:
+ For RDS for Oracle 21c, cross\-Region read replicas aren't available\.
+ For RDS for Oracle 19c, cross\-Region read replicas are available for instances of Oracle Database 19c that aren't container database \(CBD\) instances\.
+ For RDS for Oracle 12c, cross\-Region read replicas are available for Oracle Enterprise Edition \(EE\) of Oracle Database 12c Release 1 \(12\.1\) using 12\.1\.0\.2\.v10 and higher 12c releases\.
+ You can replicate between the AWS GovCloud \(US\-East\) and AWS GovCloud \(US\-West\) Regions, but not into or out of AWS GovCloud \(US\)\.

For more information on additional requirements for cross\-Region read replicas with RDS for Oracle, see [Considerations for RDS for Oracle replicas](oracle-read-replicas.limitations.md)\. 

## Database activity streams<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.DBActivityStreams"></a>

 By using Database Activity Streams in Amazon RDS, you can monitor and set alarms for auditing activity in your Oracle database\. For more information, see [Overview of Database Activity Streams](DBActivityStreams.Overview.md)\.

Database activity streams aren't available with the following engines:
+ RDS for MariaDB
+ RDS for MySQL
+ RDS for PostgreSQL
+ RDS for SQL Server

Database activity streams for RDS for Oracle have the following version limitations:
+ For RDS for Oracle 21c, database activity streams aren't available\.
+ For RDS for Oracle 19c, database activity streams are available for Oracle Database 19\.0\.0\.0\.ru\-2019\-07\.rur\-2019\-07\.r1 and higher, using either Enterprise Edition or Standard Edition 2 \(SE2\)\.
+ For RDS for Oracle 12c, database activity streams aren't available\.

For more information on additional requirements for database activity streams with RDS for Oracle, see [Overview of Database Activity Streams](DBActivityStreams.Overview.md)\.

Database activity streams for RDS for Oracle are supported in all AWS Regions except the following:
+ China \(Beijing\) Region
+ China \(Ningxia\) Region
+ Middle East \(UAE\) Region
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

## Dual\-stack mode<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode"></a>

By using dual\-stack mode in RDS, resources can communicate with a DB instance over Internet Protocol version 4 \(IPv4\), Internet Protocol version 6 \(IPv6\), or both\. For more information, see [Dual\-stack mode](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.IP_addressing.dual-stack-mode)\.

Dual\-stack mode is available for all versions in all Regions, except in the Middle East \(UAE\) Region, for the following engines:
+ RDS for MariaDB
+ RDS for MySQL
+ RDS for Oracle
+ RDS for PostgreSQL

Dual\-stack mode for RDS for SQL Server is available in all Regions, except in the Middle East \(UAE\) Region, with the following version limitations:
+ For RDS for SQL Server 2019, dual\-stack mode is available for all versions\.
+ For RDS for SQL Server 2017, dual\-stack mode is available for all versions\.
+ For RDS for SQL Server 2016, dual\-stack mode is available for all versions\.
+ For RDS for SQL Server 2014, dual\-stack mode isn't available\.

## Export snapshots to S3<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3"></a>

You can export RDS DB snapshot data to an Amazon S3 bucket\. You can export all types of DB snapshots—including manual snapshots, automated system snapshots, and snapshots created by AWS Backup\. After the data is exported, you can analyze the exported data directly through tools like Amazon Athena or Amazon Redshift Spectrum\. For more information, see [Exporting DB snapshot data to Amazon S3](USER_ExportSnapshot.md)\.

Exporting snapshots to S3 isn't available with the following engines:
+ RDS for Oracle
+ RDS for SQL Server

Exporting snapshots to S3 is available in all Regions except the following:
+ Asia Pacific \(Jakarta\)
+ Middle East \(UAE\)
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

**Topics**
+ [Export snapshots to S3 with RDS for MariaDB](#Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.mdb)
+ [Export snapshots to S3 with RDS for MySQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.my)
+ [Export snapshots to S3 with RDS for PostgreSQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.pg)

### Export snapshots to S3 with RDS for MariaDB<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.mdb"></a>

Exporting snapshots to S3 with RDS for MariaDB is available for all versions\. 

### Export snapshots to S3 with RDS for MySQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.my"></a>

Exporting snapshots to S3 with RDS for MySQL is available for all versions\.

### Export snapshots to S3 with RDS for PostgreSQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.pg"></a>

Exporting snapshots to S3 with RDS for PostgreSQL is available for all versions\.

## IAM database authentication<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication"></a>

By using IAM database authentication in Amazon RDS, you can authenticate without a password when you connect to a DB instance\. Instead, you use an authentication token\. For more information, see [IAM database authentication for MariaDB, MySQL, and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. 

IAM database authentication isn't available with the following engines:
+ RDS for Oracle
+ RDS for SQL Server

**Topics**
+ [IAM database authentication with RDS for MariaDB](#Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.mdb)
+ [IAM database authentication with RDS for MySQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.my)
+ [IAM database authentication with RDS for PostgreSQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.pg)

### IAM database authentication with RDS for MariaDB<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.mdb"></a>

IAM database authentication for RDS for MariaDB is available in all Regions, exept for the Middle East \(UAE\) Region, for the following versions:
+ For RDS for MariaDB 10\.6, IAM database authentication is available for all versions\.

### IAM database authentication with RDS for MySQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.my"></a>

IAM database authentication for RDS for MySQL is available in all Regions for all versions\.

### IAM database authentication with RDS for PostgreSQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.pg"></a>

IAM database authentication for RDS for PostgreSQL is available in all Regions for all versions\.

## Kerberos authentication<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication"></a>

By using Kerberos authentication in Amazon RDS, you can support external authentication of database users using Kerberos and Microsoft Active Directory\. Using Kerberos and Active Directory provides the benefits of single sign\-on and centralized authentication of database users\. For more information, see [Kerberos authentication](database-authentication.md#kerberos-authentication)\. 

Kerberos authentication isn't available with the following engines:
+ RDS for MariaDB

**Topics**
+ [Kerberos authentication with RDS for MySQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.my)
+ [Kerberos authentication with RDS for Oracle](#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.ora)
+ [Kerberos authentication with RDS for PostgreSQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.pg)
+ [Kerberos authentication with RDS for SQL Server](#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.sq)

### Kerberos authentication with RDS for MySQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.my"></a>

Amazon RDS supports Kerberos authentication for all versions of RDS for MySQL\.

Kerberos authentication for RDS for MySQL is available in all Regions except the following:
+ Africa \(Cape Town\)
+ Asia Pacific \(Hong Kong\)
+ Asia Pacific \(Jakarta\)
+ Asia Pacific \(Osaka\)
+ Europe \(Milan\)
+ Europe \(Paris\)
+ Middle East \(Bahrain\)
+ Middle East \(UAE\)
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

### Kerberos authentication with RDS for Oracle<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.ora"></a>

Amazon RDS supports Kerberos authentication for all versions of RDS for Oracle\.

Kerberos authentication for RDS for Oracle is available in all Regions except the following:
+ Africa \(Cape Town\)
+ Asia Pacific \(Hong Kong\)
+ Asia Pacific \(Jakarta\)
+ Asia Pacific \(Osaka\)
+ China \(Beijing\)
+ China \(Ningxia\)
+ Europe \(Milan\)
+ Europe \(Paris\)
+ Middle East \(Bahrain\)
+ Middle East \(UAE\)

### Kerberos authentication with RDS for PostgreSQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.pg"></a>

Amazon RDS supports Kerberos authentication for all currently available versions of RDS for PostgreSQL\. For details, see [Amazon RDS for PostgreSQL updates](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html) in the *Release Notes for Aurora PostgreSQL*\.

Kerberos authentication for RDS for PostgreSQL is available in all AWS Regions except the following:
+ Africa \(Cape Town\)
+ Asia Pacific \(Hong Kong\)
+ Asia Pacific \(Jakarta\)
+ Asia Pacific \(Osaka\)
+ Europe \(Milan\)
+ Middle East \(Bahrain\)
+ Middle East \(UAE\)

### Kerberos authentication with RDS for SQL Server<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.sq"></a>

Amazon RDS supports Kerberos authentication for all versions of RDS for SQL Server in all AWS Regions except in the Middle East \(UAE\) Region\.

## Multi\-AZ DB clusters<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters"></a>

A Multi\-AZ DB cluster deployment in Amazon RDS provides a high availability deployment mode of Amazon RDS with two readable standby DB instances\. A Multi\-AZ DB cluster has a writer DB instance and two reader DB instances in three separate Availability Zones in the same Region\. Multi\-AZ DB clusters provide high availability, increased capacity for read workloads, and lower write latency when compared to Multi\-AZ DB instance deployments\. For more information, see [Multi\-AZ DB cluster deployments](multi-az-db-clusters-concepts.md)\. 

Multi\-AZ DB clusters aren't available with the following engines:
+ RDS for MariaDB
+ RDS for Oracle
+ RDS for SQL Server

**Topics**
+ [Multi\-AZ DB clusters with RDS for MySQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.my)
+ [Multi\-AZ DB clusters with RDS for PostgreSQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.pg)

### Multi\-AZ DB clusters with RDS for MySQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.my"></a>

Following are the supported engines and Region availability for Multi\-AZ DB clusters with RDS for MySQL\.


| Region | RDS for MySQL 8\.0 | 
| --- | --- | 
| US East \(Ohio\) | Version 8\.0\.28 and higher | 
| US East \(N\. Virginia\) | Version 8\.0\.28 and higher | 
| US West \(N\. California\) | – | 
| US West \(Oregon\) | Version 8\.0\.28 and higher | 
| Africa \(Cape Town\) | – | 
| Asia Pacific \(Hong Kong\) | – | 
| Asia Pacific \(Jakarta\) | – | 
| Asia Pacific \(Mumbai\) | – | 
| Asia Pacific \(Osaka\) | – | 
| Asia Pacific \(Seoul\) | – | 
| Asia Pacific \(Singapore\) | Version 8\.0\.28 and higher | 
| Asia Pacific \(Sydney\) | Version 8\.0\.28 and higher | 
| Asia Pacific \(Tokyo\) | Version 8\.0\.28 and higher | 
| Canada \(Central\) | – | 
| China \(Beijing\) | – | 
| China \(Ningxia\) | – | 
| Europe \(Frankfurt\) | Version 8\.0\.28 and higher | 
| Europe \(Ireland\) | Version 8\.0\.28 and higher | 
| Europe \(London\) | – | 
| Europe \(Milan\) | – | 
| Europe \(Paris\) | – | 
| Europe \(Stockholm\) | Version 8\.0\.28 and higher | 
| Middle East \(Bahrain\) | – | 
| Middle East \(UAE\) | – | 
| South America \(São Paulo\) | – | 
| AWS GovCloud \(US\-East\) | – | 
| AWS GovCloud \(US\-West\) | – | 

### Multi\-AZ DB clusters with RDS for PostgreSQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.pg"></a>

Following are the supported engine version and Region availability for Multi\-AZ DB clusters with RDS for PostgreSQL\.


| Region | RDS for PostgreSQL 13 | 
| --- | --- | 
| US East \(Ohio\) | Version 13\.4 | 
| US East \(N\. Virginia\) | Version 13\.4 | 
| US West \(N\. California\) | – | 
| US West \(Oregon\) | Version 13\.4 | 
| Africa \(Cape Town\) | – | 
| Asia Pacific \(Hong Kong\) | – | 
| Asia Pacific \(Jakarta\) | – | 
| Asia Pacific \(Mumbai\) | – | 
| Asia Pacific \(Osaka\) | – | 
| Asia Pacific \(Seoul\) | – | 
| Asia Pacific \(Singapore\) | Version 13\.4 | 
| Asia Pacific \(Sydney\) | Version 13\.4 | 
| Asia Pacific \(Tokyo\) | Version 13\.4 | 
| Canada \(Central\) | – | 
| China \(Beijing\) | – | 
| China \(Ningxia\) | – | 
| Europe \(Frankfurt\) | Version 13\.4 | 
| Europe \(Ireland\) | Version 13\.4 | 
| Europe \(London\) | – | 
| Europe \(Milan\) | – | 
| Europe \(Paris\) | – | 
| Europe \(Stockholm\) | Version 13\.4 | 
| Middle East \(Bahrain\) | – | 
| Middle East \(UAE\) | – | 
| South America \(São Paulo\) | – | 
| AWS GovCloud \(US\-East\) | – | 
| AWS GovCloud \(US\-West\) | – | 

## Performance Insights<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights"></a>

Performance Insights in Amazon RDS expands on existing Amazon RDS monitoring features to illustrate and help you analyze your database performance\. With the Performance Insights dashboard, you can visualize the database load on your Amazon RDS DB instance\. You can also filter the load by waits, SQL statements, hosts, or users\. For more information, see [Monitoring DB load with Performance Insights on Amazon RDS](USER_PerfInsights.md)\.

Performance Insights is available for all RDS DB engines and all versions\.

Performance Insights is available in all AWS Regions except the following:
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

## RDS Custom<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom"></a>

Amazon RDS Custom automates database administration tasks and operations\. By using RDS Custom, as a database administrator you can access and customize your database environment and operating system\. With RDS Custom, you can customize to meet the requirements of legacy, custom, and packaged applications\. For more information, see [Working with Amazon RDS Custom](rds-custom.md)\.

RDS Custom isn't available with the following engines:
+ RDS for MariaDB
+ RDS for MySQL
+ RDS for PostgreSQL

**Topics**
+ [RDS Custom with RDS for Oracle](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.ora)
+ [RDS Custom with RDS for SQL Server](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.sq)

### RDS Custom with RDS for Oracle<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.ora"></a>

Following are the supported engine versions and Region availability for RDS Custom with RDS for Oracle\.


| Region | RDS for Oracle 19c | RDS for Oracle 18c | RDS for Oracle 12c | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| US East \(N\. Virginia\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| US West \(N\. California\) | – | – | – | 
| US West \(Oregon\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Africa \(Cape Town\) | – | – | – | 
| Asia Pacific \(Hong Kong\) | – | – | – | 
| Asia Pacific \(Jakarta\) | – | – | – | 
| Asia Pacific \(Mumbai\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Asia Pacific \(Osaka\) | – | – | – | 
| Asia Pacific \(Seoul\) | – | – | – | 
| Asia Pacific \(Singapore\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Asia Pacific \(Sydney\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Asia Pacific \(Tokyo\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Canada \(Central\) | – | – | – | 
| China \(Beijing\) | – | – | – | 
| China \(Ningxia\) | – | – | – | 
| Europe \(Frankfurt\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Europe \(Ireland\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Europe \(London\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Europe \(Milan\) | – | – | – | 
| Europe \(Paris\) | – | – | – | 
| Europe \(Stockholm\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Middle East \(Bahrain\) | – | – | – | 
| Middle East \(UAE\) | – | – | – | 
| South America \(São Paulo\) | – | – | – | 
| AWS GovCloud \(US\-East\) | – | – | – | 
| AWS GovCloud \(US\-West\) | – | – | – | 

### RDS Custom with RDS for SQL Server<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.sq"></a>

Following are the supported engine versions and Region availability for RDS Custom with RDS for SQL Server\.


| Region | RDS for SQL Server 2019 | 
| --- | --- | 
| US East \(Ohio\) | Enterprise, Standard, or Web | 
| US East \(N\. Virginia\) | Enterprise, Standard, or Web | 
| US West \(N\. California\) | – | 
| US West \(Oregon\) | Enterprise, Standard, or Web | 
| Africa \(Cape Town\) | – | 
| Asia Pacific \(Hong Kong\) | – | 
| Asia Pacific \(Jakarta\) | – | 
| Asia Pacific \(Mumbai\) | Enterprise, Standard, or Web | 
| Asia Pacific \(Osaka\) | – | 
| Asia Pacific \(Seoul\) | – | 
| Asia Pacific \(Singapore\) | Enterprise, Standard, or Web | 
| Asia Pacific \(Sydney\) | Enterprise, Standard, or Web | 
| Asia Pacific \(Tokyo\) | Enterprise, Standard, or Web | 
| Canada \(Central\) | – | 
| China \(Beijing\) | – | 
| China \(Ningxia\) | – | 
| Europe \(Frankfurt\) | Enterprise, Standard, or Web | 
| Europe \(Ireland\) | Enterprise, Standard, or Web | 
| Europe \(London\) | Enterprise, Standard, or Web | 
| Europe \(Milan\) | – | 
| Europe \(Paris\) | – | 
| Europe \(Stockholm\) | Enterprise, Standard, or Web | 
| Middle East \(Bahrain\) | – | 
| Middle East \(UAE\) | – | 
| South America \(São Paulo\) | – | 
| AWS GovCloud \(US\-East\) | – | 
| AWS GovCloud \(US\-West\) | – | 

## Amazon RDS Proxy<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSProxy"></a>

Amazon RDS Proxy is a fully managed, highly available database proxy that makes applications more scalable by pooling and sharing established database connections\. For more information, see [Using Amazon RDS Proxy](rds-proxy.md)\.

RDS Proxy isn't available with RDS for Oracle\.

RDS Proxy is available in all Regions except the following:
+ Asia Pacific \(Jakarta\)
+ China \(Beijing\)
+ China \(Ningxia\)
+ Middle East \(UAE\)
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

**Topics**
+ [RDS Proxy with RDS for MariaDB](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.mdb)
+ [RDS Proxy with RDS for MySQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.my)
+ [RDS Proxy with RDS for PostgreSQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.pg)
+ [RDS Proxy with RDS for SQL Server](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.sq)

### RDS Proxy with RDS for MariaDB<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.mdb"></a>

Amazon RDS supports RDS Proxy for RDS for MariaDB versions 10\.5, 10\.4, 10\.3, and 10\.2\.

### RDS Proxy with RDS for MySQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.my"></a>

Amazon RDS supports RDS Proxy for all versions of RDS for MySQL\.

### RDS Proxy with RDS for PostgreSQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.pg"></a>

Amazon RDS supports RDS Proxy for all versions of RDS for PostgreSQL except version 14\.

### RDS Proxy with RDS for SQL Server<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.sq"></a>

Amazon RDS supports RDS Proxy for RDS for SQL Server 2014 and higher\.

## Engine\-native features<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.EngineNativeFeatures"></a>

Amazon RDS database engines also support many of the most common engine\-native features and functionality\. These features are different than the Amazon RDS\-native features listed on this page\. Some engine\-native features might have limited support or restricted privileges\.

For more information on engine\-native features, see:
+ [MariaDB feature support on Amazon RDS](MariaDB.Concepts.FeatureSupport.md)
+ [MySQL feature support on Amazon RDS](MySQL.Concepts.FeatureSupport.md)
+ [RDS for Oracle features](Oracle.Concepts.FeatureSupport.md)
+ [Working with PostgreSQL features supported by Amazon RDS for PostgreSQL](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport)
+ [Microsoft SQL Server features on Amazon RDS](CHAP_SQLServer.md#SQLServer.Concepts.General.FeatureSupport)