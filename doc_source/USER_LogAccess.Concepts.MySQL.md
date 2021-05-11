# Accessing MySQL database log files<a name="USER_LogAccess.Concepts.MySQL"></a>

You can monitor the MySQL logs directly through the Amazon RDS console, Amazon RDS API, AWS CLI, or AWS SDKs\. You can also access MySQL logs by directing the logs to a database table in the main database and querying that table\. You can use the mysqlbinlog utility to download a binary log\. 

For more information about viewing, downloading, and watching file\-based database logs, see [Working with Amazon RDS database log files](USER_LogAccess.md)\.

**Topics**
+ [Overview of MySQL database logs](USER_LogAccess.MySQL.LogFileSize.md)
+ [Accessing MySQL error logs](USER_LogAccess.MySQL.Errorlog.md)
+ [Accessing the MySQL slow query and general logs](USER_LogAccess.MySQL.Generallog.md)
+ [Accessing the MySQL audit log](USER_LogAccess.MySQL.Auditlog.md)
+ [Publishing MySQL logs to Amazon CloudWatch Logs](USER_LogAccess.MySQLDB.PublishtoCloudWatchLogs.md)
+ [Managing table\-based MySQL logs](Appendix.MySQL.CommonDBATasks.Logs.md)
+ [Setting the binary logging format](USER_LogAccess.MySQL.BinaryFormat.md)
+ [Accessing MySQL binary logs](USER_LogAccess.MySQL.Binarylog.md)