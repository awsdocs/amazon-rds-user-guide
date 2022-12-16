# Cross\-Region automated backups<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups"></a>

By using backup replication in Amazon RDS, you can configure your RDS DB instance to replicate snapshots and transaction logs to a destination Region\. When backup replication is configured for a DB instance, RDS starts a cross\-Region copy of all snapshots and transaction logs when they're ready\. For more information, see [Replicating automated backups to another AWS Region](USER_ReplicateBackups.md)\.

Backup replication is available in all AWS Regions except the following:
+ Africa \(Cape Town\)
+ Asia Pacific \(Hong Kong\)
+ Asia Pacific \(Hyderabad\)
+ Asia Pacific \(Jakarta\)
+ Europe \(Milan\)
+ Europe \(Spain\)
+ Europe \(Zurich\)
+ Middle East \(Bahrain\)
+ Middle East \(UAE\)

For more detailed information on limitations for source and destination backup Regions, see [Replicating automated backups to another AWS Region](USER_ReplicateBackups.md)\.

Backup replication isn't available with the following engines:
+ RDS for MariaDB
+ RDS for MySQL

**Topics**
+ [Backup replication with RDS for Oracle](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.ora)
+ [Backup replication with RDS for PostgreSQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.pg)
+ [Backup replication with RDS for SQL Server](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.sq)

## Backup replication with RDS for Oracle<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.ora"></a>

Amazon RDS supports backup replication for all currently available versions of RDS for Oracle\.

## Backup replication with RDS for PostgreSQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.pg"></a>

Amazon RDS supports backup replication for all currently available versions of RDS for PostgreSQL\.

## Backup replication with RDS for SQL Server<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.sq"></a>

Amazon RDS supports backup replication for all currently available versions of RDS for SQL Server\.

**Note**  
Backup replication isn't supported for encrypted SQL Server DB instances\. Make sure to clear the **Enable encryption** check box when you create a SQL Server DB instance for which you want to use backup replication\.