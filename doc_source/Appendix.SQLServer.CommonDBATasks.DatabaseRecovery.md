# Determining a Recovery Model for Your Microsoft SQL Server Database<a name="Appendix.SQLServer.CommonDBATasks.DatabaseRecovery"></a>

In Amazon RDS, the recovery model, retention period, and database status are linked\.

It's important to understand the consequences before making a change to one of these settings\. Each setting can affect the others\. For example:
+ If you change a database's recovery model to SIMPLE or BULK\_LOGGED while backup retention is enabled, Amazon RDS resets the recovery model to FULL within five minutes\. This also results in RDS taking a snapshot of the DB instance\.
+ If you set backup retention to `0` days, RDS sets the recovery mode to SIMPLE\.
+ If you change a database's recovery model from SIMPLE to any other option while backup retention is set to `0` days, RDS resets the recovery model to SIMPLE\.

**Important**  
Never change the recovery model on Multi\-AZ instances, even if it seems you can do so—for example, by using ALTER DATABASE\. Backup retention, and therefore FULL recovery mode, is required for Multi\-AZ\. If you alter the recovery model, RDS immediately changes it back to FULL\.  
This automatic reset forces RDS to completely rebuild the mirror\. During this rebuild, the availability of the database is degraded for about 30\-90 minutes until the mirror is ready for failover\. The DB instance also experiences performance degradation in the same way it does during a conversion from Single\-AZ to Multi\-AZ\. How long performance is degraded depends on the database storage size—the bigger the stored database, the longer the degradation\.

For more information on SQL Server recovery models, see [Recovery Models \(SQL Server\)](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/recovery-models-sql-server) in the Microsoft documentation\.