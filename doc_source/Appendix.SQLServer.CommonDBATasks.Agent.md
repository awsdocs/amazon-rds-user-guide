# Using SQL Server Agent<a name="Appendix.SQLServer.CommonDBATasks.Agent"></a>

With Amazon RDS, you can use SQL Server Agent on a DB instance running Microsoft SQL Server Standard, Web Edition, or Enterprise Edition\. SQL Server Agent is a Microsoft Windows service that runs scheduled administrative tasks, which are called jobs\. You can use SQL Server Agent to run T\-SQL jobs to rebuild indexes, run corruption checks, and aggregate data in a SQL Server DB instance\. 

SQL Server Agent can run a job on a schedule, in response to a specific event, or on demand\. For more information, see [SQL Server Agent](http://msdn.microsoft.com/en-us/library/ms189237) in the SQL Server documentation\. You should avoid scheduling jobs to run during the maintenance and backup windows for your DB instance because these maintenance and backup processes that are launched by AWS could interrupt the job or cause it to be cancelled\. Because Amazon RDS backs up your DB instance, you do not use SQL Server Agent to create backups\. 

To view the history of an individual SQL Server Agent job in the SQL Server Management Studio, you open Object Explorer, right\-click the job, and then click **View History**\. 

Because SQL Server Agent is running on a managed host in a DB instance, there are some actions that are not supported\. Running replication jobs and running command\-line scripts by using ActiveX, Windows command shell, or Windows PowerShell are not supported\. In addition, you cannot manually start, stop, or restart SQL Server Agent because its operation is managed by the host\. Email notifications through SQL Server Agent are not available from a DB instance\. 

When you create a SQL Server DB instance, the master user name is enrolled in the SQLAgentUserRole role\. To allow an additional login/user to use SQL Server Agent, you must log in as the master user and do the following\. 

1. Create another server\-level login by using the `CREATE LOGIN` command\. 

1. Create a user in msdb using `CREATE USER` command, and then link this user to the login that you created in the previous step\.

1. Add the user to the SQLAgentUserRole using the `sp_addrolemember` system stored procedure\.

For example, suppose your master user name is **admin** and you want to give access to SQL Server Agent to a user named **theirname** with a password **theirpassword**\. You would log in using the master user name and run the following commands\.

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

To delete a SQL Server Agent job, run the following T\-SQL statement\.

```
EXEC msdb..sp_delete_job @job_name = 'job_name';
```

**Note**  
Don't use the UI in SQL Server Management Console \(SSMS\) to delete a SQL Server Agent job\. If you do, you get an error message similar to the following:  

```
The EXECUTE permission was denied on the object 'xp_regread', database 'mssqlsystemresource', schema 'sys'.
```
This error occurs because, as a managed service, RDS is restricted from running procedures that access the Windows registry\. When you use SSMS to delete the job, it tries to run a process \(`xp_regread`\) that RDS isn't authorized to do\.