# Determining a Recovery Model for Your Microsoft SQL Server Database<a name="Appendix.SQLServer.CommonDBATasks.DatabaseRecovery"></a>

In Amazon RDS, the recovery model, retention period, and database status are linked\. Changes to one can impact the other settings\. For example: 
+ Changing a database’s recovery model to “Simple” while backup retention is enabled will result in Amazon RDS setting the recovery model to “Full” within five minutes of the setting change\. This will also result in Amazon RDS taking a snapshot of the DB instance\.
+ Setting the backup retention to “0” days results in Amazon RDS setting the recovery mode to “Simple\.”
+ Changing a database’s recovery model from “Simple” to any other option while backup retention is set to “0” days results in Amazon RDS setting the recovery model back to “Simple\.”