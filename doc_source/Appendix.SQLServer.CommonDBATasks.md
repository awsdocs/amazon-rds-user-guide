# Common DBA Tasks for Microsoft SQL Server<a name="Appendix.SQLServer.CommonDBATasks"></a>

This section describes the Amazon RDS\-specific implementations of some common DBA tasks for DB instances that are running the Microsoft SQL Server database engine\. In order to deliver a managed service experience, Amazon RDS does not provide shell access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. 

**Note**  
When working with a SQL Server DB instance, you can run scripts to modify a newly created database, but you cannot modify the \[model\] database, the database used as the model for new databases\. 

**Topics**
+ [Accessing the tempdb Database on Microsoft SQL Server DB Instances on Amazon RDS](SQLServer.TempDB.md)
+ [Analyzing Your Database Workload on an Amazon RDS DB Instance with SQL Server Tuning Advisor](Appendix.SQLServer.CommonDBATasks.Workload.md)
+ [Collations and Character Sets for Microsoft SQL Server](Appendix.SQLServer.CommonDBATasks.Collation.md)
+ [Determining a Recovery Model for Your Microsoft SQL Server Database](Appendix.SQLServer.CommonDBATasks.DatabaseRecovery.md)
+ [Determining the Last Failover Time](Appendix.SQLServer.CommonDBATasks.LastFailover.md)
+ [Disabling Fast Inserts During Bulk Loading](Appendix.SQLServer.CommonDBATasks.DisableFastInserts.md)
+ [Dropping a Microsoft SQL Server Database](Appendix.SQLServer.CommonDBATasks.DropMirrorDB.md)
+ [Renaming a Microsoft SQL Server Database in a Multi\-AZ Deployment](Appendix.SQLServer.CommonDBATasks.RenamingDB.md)
+ [Resetting the `db_owner` Role Password](Appendix.SQLServer.CommonDBATasks.ResetPassword.md)
+ [Restoring License\-Terminated DB Instances](Appendix.SQLServer.CommonDBATasks.RestoreLTI.md)
+ [Transitioning a Microsoft SQL Server Database from OFFLINE to ONLINE](Appendix.SQLServer.CommonDBATasks.TransitionOnline.md)
+ [Using Change Data Capture](Appendix.SQLServer.CommonDBATasks.CDC.md)
+ [Using SQL Server Agent](Appendix.SQLServer.CommonDBATasks.Agent.md)
+ [Working with Microsoft SQL Server Logs](Appendix.SQLServer.CommonDBATasks.Logs.md)
+ [Working with Trace and Dump Files](Appendix.SQLServer.CommonDBATasks.TraceFiles.md)