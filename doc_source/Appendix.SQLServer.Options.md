# Options for the Microsoft SQL Server Database Engine<a name="Appendix.SQLServer.Options"></a>

This section describes options, or additional features, that are available for Amazon RDS instances running the Microsoft SQL Server DB engine\. To enable these options, you add them to an option group, and then associate the option group with your DB instance\. For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\. 

Amazon RDS supports the following options for Microsoft SQL Server DB instances\. 


****  

| Option | Option ID | Engine Editions | 
| --- | --- | --- | 
|  [Native Backup and Restore](Appendix.SQLServer.Options.BackupRestore.md)  |  `SQLSERVER_BACKUP_RESTORE`  |  SQL Server Enterprise Edition SQL Server Standard Edition SQL Server Web Edition SQL Server Express Edition  | 
|  [Transparent Data Encryption](Appendix.SQLServer.Options.TDE.md)  |  `TRANSPARENT_DATA_ENCRYPTION`  |  SQL Server Enterprise Edition  | 