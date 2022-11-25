# Cross\-Region automated backups<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups"></a>

By using backup replication in Amazon RDS, you can configure your RDS DB instance to replicate snapshots and transaction logs to a destination Region\. When backup replication is configured for a DB instance, RDS starts a cross\-Region copy of all snapshots and transaction logs when they're ready\. For more information, see [Replicating automated backups to another AWS Region](USER_ReplicateBackups.md)\.  

Backup replication isn't available with the following engines:
+ RDS for MariaDB
+ RDS for MySQL

For more information on limitations for source and destination backup Regions, see [Replicating automated backups to another AWS Region](USER_ReplicateBackups.md)\.

**Topics**
+ [Backup replication with RDS for Oracle](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.ora)
+ [Backup replication with RDS for PostgreSQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.pg)
+ [Backup replication with RDS for SQL Server](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.sq)

## Backup replication with RDS for Oracle<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.ora"></a>

Following are the supported engines and Region availability for backup replication with RDS for Oracle\.


| Region | RDS for Oracle 21c | RDS for Oracle 19c | RDS for Oracle 12c | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| US East \(N\. Virginia\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| US West \(N\. California\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| US West \(Oregon\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Africa \(Cape Town\) | – | – | – | 
| Asia Pacific \(Hong Kong\) | – | – | – | 
| Asia Pacific \(Hyderabad\) | – | – | – | 
| Asia Pacific \(Jakarta\) | – | – | – | 
| Asia Pacific \(Mumbai\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Asia Pacific \(Osaka\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Asia Pacific \(Seoul\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Asia Pacific \(Singapore\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Asia Pacific \(Sydney\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Asia Pacific \(Tokyo\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Canada \(Central\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| China \(Beijing\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| China \(Ningxia\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Europe \(Frankfurt\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Europe \(Ireland\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Europe \(London\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Europe \(Milan\) | – | – | – | 
| Europe \(Paris\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Europe \(Spain\) | – | – | – | 
| Europe \(Stockholm\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Europe \(Zurich\) | – | – | – | 
| Middle East \(Bahrain\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| Middle East \(UAE\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| South America \(São Paulo\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| AWS GovCloud \(US\-East\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 
| AWS GovCloud \(US\-West\) | All versions | All versions | Version 12\.1\.0\.2\.v10 and higher | 

## Backup replication with RDS for PostgreSQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.pg"></a>

Following are the supported engines and Region availability for backup replication with RDS for PostgreSQL\.


| Region | RDS for PostgreSQL 14 | RDS for PostgreSQL 13 | RDS for PostgreSQL 12 | RDS for PostgreSQL 11 | RDS for PostgreSQL 10 | 
| --- | --- | --- | --- | --- | --- | 
| US East \(Ohio\) | All versions | All versions | All versions | All versions | All versions | 
| US East \(N\. Virginia\) | All versions | All versions | All versions | All versions | All versions | 
| US West \(N\. California\) | All versions | All versions | All versions | All versions | All versions | 
| US West \(Oregon\) | All versions | All versions | All versions | All versions | All versions | 
| Africa \(Cape Town\) | – | – | – | – | – | 
| Asia Pacific \(Hong Kong\) | – | – | – | – | – | 
| Asia Pacific \(Hyderabad\) | – | – | – | – | – | 
| Asia Pacific \(Jakarta\) | – | – | – | – | – | 
| Asia Pacific \(Mumbai\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Osaka\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Seoul\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Singapore\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Sydney\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Tokyo\) | All versions | All versions | All versions | All versions | All versions | 
| Canada \(Central\) | All versions | All versions | All versions | All versions | All versions | 
| China \(Beijing\) | All versions | All versions | All versions | All versions | All versions | 
| China \(Ningxia\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Frankfurt\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Ireland\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(London\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Milan\) | – | – | – | – | – | 
| Europe \(Paris\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Spain\) | – | – | – | – | – | 
| Europe \(Stockholm\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Zurich\) | – | – | – | – | – | 
| Middle East \(Bahrain\) | All versions | All versions | All versions | All versions | All versions | 
| Middle East \(UAE\) | All versions | All versions | All versions | All versions | 
| South America \(São Paulo\) | All versions | All versions | All versions | All versions | All versions | 
| AWS GovCloud \(US\-East\) | All versions | All versions | All versions | All versions | All versions | 
| AWS GovCloud \(US\-West\) | All versions | All versions | All versions | All versions | All versions | 

## Backup replication with RDS for SQL Server<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.sq"></a>

Following are the supported engines and Region availability for backup replication with RDS for SQL Server\.

**Note**  
Backup replication isn't supported for encrypted SQL Server DB instances\. Make sure to clear the **Enable encryption** check box when you create a SQL Server DB instance for which you want to use backup replication\. 


| Region | RDS for SQL Server 2019 | RDS for SQL Server 2017 | RDS for SQL Server 2016 | RDS for SQL Server 2014 | 
| --- | --- | --- | --- | --- | 
| US East \(Ohio\) | All versions | All versions | All versions | All versions | 
| US East \(N\. Virginia\) | All versions | All versions | All versions | All versions | 
| US West \(N\. California\) | All versions | All versions | All versions | All versions | 
| US West \(Oregon\) | All versions | All versions | All versions | All versions | 
| Africa \(Cape Town\) | – | – | – | – | 
| Asia Pacific \(Hong Kong\) | – | – | – | – | 
| Asia Pacific \(Hyderabad\) | – | – | – | 
| Asia Pacific \(Jakarta\) | – | – | – | – | 
| Asia Pacific \(Mumbai\) | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Osaka\) | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Seoul\) | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Singapore\) | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Sydney\) | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Tokyo\) | All versions | All versions | All versions | All versions | 
| Canada \(Central\) | All versions | All versions | All versions | All versions | 
| China \(Beijing\) | All versions | All versions | All versions | All versions | 
| China \(Ningxia\) | All versions | All versions | All versions | All versions | 
| Europe \(Frankfurt\) | All versions | All versions | All versions | All versions | 
| Europe \(Ireland\) | All versions | All versions | All versions | All versions | 
| Europe \(London\) | All versions | All versions | All versions | All versions | 
| Europe \(Milan\) | – | – | – | – | 
| Europe \(Paris\) | All versions | All versions | All versions | All versions | 
| Europe \(Spain\) | – | – | – | – | 
| Europe \(Stockholm\) | All versions | All versions | All versions | All versions | 
| Europe \(Zurich\) | – | – | – | – | 
| Middle East \(Bahrain\) | All versions | All versions | All versions | All versions | 
| Middle East \(UAE\) | All versions | All versions | All versions | All versions | 
| South America \(São Paulo\) | All versions | All versions | All versions | All versions | 
| AWS GovCloud \(US\-East\) | All versions | All versions | All versions | All versions | 
| AWS GovCloud \(US\-West\) | All versions | All versions | All versions | All versions | 