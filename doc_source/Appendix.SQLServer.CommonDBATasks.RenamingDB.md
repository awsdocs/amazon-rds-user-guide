# Renaming a Microsoft SQL Server Database in a Multi\-AZ with Mirroring Deployment<a name="Appendix.SQLServer.CommonDBATasks.RenamingDB"></a>

You can't rename a database on a Microsoft SQL Server DB instance that is in a SQL Server Multi\-AZ with Mirroring deployment\. If you need to rename a database on such an instance, first turn off Multi\-AZ with Mirroring for the DB instance, then rename the database, and finally turn Multi\-AZ with Mirroring back on for the DB instance\. For more information, see [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)\. 

You can rename a database using the following procedure, which renames a database from MOO to ZAR\. The procedure is analogous to the command `DDL ALTER DATABASE [MOO] MODIFY NAME = [ZAR]`\. 

```
EXEC rdsadmin.dbo.rds_modify_db_name N’MOO’, N’ZAR’
GO
```