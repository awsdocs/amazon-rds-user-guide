# Options for the Microsoft SQL Server Database Engine<a name="Appendix.SQLServer.Options"></a>

In this section, you can find descriptions for options, or additional features, that are available for Amazon RDS instances running the Microsoft SQL Server DB engine\. To enable these options, you add them to an option group, and then associate the option group with your DB instance\. For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\. 

Amazon RDS supports the following options for Microsoft SQL Server DB instances\. 


****  

| Option | Option ID | Engine Editions | 
| --- | --- | --- | 
|  [Native Backup and Restore](Appendix.SQLServer.Options.BackupRestore.md)  |  `SQLSERVER_BACKUP_RESTORE`  |  SQL Server Enterprise Edition SQL Server Standard Edition SQL Server Web Edition SQL Server Express Edition  | 
|  [Transparent Data Encryption](Appendix.SQLServer.Options.TDE.md)  |  `TRANSPARENT_DATA_ENCRYPTION`  |  SQL Server Enterprise Edition  | 
| [SQL Server Audit](Appendix.SQLServer.Options.Audit.md) | `SQLSERVER_AUDIT` |  In RDS, starting with SQL Server 2012, all editions of SQL Server support server level audits, and Enterprise edition also supports database level audits\.  Starting with SQL Server SQL Server 2016 \(13\.x\) SP1, all editions support both server and database level audits\.  For more information, see [SQL Server Audit \(Database Engine\)](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017)  | 