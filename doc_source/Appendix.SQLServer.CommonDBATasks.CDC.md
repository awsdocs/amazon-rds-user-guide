# Enabling and Disabling Change Data Capture<a name="Appendix.SQLServer.CommonDBATasks.CDC"></a>

Amazon RDS supports change data capture \(CDC\) for your DB instances running Microsoft SQL Server\. CDC captures changes that are made to the data in your tables, and stores metadata about each change that you can access later\. For more information, see [Change Data Capture](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture) in the Microsoft documentation\. 

To use CDC with your Amazon RDS DB instances, first enable CDC at the database level\. To enable CDC, run the following procedure\. 

```
1. exec msdb.dbo.rds_cdc_enable_db '<database name>'
```

After you enable CDC, any user in the `db_owner` role for that database can use the native Microsoft stored procedures to control CDC on that database\. For more information on CDC in SQL Server, see [Change Data Capture Functions \(Transact\-SQL\)](https://docs.microsoft.com/en-us/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql)\.

 To disable CDC, run the following procedure\. 

```
1. exec msdb.dbo.rds_cdc_disable_db '<database name>'
```