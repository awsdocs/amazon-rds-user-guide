# Supported features in Amazon RDS by AWS Region and DB engine<a name="Concepts.RDSFeaturesRegionsDBEngines.grids"></a>

Support for Amazon RDS features and options varies across AWS Regions and specific versions of each DB engine\. To identify RDS DB engine version support and availability in a given AWS Region, you can use the following sections\.

Amazon RDS features are different from engine\-native features and options\. For more information on engine\-native features and options, see [Engine\-native features\.](Concepts.RDS_Fea_Regions_DB-eng.Feature.EngineNativeFeatures.md)

**Topics**
+ [Table conventions](#Concepts.RDS_Fea_Regions_DB-eng.Feature.TableConventions)
+ [Feature quick reference](#Concepts.RDS_Fea_Regions_DB-eng.Feature.QuickReferenceTable)
+ [Blue/Green Deployments](Concepts.RDS_Fea_Regions_DB-eng.Feature.BlueGreenDeployments.md)
+ [Cross\-Region automated backups](Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.md)
+ [Cross\-Region read replicas](Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.md)
+ [Database activity streams](Concepts.RDS_Fea_Regions_DB-eng.Feature.DBActivityStreams.md)
+ [Dual\-stack mode](Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode.md)
+ [Export snapshots to S3](Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.md)
+ [IAM database authentication](Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.md)
+ [Kerberos authentication](Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.md)
+ [Multi\-AZ DB clusters](Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.md)
+ [Performance Insights](Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights.md)
+ [RDS Custom](Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.md)
+ [Amazon RDS Proxy](Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSProxy.md)
+ [Secrets Manager integration](Concepts.RDS_Fea_Regions_DB-eng.Feature.SecretsManager.md)
+ [Engine\-native features](Concepts.RDS_Fea_Regions_DB-eng.Feature.EngineNativeFeatures.md)

## Table conventions<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.TableConventions"></a>

The tables in the feature sections use these patterns to specify version numbers and level of support:
+  **Version x\.y** – The specific version alone is supported\. 
+ **Version x\.y and higher** – The version and all higher minor versions are supported\. For example, "version 10\.11 and higher" means that versions 10\.11, 10\.11\.1, and 10\.12 are supported\.
+ **\-** – The feature isn't currently available for that particular RDS feature for the given RDS DB engine, or in that specific AWS Region\.

## Feature quick reference<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.QuickReferenceTable"></a>

The following quick reference table lists each feature and supported RDS DB engine\. Region and specific version availability appears in the later feature sections\.


| Feature | RDS for MariaDB | RDS for MySQL | RDS for Oracle | RDS for PostgreSQL | RDS for SQL Server | 
| --- | --- | --- | --- | --- | --- | 
| Blue/Green Deployments | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.BlueGreenDeployments.md) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.BlueGreenDeployments.md) | – | – | – | 
| Cross\-Region automated backups | – | – | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.ora) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.pg) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.sq) | 
| Cross\-Region read replicas | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.mdb) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.my) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.ora) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.pg) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionReadReplicas.sq) | 
| Database activity streams | – | – | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.DBActivityStreams.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.DBActivityStreams.ora) | – | – | 
| Dual\-stack mode | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode.mdb) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode.my) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode.ora) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode.pg) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.DualStackMode.sq) | 
| Export Snapshot to Amazon S3 | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.mdb) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.my) | – | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.pg) | – | 
| AWS Identity and Access Management \(IAM\) database authentication | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.mdb) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.my) | – | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.IamDatabaseAuthentication.pg) | – | 
| Kerberos authentication | – | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.my) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.ora) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.pg) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.sq) | 
| Multi\-AZ DB clusters | – | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.my) | – | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.pg) | – | 
| Performance Insights | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights.md) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights.md) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights.md) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights.md) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.PerformanceInsights.md) | 
| RDS Custom | – | – | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.ora) | – | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.sq) | 
| RDS Proxy | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSProxy.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.mdb) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSProxy.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.my) | – | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSProxy.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.pg) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSProxy.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDS_Proxy.sq) | 
| Secrets Manager integration | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.SecretsManager.md) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.SecretsManager.md) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.SecretsManager.md) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.SecretsManager.md) | [Available](Concepts.RDS_Fea_Regions_DB-eng.Feature.SecretsManager.md) | 