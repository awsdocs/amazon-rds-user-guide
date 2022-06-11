# MySQL database log files<a name="USER_LogAccess.Concepts.MySQL"></a>

You can monitor the MySQL logs directly through the Amazon RDS console, Amazon RDS API, AWS CLI, or AWS SDKs\. You can also access MySQL logs by directing the logs to a database table in the main database and querying that table\. You can use the mysqlbinlog utility to download a binary log\. 

For more information about viewing, downloading, and watching file\-based database logs, see [Monitoring Amazon RDS log files](USER_LogAccess.md)\.

**Topics**
+ [Overview of RDS for MySQL database logs](USER_LogAccess.MySQL.LogFileSize.md)
+ [Publishing MySQL logs to Amazon CloudWatch Logs](USER_LogAccess.MySQLDB.PublishtoCloudWatchLogs.md)
+ [Managing table\-based MySQL logs](Appendix.MySQL.CommonDBATasks.Logs.md)
+ [Configuring MySQL binary logging](USER_LogAccess.MySQL.BinaryFormat.md)
+ [Accessing MySQL binary logs](USER_LogAccess.MySQL.Binarylog.md)