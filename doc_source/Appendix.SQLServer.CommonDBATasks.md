# Common DBA tasks for Microsoft SQL Server<a name="Appendix.SQLServer.CommonDBATasks"></a>

This section describes the Amazon RDS\-specific implementations of some common DBA tasks for DB instances that are running the Microsoft SQL Server database engine\. In order to deliver a managed service experience, Amazon RDS does not provide shell access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. 

**Note**  
When working with a SQL Server DB instance, you can run scripts to modify a newly created database, but you cannot modify the \[model\] database, the database used as the model for new databases\. 

**Topics**
+ [Accessing the tempdb database on Microsoft SQL Server DB instances on Amazon RDS](SQLServer.TempDB.md)
+ [Analyzing your database workload on an Amazon RDS for SQL Server DB instance with Database Engine Tuning Advisor](Appendix.SQLServer.CommonDBATasks.Workload.md)
+ [Collations and character sets for Microsoft SQL Server](Appendix.SQLServer.CommonDBATasks.Collation.md)
+ [Creating a database user](Appendix.SQLServer.CommonDBATasks.CreateUser.md)
+ [Determining a recovery model for your Microsoft SQL Server database](Appendix.SQLServer.CommonDBATasks.DatabaseRecovery.md)
+ [Determining the last failover time](Appendix.SQLServer.CommonDBATasks.LastFailover.md)
+ [Disabling fast inserts during bulk loading](Appendix.SQLServer.CommonDBATasks.DisableFastInserts.md)
+ [Dropping a Microsoft SQL Server database](Appendix.SQLServer.CommonDBATasks.DropMirrorDB.md)
+ [Renaming a Microsoft SQL Server database in a Multi\-AZ deployment](Appendix.SQLServer.CommonDBATasks.RenamingDB.md)
+ [Resetting the `db_owner` role password](Appendix.SQLServer.CommonDBATasks.ResetPassword.md)
+ [Restoring license\-terminated DB instances](Appendix.SQLServer.CommonDBATasks.RestoreLTI.md)
+ [Transitioning a Microsoft SQL Server database from OFFLINE to ONLINE](Appendix.SQLServer.CommonDBATasks.TransitionOnline.md)
+ [Using change data capture](Appendix.SQLServer.CommonDBATasks.CDC.md)
+ [Using SQL Server Agent](Appendix.SQLServer.CommonDBATasks.Agent.md)
+ [Working with Microsoft SQL Server logs](Appendix.SQLServer.CommonDBATasks.Logs.md)
+ [Working with trace and dump files](Appendix.SQLServer.CommonDBATasks.TraceFiles.md)