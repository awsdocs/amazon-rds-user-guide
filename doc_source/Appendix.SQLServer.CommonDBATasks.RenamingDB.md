# Renaming a Microsoft SQL Server Database in a Multi\-AZ Deployment<a name="Appendix.SQLServer.CommonDBATasks.RenamingDB"></a>

To rename a Microsoft SQL Server database instance that uses Multi\-AZ, use the following procedure:

1. First, turn off Multi\-AZ for the DB instance\.

1. Rename the database by running `rdsadmin.dbo.rds_modify_db_name`\.

1. Then, turn on Multi\-AZ Mirroring or Always On Availability Groups for the DB instance, to return it to its original state\.

For more information, see [Adding Multi\-AZ to a Microsoft SQL Server DB Instance](USER_SQLServerMultiAZ.md#USER_SQLServerMultiAZ.Adding)\. 

**Note**  
If your instance doesn't use Multi\-AZ, you don't need to change any settings before or after running `rdsadmin.dbo.rds_modify_db_name`\.

**Example: **In the following example, the `rdsadmin.dbo.rds_modify_db_name` stored procedure renames a database from **MOO** to **ZAR**\. This is similar to running the statement `DDL ALTER DATABASE [MOO] MODIFY NAME = [ZAR]`\. 

```
EXEC rdsadmin.dbo.rds_modify_db_name N'MOO', N'ZAR'
GO
```