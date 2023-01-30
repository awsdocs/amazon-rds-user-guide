# Blue/Green Deployments<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.BlueGreenDeployments"></a>

A blue/green deployment copies a production database environment in a separate, synchronized staging environment\. By using Amazon RDS Blue/Green Deployments, you can make changes to the database in the staging environment without affecting the production environment\. For example, you can upgrade the major or minor DB engine version, change database parameters, or make schema changes in the staging environment\. When you are ready, you can promote the staging environment to be the new production database environment\. For more information, see [Using Amazon RDS Blue/Green Deployments for database updates](blue-green-deployments.md)\. 

The Blue/Green Deployments feature is available for the following engines:
+ RDS for MariaDB version 10\.2 and higher
+ RDS for MySQL version 5\.7 and higher
+ RDS for MySQL version 8\.0\.15 and higher

The Blue/Green Deployments feature isn't available with the following engines:
+ RDS for SQL Server
+ RDS for Oracle
+ RDS for PostgreSQL

The Blue/Green Deployments feature is available in all AWS Regions except for the Asia Pacific \(Melbourne\) Region\.