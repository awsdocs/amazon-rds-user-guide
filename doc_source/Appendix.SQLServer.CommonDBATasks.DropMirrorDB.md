# Dropping a Microsoft SQL Server Database in a Multi\-AZ with Mirroring Deployment<a name="Appendix.SQLServer.CommonDBATasks.DropMirrorDB"></a>

You can drop a database on an Amazon RDS DB instance running Microsoft SQL Server in a Multi\-AZ deployment using Mirroring\. You can use the following commands: 

```
ALTER DATABASE <database_name> SET PARTNER OFF;
GO
DROP DATABASE <database_name>;
GO
```