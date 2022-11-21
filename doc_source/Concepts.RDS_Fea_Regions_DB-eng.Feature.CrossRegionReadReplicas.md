# Cross\-Region read replicas<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas"></a>

By using cross\-Region read replicas in Amazon RDS, you can create a MariaDB, MySQL, Oracle, PostgreSQL, or SQL Server read replica in a different Region from the source DB instance\. For more information about cross\-Region read replicas, including source and destination Region considerations, see [Creating a read replica in a different AWS Region](USER_ReadRepl.md#USER_ReadRepl.XRgn)\.

**Topics**
+ [Cross\-Region read replicas with RDS for MariaDB](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.mdb)
+ [Cross\-Region read replicas with RDS for MySQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.my)
+ [Cross\-Region read replicas with RDS for Oracle](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.ora)
+ [Cross\-Region read replicas with RDS for PostgreSQL](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.pg)
+ [Cross\-Region read replicas with RDS for SQL Server](#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.sq)

## Cross\-Region read replicas with RDS for MariaDB<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.mdb"></a>

Cross\-Region read replicas with RDS for MariaDB are available in all Regions for the following versions:
+ RDS for MariaDB 10\.6 \(All versions\)
+ RDS for MariaDB 10\.5 \(All versions\)
+ RDS for MariaDB 10\.4 \(All versions\)
+ RDS for MariaDB 10\.3 \(All versions\)

## Cross\-Region read replicas with RDS for MySQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.my"></a>

Cross\-Region read replicas with RDS for MySQL are available in all Regions for the following versions:
+ RDS for MySQL 8\.0 \(All versions\)
+ RDS for MySQL 5\.7 \(All versions\)

## Cross\-Region read replicas with RDS for Oracle<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.ora"></a>

Cross\-Region read replicas for RDS for Oracle are available in all Regions with the following version limitations:
+ For RDS for Oracle 21c, cross\-Region read replicas aren't available\.
+ For RDS for Oracle 19c, cross\-Region read replicas are available for instances of Oracle Database 19c that aren't container database \(CBD\) instances\.
+ For RDS for Oracle 12c, cross\-Region read replicas are available for Oracle Enterprise Edition \(EE\) of Oracle Database 12c Release 1 \(12\.1\) using 12\.1\.0\.2\.v10 and higher 12c releases\.

For more information on additional requirements for cross\-Region read replicas with RDS for Oracle, see [Considerations for RDS for Oracle replicas](oracle-read-replicas.limitations.md)\. 

## Cross\-Region read replicas with RDS for PostgreSQL<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.pg"></a>

Cross\-Region read replicas with RDS for PostgreSQL are available in all Regions for the following versions:
+ RDS for PostgreSQL 14 \(All versions\)
+ RDS for PostgreSQL 13 \(All versions\)
+ RDS for PostgreSQL 12 \(All versions\)
+ RDS for PostgreSQL 11 \(All versions\)
+ RDS for PostgreSQL 10 \(All versions\)

## Cross\-Region read replicas with RDS for SQL Server<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.sq"></a>

Cross\-Region read replicas with RDS for SQL Server are available in all Regions except the following:
+ Africa \(Cape Town\)
+ Asia Pacific \(Jakarta\)
+ Asia Pacific \(Hong Kong\)
+ China \(Beijing\)
+ China \(Ningxia\)
+ Europe \(Milan\)
+ Middle East \(Bahrain\)
+ Middle East \(UAE\)

Cross\-Region read replicas with RDS for SQL Server are available for the following versions using Microsoft SQL Server Enterprise Edition:
+ RDS for SQL Server 2019 \(Version 15\.00\.4073\.23 and higher\)
+ RDS for SQL Server 2017 \(Version 14\.00\.3049\.1 and higher\)
+ RDS for SQL Server 2016 \(Version 13\.00\.5216\.0 and higher\)