# MariaDB Audit Plugin support<a name="Appendix.MySQL.Options.AuditPlugin"></a>

Amazon RDS supports using the MariaDB Audit Plugin on MySQL database instances\. The MariaDB Audit Plugin records database activity such as users logging on to the database, queries run against the database, and more\. The record of database activity is stored in a log file\.

**Note**  
Currently, the MariaDB Audit Plugin is only supported for the following RDS for MySQL versions:   
MySQL 8\.0\.25 and higher 8\.0 versions
All MySQL 5\.7 versions

An open source version of this plugin is available, so that you can use it with any MySQL database\. For more information, see the [ Audit Plugin for MySQL Server GitHub repository](https://github.com/aws/audit-plugin-for-mysql)\.

## Audit Plugin option settings<a name="Appendix.MySQL.Options.AuditPlugin.Options"></a>

Amazon RDS supports the following settings for the MariaDB Audit Plugin option\.


****  

| Option setting | Valid values | Default value | Description | 
| --- | --- | --- | --- | 
| `SERVER_AUDIT_FILE_PATH` | `/rdsdbdata/log/audit/` | `/rdsdbdata/log/audit/` |  The location of the log file\. The log file contains the record of the activity specified in `SERVER_AUDIT_EVENTS`\. For more information, see [Viewing and listing database log files](USER_LogAccess.Procedural.Viewing.md) and [MySQL database log files](USER_LogAccess.Concepts.MySQL.md)\.   | 
| `SERVER_AUDIT_FILE_ROTATE_SIZE` | 1–1000000000 | 1000000 |  The size in bytes that when reached, causes the file to rotate\. For more information, see [Overview of RDS for MySQL database logs](USER_LogAccess.MySQL.LogFileSize.md)\.   | 
| `SERVER_AUDIT_FILE_ROTATIONS` | 0–100 | 9 |  The number of log rotations to save\. For more information, see [Overview of RDS for MySQL database logs](USER_LogAccess.MySQL.LogFileSize.md) and [Downloading a database log file](USER_LogAccess.Procedural.Downloading.md)\.   | 
| `SERVER_AUDIT_EVENTS` | `CONNECT`, `QUERY`, `QUERY_DDL`, `QUERY_DML`, `QUERY_DML_NO_SELECT`, `QUERY_DCL` | `CONNECT`, `QUERY` |  The types of activity to record in the log\. Installing the MariaDB Audit Plugin is itself logged\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.MySQL.Options.AuditPlugin.html) For MySQL, `TABLE` is not supported\.  | 
| `SERVER_AUDIT_INCL_USERS` | Multiple comma\-separated values | None |  Include only activity from the specified users\. By default, activity is recorded for all users\. `SERVER_AUDIT_INCL_USERS` and `SERVER_AUDIT_EXCL_USERS` are mutually exclusive\. If you add values to `SERVER_AUDIT_INCL_USERS`, make sure no values are added to `SERVER_AUDIT_EXCL_USERS`\.   | 
| `SERVER_AUDIT_EXCL_USERS` | Multiple comma\-separated values | None |  Exclude activity from the specified users\. By default, activity is recorded for all users\. `SERVER_AUDIT_INCL_USERS` and `SERVER_AUDIT_EXCL_USERS` are mutually exclusive\. If you add values to `SERVER_AUDIT_EXCL_USERS`, make sure no values are added to `SERVER_AUDIT_INCL_USERS`\.   The `rdsadmin` user queries the database every second to check the health of the database\. Depending on your other settings, this activity can possibly cause the size of your log file to grow very large, very quickly\. If you don't need to record this activity, add the `rdsadmin` user to the `SERVER_AUDIT_EXCL_USERS` list\.    `CONNECT` activity is always recorded for all users, even if the user is specified for this option setting\.    | 
| `SERVER_AUDIT_LOGGING` | `ON` | `ON` |  Logging is active\. The only valid value is `ON`\. Amazon RDS does not support deactivating logging\. If you want to deactivate logging, remove the MariaDB Audit Plugin\. For more information, see [Removing the MariaDB Audit Plugin](#Appendix.MySQL.Options.AuditPlugin.Remove)\.   | 
| `SERVER_AUDIT_QUERY_LOG_LIMIT` | 0–2147483647 | 1024 |  The limit on the length of the query string in a record\.   | 

## Adding the MariaDB Audit Plugin<a name="Appendix.MySQL.Options.AuditPlugin.Add"></a>

The general process for adding the MariaDB Audit Plugin to a DB instance is the following: 
+ Create a new option group, or copy or modify an existing option group
+ Add the option to the option group
+ Associate the option group with the DB instance

After you add the MariaDB Audit Plugin, you don't need to restart your DB instance\. As soon as the option group is active, auditing begins immediately\. 

**Important**  
Adding the MariaDB Audit Plugin to a DB instance might cause an outage\. We recommend adding the MariaDB Audit Plugin during a maintenance window or during a time of low database workload\.

**To add the MariaDB Audit Plugin**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group\. Choose **mysql** for **Engine**, and choose **5\.7** or **8\.0** for **Major engine version**\. For more information, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **MARIADB\_AUDIT\_PLUGIN** option to the option group, and configure the option settings\. For more information about adding options, see [Adding an option to an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. For more information about each setting, see [Audit Plugin option settings](#Appendix.MySQL.Options.AuditPlugin.Options)\. 

1. Apply the option group to a new or existing DB instance\. 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

## Audit log format<a name="Appendix.MySQL.Options.AuditPlugin.LogFormat"></a>

Log files are represented as comma\-separated variable \(CSV\) files in UTF\-8 format\.

**Tip**  
Log file entries are not in sequential order\. To order the entries, use the timestamp value\. To see the latest events, you might have to review all log files\. For more flexibility in sorting and searching the log data, turn on the setting to upload the audit logs to CloudWatch and view them using the CloudWatch interface\.  
 To view audit data with more types of fields and with output in JSON format, you can also use the Database Activity Streams feature\. For more information, see [Monitoring Amazon RDS for Oracle with Database Activity Streams](DBActivityStreams.md)\. 

The audit log files include the following comma\-delimited information in rows, in the specified order:


| Field | Description | 
| --- | --- | 
|  timestamp  |  The Unix time stamp for the logged event with microsecond precision\.  | 
|  serverhost  |  The name of the instance that the event is logged for\.  | 
|  username  |  The connected user name of the user\.  | 
|  host  |  The host that the user connected from\.  | 
|  connectionid  |  The connection ID number for the logged operation\.  | 
|  queryid  |  The query ID number, which can be used for finding the relational table events and related queries\. For `TABLE` events, multiple lines are added\.  | 
|  operation  |  The recorded action type\. Possible values are: `CONNECT`, `QUERY`, `READ`, `WRITE`, `CREATE`, `ALTER`, `RENAME`, and `DROP`\.  | 
|  database  |  The active database, as set by the `USE` command\.  | 
|  object  |  For `QUERY` events, this value indicates the query that the database performed\. For `TABLE` events, it indicates the table name\.  | 
|  retcode  |  The return code of the logged operation\.  | 
|  connection\_type  |  The security state of the connection to the server\. Possible values are: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.MySQL.Options.AuditPlugin.html) This field is included only for RDS for MySQL version 5\.7\.34 and higher 5\.7 versions, and all 8\.0 versions\.  | 

## Viewing and downloading the MariaDB Audit Plugin log<a name="Appendix.MySQL.Options.AuditPlugin.Log"></a>

After you enable the MariaDB Audit Plugin, you access the results in the log files the same way you access any other text\-based log files\. The audit log files are located at `/rdsdbdata/log/audit/`\. For information about viewing the log file in the console, see [Viewing and listing database log files](USER_LogAccess.Procedural.Viewing.md)\. For information about downloading the log file, see [Downloading a database log file](USER_LogAccess.Procedural.Downloading.md)\. 

## Modifying MariaDB Audit Plugin settings<a name="Appendix.MySQL.Options.AuditPlugin.ModifySettings"></a>

After you enable the MariaDB Audit Plugin, you can modify the settings\. For more information about how to modify option settings, see [Modifying an option setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\. For more information about each setting, see [Audit Plugin option settings](#Appendix.MySQL.Options.AuditPlugin.Options)\. 

## Removing the MariaDB Audit Plugin<a name="Appendix.MySQL.Options.AuditPlugin.Remove"></a>

Amazon RDS doesn't support turning off logging in the MariaDB Audit Plugin\. However, you can remove the plugin from a DB instance\. When you remove the MariaDB Audit Plugin, the DB instance is restarted automatically to stop auditing\. 

To remove the MariaDB Audit Plugin from a DB instance, do one of the following: 
+ Remove the MariaDB Audit Plugin option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an option from an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption) 
+ Modify the DB instance and specify a different option group that doesn't include the plugin\. This change affects a single DB instance\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 