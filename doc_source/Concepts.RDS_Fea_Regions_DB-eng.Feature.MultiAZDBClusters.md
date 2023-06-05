# Multi\-AZ DB clusters<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters"></a>

A Multi\-AZ DB cluster deployment in Amazon RDS provides a high availability deployment mode of Amazon RDS with two readable standby DB instances\. A Multi\-AZ DB cluster has a writer DB instance and two reader DB instances in three separate Availability Zones in the same Region\. Multi\-AZ DB clusters provide high availability, increased capacity for read workloads, and lower write latency when compared to Multi\-AZ DB instance deployments\. For more information, see [Multi\-AZ DB cluster deployments](multi-az-db-clusters-concepts.md)\. 

Multi\-AZ DB clusters aren't available with the following engines:
+ RDS for MariaDB
+ RDS for Oracle
+ RDS for SQL Server

**Topics**
+ [Multi\-AZ DB clusters with RDS for MySQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.my)
+ [Multi\-AZ DB clusters with RDS for PostgreSQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.pg)

## Multi\-AZ DB clusters with RDS for MySQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.my"></a>

The following Regions and engine versions are available for Multi\-AZ DB clusters with RDS for MySQL\.


| Region | RDS for MySQL 8\.0 | 
| --- | --- | 
| US East \(Ohio\) | Version 8\.0\.28 and higher | 
| US East \(N\. Virginia\) | Version 8\.0\.28 and higher | 
| US West \(N\. California\) | – | 
| US West \(Oregon\) | Version 8\.0\.28 and higher | 
| Africa \(Cape Town\) | Version 8\.0\.28 and higher | 
| Asia Pacific \(Hong Kong\) | Version 8\.0\.28 and higher | 
| Asia Pacific \(Hyderabad\) | – | 
| Asia Pacific \(Jakarta\) | Version 8\.0\.28 and higher | 
| Asia Pacific \(Melbourne\) | – | 
| Asia Pacific \(Mumbai\) | Version 8\.0\.28 and higher | 
| Asia Pacific \(Osaka\) | Version 8\.0\.28 and higher | 
| Asia Pacific \(Seoul\) | Version 8\.0\.28 and higher | 
| Asia Pacific \(Singapore\) | Version 8\.0\.28 and higher | 
| Asia Pacific \(Sydney\) | Version 8\.0\.28 and higher | 
| Asia Pacific \(Tokyo\) | Version 8\.0\.28 and higher | 
| Canada \(Central\) | Version 8\.0\.28 and higher | 
| China \(Beijing\) | Version 8\.0\.28 and higher | 
| China \(Ningxia\) | Version 8\.0\.28 and higher | 
| Europe \(Frankfurt\) | Version 8\.0\.28 and higher | 
| Europe \(Ireland\) | Version 8\.0\.28 and higher | 
| Europe \(London\) | Version 8\.0\.28 and higher | 
| Europe \(Milan\) | Version 8\.0\.28 and higher | 
| Europe \(Paris\) | Version 8\.0\.28 and higher | 
| Europe \(Spain\) | – | 
| Europe \(Stockholm\) | Version 8\.0\.28 and higher | 
| Europe \(Zurich\) | – | 
| Middle East \(Bahrain\) | Version 8\.0\.28 and higher | 
| Middle East \(UAE\) | – | 
| South America \(São Paulo\) | Version 8\.0\.28 and higher | 
| AWS GovCloud \(US\-East\) | – | 
| AWS GovCloud \(US\-West\) | – | 

You can also list the available versions in a Region for the db\.r5d\.large DB instance class by running the following AWS CLI command\.

For Linux, macOS, or Unix:

```
aws rds describe-orderable-db-instance-options \
--engine mysql \
--db-instance-class db.r5d.large \
--query '*[]|[?SupportsClusters == `true`].[EngineVersion]'  \
--output text
```

For Windows:

```
aws rds describe-orderable-db-instance-options ^
--engine mysql ^
--db-instance-class db.r5d.large ^
--query "*[]|[?SupportsClusters == `true`].[EngineVersion]"  ^
--output text
```

You can change the DB instance class to show the available engine versions for it\.

## Multi\-AZ DB clusters with RDS for PostgreSQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.pg"></a>

The following Regions and engine versions are available for Multi\-AZ DB clusters with RDS for PostgreSQL\.


| Region | RDS for PostgreSQL 15 | RDS for PostgreSQL 14 | RDS for PostgreSQL 13 | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| US East \(N\. Virginia\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| US West \(N\. California\) | – | – | – | 
| US West \(Oregon\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Africa \(Cape Town\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Asia Pacific \(Hong Kong\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Asia Pacific \(Hyderabad\) | – | – | – | 
| Asia Pacific \(Jakarta\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Asia Pacific \(Melbourne\) | – | – | – | 
| Asia Pacific \(Mumbai\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Asia Pacific \(Osaka\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Asia Pacific \(Seoul\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Asia Pacific \(Singapore\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Asia Pacific \(Sydney\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Asia Pacific \(Tokyo\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Canada \(Central\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| China \(Beijing\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| China \(Ningxia\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Europe \(Frankfurt\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Europe \(Ireland\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Europe \(London\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Europe \(Milan\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Europe \(Paris\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Europe \(Spain\) | – | – | – | 
| Europe \(Stockholm\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Europe \(Zurich\) | – | – | – | 
| Middle East \(Bahrain\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| Middle East \(UAE\) | – | – | – | 
| South America \(São Paulo\) | All PostgreSQL 15 versions | Version 14\.5 and higher | Version 13\.4 and version 13\.7 and higher | 
| AWS GovCloud \(US\-East\) | – | – | – | 
| AWS GovCloud \(US\-West\) | – | – | – | 

You can also list the available versions in a Region for the db\.r5d\.large DB instance class by running the following AWS CLI command\.

For Linux, macOS, or Unix:

```
aws rds describe-orderable-db-instance-options \
--engine postgres \
--db-instance-class db.r5d.large \
--query '*[]|[?SupportsClusters == `true`].[EngineVersion]'  \
--output text
```

For Windows:

```
aws rds describe-orderable-db-instance-options ^
--engine postgres ^
--db-instance-class db.r5d.large ^
--query "*[]|[?SupportsClusters == `true`].[EngineVersion]"  ^
--output text
```

You can change the DB instance class to show the available engine versions for it\.