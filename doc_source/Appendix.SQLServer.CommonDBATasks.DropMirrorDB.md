# Dropping a Microsoft SQL Server Database in a Multi\-AZ Deployment<a name="Appendix.SQLServer.CommonDBATasks.DropMirrorDB"></a>

To drop a database in an Amazon RDS Multi-AZ deployment running Microsoft SQL Server, use the following command: 

```
-- Replace my-database-name with the name of the database you want to drop
EXECUTE msdb.dbo.rds_drop_database N'my-database-name'
```

**Note**  
This stored procedure will drop all existing connections to the target database and remove all of its backup history\.
