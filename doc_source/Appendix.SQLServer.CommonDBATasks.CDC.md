# Using Change Data Capture<a name="Appendix.SQLServer.CommonDBATasks.CDC"></a>

Amazon RDS supports change data capture \(CDC\) for your DB instances running Microsoft SQL Server\. CDC captures changes that are made to the data in your tables\. It stores metadata about each change, which you can access later\. For more information about how CDC works, see [Change Data Capture](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture) in the Microsoft documentation\. 

Before you use CDC with your Amazon RDS DB instances, enable it in the database by running `msdb.dbo.rds_cdc_enable_db`\. You must have master user privileges to enable CDC in the Amazon RDS DB instance\. After CDC is enabled, any user who is `db_owner` of that database can enable or disable CDC on tables in that database\.

**Important**  
During restores, CDC will be disabled\. All of the related metadata is automatically removed from the database\. This applies to snapshot restores, point\-in\-time restores, and SQL Server Native restores from S3\. After performing one of these types of restores, you can re\-enable CDC and re\-specify tables to track\.

To enable CDC for a DB instance, run the `msdb.dbo.rds_cdc_enable_db` stored procedure\.

```
1. exec msdb.dbo.rds_cdc_enable_db 'database_name'
```

To disable CDC for a DB instance, run the `msdb.dbo.rds_cdc_disable_db` stored procedure\.

```
1. exec msdb.dbo.rds_cdc_disable_db 'database_name'
```

**Topics**
+ [Tracking Tables with Change Data Capture](#Appendix.SQLServer.CommonDBATasks.CDC.tables)
+ [Change Data Capture Jobs](#Appendix.SQLServer.CommonDBATasks.CDC.jobs)
+ [Change Data Capture for Multi\-AZ Instances](#Appendix.SQLServer.CommonDBATasks.CDC.Multi-AZ)

## Tracking Tables with Change Data Capture<a name="Appendix.SQLServer.CommonDBATasks.CDC.tables"></a>

After CDC is enabled on the database, you can start tracking specific tables\. You can choose the tables to track by running [sys\.sp\_cdc\_enable\_table](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sys-sp-cdc-enable-table-transact-sql)\.

```
 1. --Begin tracking a table
 2. exec sys.sp_cdc_enable_table   
 3.    @source_schema           = N'source_schema'
 4. ,  @source_name             = N'source_name'
 5. ,  @role_name               = N'role_name'
 6. 
 7. --The following parameters are optional:
 8.  
 9. --, @capture_instance       = 'capture_instance'
10. --, @supports_net_changes   = supports_net_changes
11. --, @index_name             = 'index_name'
12. --, @captured_column_list   = 'captured_column_list'
13. --, @filegroup_name         = 'filegroup_name'
14. --, @allow_partition_switch = 'allow_partition_switch'
15. ;
```

To view the CDC configuration for your tables, run [sys\.sp\_cdc\_help\_change\_data\_capture](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sys-sp-cdc-help-change-data-capture-transact-sql)\.

```
1. --View CDC configuration
2. exec sys.sp_cdc_help_change_data_capture 
3. 
4. --The following parameters are optional and must be used together.
5. --  'schema_name', 'table_name'
6. ;
```

For more information on CDC tables, functions, and stored procedures in SQL Server documentation, see the following:
+ [Change Data Capture Stored Procedures \(Transact\-SQL\)](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/change-data-capture-stored-procedures-transact-sql)
+ [Change Data Capture Functions \(Transact\-SQL\)](https://docs.microsoft.com/en-us/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql)
+ [Change Data Capture Tables \(Transact\-SQL\)](https://docs.microsoft.com/en-us/sql/relational-databases/system-tables/change-data-capture-tables-transact-sql)

## Change Data Capture Jobs<a name="Appendix.SQLServer.CommonDBATasks.CDC.jobs"></a>

When you enable CDC, SQL Server creates the CDC jobs\. Database owners \(`db_owner`\) can view, create, modify, and delete the CDC jobs\. However, the RDS system account owns them\. Therefore, the jobs aren't visible from native views, procedures, or in SQL Server Management Studio\.

To control behavior of CDC in a database, use native SQL Server procedures such as [sp\_cdc\_enable\_table](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sys-sp-cdc-enable-table-transact-sql) and [sp\_cdc\_start\_job](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sys-sp-cdc-start-job-transact-sql)\. To change CDC job parameters, like `maxtrans` and `maxscans`, you can use [sp\_cdc\_change\_job\.](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sys-sp-cdc-change-job-transact-sql)\.

To get more information regarding the CDC jobs, you can query the following dynamic management views: 
+ sys\.dm\_cdc\_errors
+ sys\.dm\_cdc\_log\_scan\_sessions
+ sysjobs
+ sysjobhistory

## Change Data Capture for Multi\-AZ Instances<a name="Appendix.SQLServer.CommonDBATasks.CDC.Multi-AZ"></a>

If you use CDC on a Multi\-AZ instance, make sure the mirror's CDC job configuration matches the one on the principal\. CDC jobs are mapped to the `database_id`\. If the database IDs on the secondary are different from the principal, then the jobs won't be associated with the correct database\. To try to prevent errors after failover, RDS drops and recreates the jobs on the new principal\. The recreated jobs use the parameters that the principal recorded before failover\.

Although this process runs quickly, it's still possible that the CDC jobs might run before RDS can correct them\. Here are three ways to force parameters to be consistent between primary and secondary replicas:
+ Use the same job parameters for all the databases that have CDC enabled\. 
+ Before you change the CDC job configuration, convert the Multi\-AZ instance to Single\-AZ\.
+ Manually transfer the parameters whenever you change them on the principal\.

To view and define the CDC parameters that are used to recreate the CDC jobs after a failover, use `rds_show_configuration` and `rds_set_configuration`\.

The following example returns the value set for cdc\_capture\_maxtrans\. For any parameter that is set to RDS\_DEFAULT, RDS automatically configures the value\.

```
-- Show configuration for each parameter on either primary and secondary replicas. 
exec rdsadmin.dbo.rds_show_configuration 'cdc_capture_maxtrans'
```

To set the configuration on the secondary, run `rdsadmin.dbo.rds_set_configuration`\. This procedure sets the parameter values for all of the databases on the secondary server\. These settings are used only after a failover\. The following example sets the `maxtrans` for all CDC capture jobs to *1000*:

```
--To set values on secondary. These are used after failover.
exec rdsadmin..rds_set_configuration 'cdc_capture_maxtrans' , 1000
```

To set the CDC job parameters on the principal, use [sys\.sp\_cdc\_change\_job](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sys-sp-cdc-change-job-transact-sql) instead\.