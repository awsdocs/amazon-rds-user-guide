# Using Advanced Auditing with an Amazon Aurora MySQL DB Cluster<a name="AuroraMySQL.Auditing"></a>

You can use the high\-performance Advanced Auditing feature in Amazon Aurora MySQL to audit database activity\. To do so, you enable the collection of audit logs by setting several DB cluster parameters\. When Advanced Auditing is enabled, you can use it to log any combination of supported events\. You can view or download the audit logs to review them\.

You must be using Aurora MySQL 1\.10\.1 or greater to use Advanced Auditing\. For more information about Aurora MySQL versions, see [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md)\.

## Enabling Advanced Auditing<a name="AuroraMySQL.Auditing.Enable"></a>

Use the parameters described in this section to enable and configure Advanced Auditing for your DB cluster\. 

Use the `server_audit_logging` parameter to enable or disable Advanced Auditing, and the `server_audit_events` parameter to specify what events to log\. 

Use the `server_audit_excl_users` and `server_audit_incl_users` parameters to specify who gets audited\. If `server_audit_excl_users` and `server_audit_incl_users` are empty \(the default\), all users are audited\. If you add users to `server_audit_incl_users` and leave `server_audit_excl_users` empty, then only those users are audited\. If you add users to `server_audit_excl_users` and leave `server_audit_incl_users` empty, then only those users are not audited, and all other users are\. If you add the same users to both `server_audit_excl_users` and `server_audit_incl_users`, then those users are audited because `server_audit_incl_users` is given higher priority\.

Configure Advanced Auditing by setting these parameters in the parameter group used by your DB cluster\. You can use the procedure shown in [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying) to modify DB cluster parameters using the AWS Management Console\. You can use the [modify\-db\-cluster\-parameter\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) AWS CLI command or the [ModifyDBClusterParameterGroup](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html) Amazon RDS API command to modify DB cluster parameters programmatically\.

Modifying these parameters doesn't require a DB cluster restart\.

### `server_audit_logging`<a name="AuroraMySQL.Auditing.Enable.server_audit_logging"></a>

Enables or disables Advanced Auditing\. This parameter defaults to OFF; set it to ON to enable Advanced Auditing\.  

### `server_audit_events`<a name="AuroraMySQL.Auditing.Enable.server_audit_events"></a>

Contains the comma\-delimited list of events to log\. Events must be specified in all caps, and there should be no white space between the list elements, for example: `CONNECT,QUERY_DDL`\. This parameter defaults to an empty string\.

You can log any combination of the following events: 

+ CONNECT – Logs both successful and failed connections and also disconnections\. This event includes user information\.

+ QUERY – Logs all queries in plain text, including queries that fail due to syntax or permission errors\.

+ QUERY\_DCL – Similar to the QUERY event, but returns only data control language \(DCL\) queries \(GRANT, REVOKE, and so on\)\.

+ QUERY\_DDL – Similar to the QUERY event, but returns only data definition language \(DDL\) queries \(CREATE, ALTER, and so on\)\.

+ QUERY\_DML – Similar to the QUERY event, but returns only data manipulation language \(DML\) queries \(INSERT, UPDATE, and so on\)\.

+ TABLE – Logs the tables that were affected by query execution\.

### `server_audit_excl_users`<a name="AuroraMySQL.Auditing.Enable.server_audit_excl_users"></a>

Contains the comma\-delimited list of user names for users whose activity isn't logged\. There should be no white space between the list elements, for example: `rdsadmin,user_1,user_2`\. This parameter defaults to an empty string\. Specified user names must match corresponding values in the `User` column of the `mysql.user` table\. For more information about user names, see [the MySQL documentation](https://dev.mysql.com/doc/refman/5.6/en/user-names.html)\.

Connect and disconnect events aren't affected by this variable; they are always logged if specified\. A user is logged if that user is also specified in the `server_audit_incl_users` parameter, because that setting has higher priority than `server_audit_excl_users`\.

### `server_audit_incl_users`<a name="AuroraMySQL.Auditing.Enable.server_audit_incl_users"></a>

Contains the comma\-delimited list of user names for users whose activity is logged\. There should be no white space between the list elements, for example: `user_3,user_4`\. This parameter defaults to an empty string\. Specified user names must match corresponding values in the `User` column of the `mysql.user` table\. For more information about user names, see [the MySQL documentation](https://dev.mysql.com/doc/refman/5.6/en/user-names.html)\.

Connect and disconnect events aren't affected by this variable; they are always logged if specified\. A user is logged even if that user is also specified in the `server_audit_excl_users` parameter, because `server_audit_incl_users` has higher priority\.  

## Viewing Audit Logs<a name="AuroraMySQL.Auditing.View"></a>

You can view and download the audit logs by using the AWS console\. On the **Instances** page, click the DB instance to show its details, then scroll to the **Logs** section\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-log.png)

To download a log file, select that file in the **Logs** section and then choose **Download**\.

You can also get a list of the log files by using the [describe\-db\-log\-files](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-log-files.html) AWS CLI command\. You can view the content of a log file by using the [download\-db\-log\-file\-portion](http://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html) AWS CLI command, and download a log file by using the [DownloadCompleteDBLogFile](RESTReference.md#RESTReference.DownloadCompleteDBLogFile) REST API\.

## Audit Log Details<a name="AuroraMySQL.Auditing.Logs"></a>

Log files are in UTF\-8 format\. Logs are written in multiple files, the number of which varies based on instance size\. To see the latest events, you might have to review all of the audit log files\. 

Log entries are not in sequential order\. You can use the timestamp value for ordering\. 

Log files are rotated when they reach 100 MB in aggregate\. This limit is not configurable\.

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
|  object  |  For QUERY events, this value indicates the executed query\. For TABLE events, it indicates the table name\.  | 
|  retcode  |  The return code of the logged operation\.  | 