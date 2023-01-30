# Options for MariaDB database engine<a name="Appendix.MariaDB.Options"></a>

Following, you can find descriptions for options, or additional features, that are available for Amazon RDS instances running the MariaDB DB engine\. To turn on these options, you add them to a custom option group, and then associate the option group with your DB instance\. For more information about working with option groups, see [Working with option groups](USER_WorkingWithOptionGroups.md)\.

Amazon RDS supports the following options for MariaDB: 


| Option ID | Engine versions | 
| --- | --- | 
|  `MARIADB_AUDIT_PLUGIN`  |  MariaDB 10\.3 and higher  | 

## MariaDB Audit Plugin support<a name="Appendix.MariaDB.Options.AuditPlugin"></a>

Amazon RDS supports using the MariaDB Audit Plugin on MariaDB database instances\. The MariaDB Audit Plugin records database activity such as users logging on to the database, queries run against the database, and more\. The record of database activity is stored in a log file\.

### Audit Plugin option settings<a name="Appendix.MariaDB.Options.AuditPlugin.Options"></a>

Amazon RDS supports the following settings for the MariaDB Audit Plugin option\. 


| Option setting | Valid values | Default value | Description | 
| --- | --- | --- | --- | 
| `SERVER_AUDIT_FILE_PATH` | `/rdsdbdata/log/audit/` | `/rdsdbdata/log/audit/` |  The location of the log file\. The log file contains the record of the activity specified in `SERVER_AUDIT_EVENTS`\. For more information, see [Viewing and listing database log files](USER_LogAccess.Procedural.Viewing.md) and [MariaDB database log files](USER_LogAccess.Concepts.MariaDB.md)\.   | 
| `SERVER_AUDIT_FILE_ROTATE_SIZE` | 1–1000000000 | 1000000 |  The size in bytes that when reached, causes the file to rotate\. For more information, see [Log file size](USER_LogAccess.Concepts.MariaDB.md#USER_LogAccess.MariaDB.LogFileSize)\.   | 
| `SERVER_AUDIT_FILE_ROTATIONS` | 0–100 | 9 |  The number of log rotations to save\. For more information, see [Log file size](USER_LogAccess.Concepts.MariaDB.md#USER_LogAccess.MariaDB.LogFileSize) and [Downloading a database log file](USER_LogAccess.Procedural.Downloading.md)\.   | 
| `SERVER_AUDIT_EVENTS` | `CONNECT`, `QUERY`, `TABLE`, `QUERY_DDL`, `QUERY_DML`, `QUERY_DML_NO_SELECT`, `QUERY_DCL` | `CONNECT`, `QUERY` |  The types of activity to record in the log\. Installing the MariaDB Audit Plugin is itself logged\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.MariaDB.Options.html)  | 
| `SERVER_AUDIT_INCL_USERS` | Multiple comma\-separated values | None |  Include only activity from the specified users\. By default, activity is recorded for all users\. `SERVER_AUDIT_INCL_USERS` and `SERVER_AUDIT_EXCL_USERS` are mutually exclusive\. If you add values to `SERVER_AUDIT_INCL_USERS`, make sure no values are added to `SERVER_AUDIT_EXCL_USERS`\.   | 
| `SERVER_AUDIT_EXCL_USERS` | Multiple comma\-separated values | None |  Exclude activity from the specified users\. By default, activity is recorded for all users\. `SERVER_AUDIT_INCL_USERS` and `SERVER_AUDIT_EXCL_USERS` are mutually exclusive\. If you add values to `SERVER_AUDIT_EXCL_USERS`, make sure no values are added to `SERVER_AUDIT_INCL_USERS`\.   The `rdsadmin` user queries the database every second to check the health of the database\. Depending on your other settings, this activity can possibly cause the size of your log file to grow very large, very quickly\. If you don't need to record this activity, add the `rdsadmin` user to the `SERVER_AUDIT_EXCL_USERS` list\.    `CONNECT` activity is always recorded for all users, even if the user is specified for this option setting\.    | 
| `SERVER_AUDIT_LOGGING` | `ON` | `ON` |  Logging is active\. The only valid value is `ON`\. Amazon RDS does not support deactivating logging\. If you want to deactivate logging, remove the MariaDB Audit Plugin\. For more information, see [Removing the MariaDB Audit Plugin](#Appendix.MariaDB.Options.AuditPlugin.Remove)\.   | 
| `SERVER_AUDIT_QUERY_LOG_LIMIT` | 0–2147483647 | 1024 |  The limit on the length of the query string in a record\.   | 

### Adding the MariaDB Audit Plugin<a name="Appendix.MariaDB.Options.AuditPlugin.Add"></a>

The general process for adding the MariaDB Audit Plugin to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

After you add the MariaDB Audit Plugin, you don't need to restart your DB instance\. As soon as the option group is active, auditing begins immediately\. 

**To add the MariaDB Audit Plugin**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group\. Choose **mariadb** for **Engine**, and choose **10\.3** or higher for **Major engine version**\. For more information, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **MARIADB\_AUDIT\_PLUGIN** option to the option group, and configure the option settings\. For more information about adding options, see [Adding an option to an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. For more information about each setting, see [Audit Plugin option settings](#Appendix.MariaDB.Options.AuditPlugin.Options)\. 

1. Apply the option group to a new or existing DB instance\. 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the DB instance and attaching the new option group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

### Viewing and downloading the MariaDB Audit Plugin log<a name="Appendix.MariaDB.Options.AuditPlugin.Log"></a>

After you enable the MariaDB Audit Plugin, you access the results in the log files the same way you access any other text\-based log files\. The audit log files are located at `/rdsdbdata/log/audit/`\. For information about viewing the log file in the console, see [Viewing and listing database log files](USER_LogAccess.Procedural.Viewing.md)\. For information about downloading the log file, see [Downloading a database log file](USER_LogAccess.Procedural.Downloading.md)\. 

### Modifying MariaDB Audit Plugin settings<a name="Appendix.MariaDB.Options.AuditPlugin.ModifySettings"></a>

After you enable the MariaDB Audit Plugin, you can modify settings for the plugin\. For more information about how to modify option settings, see [Modifying an option setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\. For more information about each setting, see [Audit Plugin option settings](#Appendix.MariaDB.Options.AuditPlugin.Options)\. 

### Removing the MariaDB Audit Plugin<a name="Appendix.MariaDB.Options.AuditPlugin.Remove"></a>

Amazon RDS doesn't support turning off logging in the MariaDB Audit Plugin\. However, you can remove the plugin from a DB instance\. When you remove the MariaDB Audit Plugin, the DB instance is restarted automatically to stop auditing\. 

To remove the MariaDB Audit Plugin from a DB instance, do one of the following: 
+ Remove the MariaDB Audit Plugin option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an option from an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption) 
+ Modify the DB instance and specify a different option group that doesn't include the plugin\. This change affects a single DB instance\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 