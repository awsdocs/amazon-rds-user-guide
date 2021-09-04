# Using SQL Server Agent<a name="Appendix.SQLServer.CommonDBATasks.Agent"></a>

With Amazon RDS, you can use SQL Server Agent on a DB instance running Microsoft SQL Server Enterprise Edition, Standard Edition, or Web Edition\. SQL Server Agent is a Microsoft Windows service that runs scheduled administrative tasks that are called jobs\. You can use SQL Server Agent to run T\-SQL jobs to rebuild indexes, run corruption checks, and aggregate data in a SQL Server DB instance\.

When you create a SQL Server DB instance, the master user name is enrolled in the `SQLAgentUserRole` role\.

SQL Server Agent can run a job on a schedule, in response to a specific event, or on demand\. For more information, see [SQL Server Agent](http://msdn.microsoft.com/en-us/library/ms189237) in the Microsoft documentation\.

**Note**  
Avoid scheduling jobs to run during the maintenance and backup windows for your DB instance\. The maintenance and backup processes that are launched by AWS could interrupt a job or cause it to be canceled\.  
Multi\-AZ deployments have a limit of 100 SQL Server Agent jobs\. If you need a higher limit, request an increase by contacting AWS Support\. Open the [AWS Support Center](https://console.aws.amazon.com/support/home#/) page, sign in if necessary, and choose **Create case**\. Choose **Service limit increase**\. Complete and submit the form\.

To view the history of an individual SQL Server Agent job in SQL Server Management Studio \(SSMS\), open Object Explorer, right\-click the job, and then choose **View History**\.

Because SQL Server Agent is running on a managed host in a DB instance, some actions aren't supported:
+ Running replication jobs and running command\-line scripts by using ActiveX, Windows command shell, or Windows PowerShell aren't supported\.
+ You can't manually start, stop, or restart SQL Server Agent\.
+ Email notifications through SQL Server Agent aren't available from a DB instance\.
+ SQL Server Agent alerts and operators aren't supported\.
+ Using SQL Server Agent to create backups isn't supported\. Use Amazon RDS to back up your DB instance\.

## Adding a user to the SQLAgentUser role<a name="Appendix.SQLServer.CommonDBATasks.Agent.AddUser"></a>

To allow an additional login or user to use SQL Server Agent, you must log in as the master user and do the following:

1. Create another server\-level login by using the `CREATE LOGIN` command\.

1. Create a user in `msdb` using `CREATE USER` command, and then link this user to the login that you created in the previous step\.

1. Add the user to the `SQLAgentUserRole` using the `sp_addrolemember` system stored procedure\.

For example, suppose your master user name is **admin** and you want to give access to SQL Server Agent to a user named **theirname** with a password **theirpassword**\.

**To add a user to the SQLAgentUser role**

1. Log in as the master user\.

1. Run the following commands:

   ```
   --Initially set context to master database
   USE [master];
   GO
   --Create a server-level login named theirname with password theirpassword
   CREATE LOGIN [theirname] WITH PASSWORD = 'theirpassword';
   GO
   --Set context to msdb database
   USE [msdb];
   GO
   --Create a database user named theirname and link it to server-level login theirname
   CREATE USER [theirname] FOR LOGIN [theirname];
   GO
   --Added database user theirname in msdb to SQLAgentUserRole in msdb
   EXEC sp_addrolemember [SQLAgentUserRole], [theirname];
   ```

## Deleting a SQL Server Agent job<a name="Appendix.SQLServer.CommonDBATasks.Agent.DeleteJob"></a>

You use the `sp_delete_job` stored procedure to delete SQL Server Agent jobs on Amazon RDS for Microsoft SQL Server\.

You can't use SSMS to delete SQL Server Agent jobs\. If you try to do so, you get an error message similar to the following:

```
The EXECUTE permission was denied on the object 'xp_regread', database 'mssqlsystemresource', schema 'sys'.
```

As a managed service, RDS is restricted from running procedures that access the Windows registry\. When you use SSMS, it tries to run a process \(`xp_regread`\) for which RDS isn't authorized\.

**Note**  
On RDS for SQL Server, you can delete only SQL Server Agent jobs that were created by the same login\.

**To delete a SQL Server Agent job**
+ Run the following T\-SQL statement:

  ```
  EXEC msdb..sp_delete_job @job_name = 'job_name';
  ```