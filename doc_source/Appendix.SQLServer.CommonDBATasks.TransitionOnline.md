# Transitioning a Microsoft SQL Server Database from OFFLINE to ONLINE<a name="Appendix.SQLServer.CommonDBATasks.TransitionOnline"></a>

You can transition your Microsoft SQL Server database on an Amazon RDS DB instance from `OFFLINE` to `ONLINE`\. 


****  

| SQL Server method | Amazon RDS method | 
| --- | --- | 
| ALTER DATABASE *db\_name* SET ONLINE; | EXEC rdsadmin\.dbo\.rds\_set\_database\_online *db\_name* | 