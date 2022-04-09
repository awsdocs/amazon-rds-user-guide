# Creating a database user<a name="Appendix.SQLServer.CommonDBATasks.CreateUser"></a>

You can create a database user for your Amazon RDS for Microsoft SQL Server DB instance by running a T\-SQL script like the following example\. Use an application such as SQL Server Management Suite \(SSMS\)\. You log into the DB instance as the master user that was created when you created the DB instance\.

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
```

For an example of adding a database user to a role, see [Adding a user to the SQLAgentUser role](Appendix.SQLServer.CommonDBATasks.Agent.md#SQLServerAgent.AddUser)\.

**Note**  
If you get permission errors when adding a user, you can restore privileges by modifying the DB instance master user password\. For more information, see [Resetting the `db_owner` role password](Appendix.SQLServer.CommonDBATasks.ResetPassword.md)\.