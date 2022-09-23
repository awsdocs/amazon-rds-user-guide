# Monitoring database activity streams<a name="DBActivityStreams.Monitoring"></a>

Database activity streams monitor and report activities\. The stream of activity is collected and transmitted to Amazon Kinesis\. From Kinesis, you can monitor the activity stream, or other services and applications can consume the activity stream for further analysis\. You can find the underlying Kinesis stream name by using the AWS CLI command `describe-db-instances` or the RDS API `DescribeDBInstances` operation\.

Amazon RDS manages the Kinesis stream for you as follows:
+ Amazon RDS creates the Kinesis stream automatically with a 24\-hour retention period\. 
+  Amazon RDS scales the Kinesis stream if necessary\. 
+  If you stop the database activity stream or delete the DB instance, Amazon RDS deletes the Kinesis stream\. 

The following categories of activity are monitored and put in the activity stream audit log:
+ **SQL commands** – All SQL commands are audited, and also prepared statements, built\-in functions, and functions in PL/SQL\. Calls to stored procedures are audited\. Any SQL statements issued inside stored procedures or functions are also audited\.
+ **Other database information** – Activity monitored includes the full SQL statement, the row count of affected rows from DML commands, accessed objects, and the unique database name\. Database activity streams also monitor the bind variables and stored procedure parameters\. 
**Important**  
The full SQL text of each statement is visible in the activity stream audit log, including any sensitive data\. However, database user passwords are redacted if Oracle can determine them from the context, such as in the following SQL statement\.   

  ```
  ALTER ROLE role-name WITH password
  ```
+ **Connection information** – Activity monitored includes session and network information, the server process ID, and exit codes\.

If an activity stream has a failure while monitoring your DB instance, you are notified through RDS events\.

**Topics**
+ [Accessing an activity stream from Kinesis](#DBActivityStreams.KinesisAccess)
+ [Audit log contents and examples](#DBActivityStreams.AuditLog)
+ [Processing a database activity stream using the AWS SDK](#DBActivityStreams.CodeExample)

## Accessing an activity stream from Kinesis<a name="DBActivityStreams.KinesisAccess"></a>

When you enable an activity stream for a database, a Kinesis stream is created for you\. From Kinesis, you can monitor your database activity in real time\. To further analyze database activity, you can connect your Kinesis stream to consumer applications\. You can also connect the stream to compliance management applications such as IBM's Security Guardium or Imperva's SecureSphere Database Audit and Protection\.

You can access your Kinesis stream either from the RDS console or the Kinesis console\.

**To access an activity stream from Kinesis using the RDS console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the RDS for Oracle instance on which you started an activity stream\.

1. Choose **Configuration**\.

1. Under **Database activity stream**, choose the link under **Kinesis stream**\.

1. In the Kinesis console, choose **Monitoring** to begin observing the database activity\.

**To access an activity stream from Kinesis using the Kinesis console**

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose your activity stream from the list of Kinesis streams\.

   An activity stream's name includes the prefix `aws-rds-das-db-` followed by the resource ID of the database\. The following is an example\. 

   ```
   aws-rds-das-db-NHVOV4PCLWHGF52NP
   ```

   To use the Amazon RDS console to find the resource ID for the database, choose your DB instance from the list of databases, and then choose the **Configuration** tab\.

   To use the AWS CLI to find the full Kinesis stream name for an activity stream, use a [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) CLI request and note the value of `ActivityStreamKinesisStreamName` in the response\.

1. Choose **Monitoring** to begin observing the database activity\.

For more information about using Amazon Kinesis, see [What Is Amazon Kinesis Data Streams?](https://docs.aws.amazon.com/streams/latest/dev/introduction.html)\.

## Audit log contents and examples<a name="DBActivityStreams.AuditLog"></a>

Monitored events are represented in the database activity stream as JSON strings\. The structure consists of a JSON object containing a `DatabaseActivityMonitoringRecord`, which in turn contains a `databaseActivityEventList` array of activity events\. 

**Topics**
+ [Examples of an audit log for an activity stream](#DBActivityStreams.AuditLog.Examples)
+ [DatabaseActivityMonitoringRecords JSON object](#DBActivityStreams.AuditLog.DatabaseActivityMonitoringRecords)
+ [databaseActivityEvents JSON Object](#DBActivityStreams.AuditLog.databaseActivityEvents)
+ [databaseActivityEventList JSON array](#DBActivityStreams.AuditLog.databaseActivityEventList)

### Examples of an audit log for an activity stream<a name="DBActivityStreams.AuditLog.Examples"></a>

Following are sample decrypted JSON audit logs of activity event records\.

**Example Activity event record of a CONNECT SQL statement**  
Following is an activity event record of a login with the use of a `CONNECT` SQL statement \(`command`\) by a JDBC Thin Client \(`clientApplication`\)\.  

```
{
    "class": "Standard",
    "clientApplication": "JDBC Thin Client",
    "command": "LOGON",
    "commandText": null,
    "dbid": "0123456789",
    "databaseName": "ORCL",
    "dbProtocol": "oracle",
    "dbUserName": "TEST",
    "endTime": null,
    "errorMessage": null,
    "exitCode": 0,
    "logTime": "2021-01-15 00:15:36.233787",
    "netProtocol": "tcp",
    "objectName": null,
    "objectType": null,
    "paramList": [],
    "pid": 17904,
    "remoteHost": "123.456.789.012",
    "remotePort": "25440",
    "rowCount": null,
    "serverHost": "987.654.321.098",
    "serverType": "oracle",
    "serverVersion": "19.0.0.0.ru-2020-01.rur-2020-01.r1.EE.3",
    "serviceName": "oracle-ee",
    "sessionId": 987654321,
    "startTime": null,
    "statementId": 1,
    "substatementId": null,
    "transactionId": "0000000000000000",
    "engineNativeAuditFields": {
        "UNIFIED_AUDIT_POLICIES": "TEST_POL_EVERYTHING",
        "FGA_POLICY_NAME": null,
        "DV_OBJECT_STATUS": null,
        "SYSTEM_PRIVILEGE_USED": "CREATE SESSION",
        "OLS_LABEL_COMPONENT_TYPE": null,
        "XS_SESSIONID": null,
        "ADDITIONAL_INFO": null,
        "INSTANCE_ID": 1,
        "DBID": 123456789
        "DV_COMMENT": null,
        "RMAN_SESSION_STAMP": null,
        "NEW_NAME": null,
        "DV_ACTION_NAME": null,
        "OLS_PROGRAM_UNIT_NAME": null,
        "OLS_STRING_LABEL": null,
        "RMAN_SESSION_RECID": null,
        "OBJECT_PRIVILEGES": null,
        "OLS_OLD_VALUE": null,
        "XS_TARGET_PRINCIPAL_NAME": null,
        "XS_NS_ATTRIBUTE": null,
        "XS_NS_NAME": null,
        "DBLINK_INFO": null,
        "AUTHENTICATION_TYPE": "(TYPE\u003d(DATABASE));(CLIENT ADDRESS\u003d((ADDRESS\u003d(PROTOCOL\u003dtcp)(HOST\u003d205.251.233.183)(PORT\u003d25440))));",
        "OBJECT_EDITION": null,
        "OLS_PRIVILEGES_GRANTED": null,
        "EXCLUDED_USER": null,
        "DV_ACTION_OBJECT_NAME": null,
        "OLS_LABEL_COMPONENT_NAME": null,
        "EXCLUDED_SCHEMA": null,
        "DP_TEXT_PARAMETERS1": null,
        "XS_USER_NAME": null,
        "XS_ENABLED_ROLE": null,
        "XS_NS_ATTRIBUTE_NEW_VAL": null,
        "DIRECT_PATH_NUM_COLUMNS_LOADED": null,
        "AUDIT_OPTION": null,
        "DV_EXTENDED_ACTION_CODE": null,
        "XS_PACKAGE_NAME": null,
        "OLS_NEW_VALUE": null,
        "DV_RETURN_CODE": null,
        "XS_CALLBACK_EVENT_TYPE": null,
        "USERHOST": "a1b2c3d4e5f6.amazon.com",
        "GLOBAL_USERID": null,
        "CLIENT_IDENTIFIER": null,
        "RMAN_OPERATION": null,
        "TERMINAL": "unknown",
        "OS_USERNAME": "sumepate",
        "OLS_MAX_READ_LABEL": null,
        "XS_PROXY_USER_NAME": null,
        "XS_DATASEC_POLICY_NAME": null,
        "DV_FACTOR_CONTEXT": null,
        "OLS_MAX_WRITE_LABEL": null,
        "OLS_PARENT_GROUP_NAME": null,
        "EXCLUDED_OBJECT": null,
        "DV_RULE_SET_NAME": null,
        "EXTERNAL_USERID": null,
        "EXECUTION_ID": null,
        "ROLE": null,
        "PROXY_SESSIONID": 0,
        "DP_BOOLEAN_PARAMETERS1": null,
        "OLS_POLICY_NAME": null,
        "OLS_GRANTEE": null,
        "OLS_MIN_WRITE_LABEL": null,
        "APPLICATION_CONTEXTS": null,
        "XS_SCHEMA_NAME": null,
        "DV_GRANTEE": null,
        "XS_COOKIE": null,
        "DBPROXY_USERNAME": null,
        "DV_ACTION_CODE": null,
        "OLS_PRIVILEGES_USED": null,
        "RMAN_DEVICE_TYPE": null,
        "XS_NS_ATTRIBUTE_OLD_VAL": null,
        "TARGET_USER": null,
        "XS_ENTITY_TYPE": null,
        "ENTRY_ID": 1,
        "XS_PROCEDURE_NAME": null,
        "XS_INACTIVITY_TIMEOUT": null,
        "RMAN_OBJECT_TYPE": null,
        "SYSTEM_PRIVILEGE": null,
        "NEW_SCHEMA": null,
        "SCN": 5124715
    }
}
```
If a database activity stream isn't enabled, then the last field in the JSON document is `"engineNativeAuditFields": { }`\. 

**Example Activity event record of a CREATE TABLE statement**  
Following is an example of a `CREATE TABLE` event for your Oracle database\.  

```
{
    "class": "Standard",
    "clientApplication": "sqlplus@ip-12-34-5-678 (TNS V1-V3)",
    "command": "CREATE TABLE",
    "commandText": "CREATE TABLE persons(\n    person_id NUMBER GENERATED BY DEFAULT AS IDENTITY,\n    first_name VARCHAR2(50) NOT NULL,\n    last_name VARCHAR2(50) NOT NULL,\n    PRIMARY KEY(person_id)\n)",
    "dbid": "0123456789",
    "databaseName": "ORCL",
    "dbProtocol": "oracle",
    "dbUserName": "TEST",
    "endTime": null,
    "errorMessage": null,
    "exitCode": 0,
    "logTime": "2021-01-15 00:22:49.535239",
    "netProtocol": "beq",
    "objectName": "PERSONS",
    "objectType": "TEST",
    "paramList": [],
    "pid": 17687,
    "remoteHost": "123.456.789.0",
    "remotePort": null,
    "rowCount": null,
    "serverHost": "987.654.321.01",
    "serverType": "oracle",
    "serverVersion": "19.0.0.0.ru-2020-01.rur-2020-01.r1.EE.3",
    "serviceName": "oracle-ee",
    "sessionId": 1234567890,
    "startTime": null,
    "statementId": 43,
    "substatementId": null,
    "transactionId": "090011007F0D0000",
    "engineNativeAuditFields": {
        "UNIFIED_AUDIT_POLICIES": "TEST_POL_EVERYTHING",
        "FGA_POLICY_NAME": null,
        "DV_OBJECT_STATUS": null,
        "SYSTEM_PRIVILEGE_USED": "CREATE SEQUENCE, CREATE TABLE",
        "OLS_LABEL_COMPONENT_TYPE": null,
        "XS_SESSIONID": null,
        "ADDITIONAL_INFO": null,
        "INSTANCE_ID": 1,
        "DV_COMMENT": null,
        "RMAN_SESSION_STAMP": null,
        "NEW_NAME": null,
        "DV_ACTION_NAME": null,
        "OLS_PROGRAM_UNIT_NAME": null,
        "OLS_STRING_LABEL": null,
        "RMAN_SESSION_RECID": null,
        "OBJECT_PRIVILEGES": null,
        "OLS_OLD_VALUE": null,
        "XS_TARGET_PRINCIPAL_NAME": null,
        "XS_NS_ATTRIBUTE": null,
        "XS_NS_NAME": null,
        "DBLINK_INFO": null,
        "AUTHENTICATION_TYPE": "(TYPE\u003d(DATABASE));(CLIENT ADDRESS\u003d((PROTOCOL\u003dbeq)(HOST\u003d123.456.789.0)));",
        "OBJECT_EDITION": null,
        "OLS_PRIVILEGES_GRANTED": null,
        "EXCLUDED_USER": null,
        "DV_ACTION_OBJECT_NAME": null,
        "OLS_LABEL_COMPONENT_NAME": null,
        "EXCLUDED_SCHEMA": null,
        "DP_TEXT_PARAMETERS1": null,
        "XS_USER_NAME": null,
        "XS_ENABLED_ROLE": null,
        "XS_NS_ATTRIBUTE_NEW_VAL": null,
        "DIRECT_PATH_NUM_COLUMNS_LOADED": null,
        "AUDIT_OPTION": null,
        "DV_EXTENDED_ACTION_CODE": null,
        "XS_PACKAGE_NAME": null,
        "OLS_NEW_VALUE": null,
        "DV_RETURN_CODE": null,
        "XS_CALLBACK_EVENT_TYPE": null,
        "USERHOST": "ip-10-13-0-122",
        "GLOBAL_USERID": null,
        "CLIENT_IDENTIFIER": null,
        "RMAN_OPERATION": null,
        "TERMINAL": "pts/1",
        "OS_USERNAME": "rdsdb",
        "OLS_MAX_READ_LABEL": null,
        "XS_PROXY_USER_NAME": null,
        "XS_DATASEC_POLICY_NAME": null,
        "DV_FACTOR_CONTEXT": null,
        "OLS_MAX_WRITE_LABEL": null,
        "OLS_PARENT_GROUP_NAME": null,
        "EXCLUDED_OBJECT": null,
        "DV_RULE_SET_NAME": null,
        "EXTERNAL_USERID": null,
        "EXECUTION_ID": null,
        "ROLE": null,
        "PROXY_SESSIONID": 0,
        "DP_BOOLEAN_PARAMETERS1": null,
        "OLS_POLICY_NAME": null,
        "OLS_GRANTEE": null,
        "OLS_MIN_WRITE_LABEL": null,
        "APPLICATION_CONTEXTS": null,
        "XS_SCHEMA_NAME": null,
        "DV_GRANTEE": null,
        "XS_COOKIE": null,
        "DBPROXY_USERNAME": null,
        "DV_ACTION_CODE": null,
        "OLS_PRIVILEGES_USED": null,
        "RMAN_DEVICE_TYPE": null,
        "XS_NS_ATTRIBUTE_OLD_VAL": null,
        "TARGET_USER": null,
        "XS_ENTITY_TYPE": null,
        "ENTRY_ID": 12,
        "XS_PROCEDURE_NAME": null,
        "XS_INACTIVITY_TIMEOUT": null,
        "RMAN_OBJECT_TYPE": null,
        "SYSTEM_PRIVILEGE": null,
        "NEW_SCHEMA": null,
        "SCN": 5133083
    }
}
```

**Example Activity event record of a SELECT statement**  
Following is an example of a `SELECT` event\.  

```
{
    "class": "Standard",
    "clientApplication": "sqlplus@ip-12-34-5-678 (TNS V1-V3)",
    "command": "SELECT",
    "commandText": "select count(*) from persons",
    "databaseName": "1234567890",
    "dbProtocol": "oracle",
    "dbUserName": "TEST",
    "endTime": null,
    "errorMessage": null,
    "exitCode": 0,
    "logTime": "2021-01-15 00:25:18.850375",
    "netProtocol": "beq",
    "objectName": "PERSONS",
    "objectType": "TEST",
    "paramList": [],
    "pid": 17687,
    "remoteHost": "123.456.789.0",
    "remotePort": null,
    "rowCount": null,
    "serverHost": "987.654.321.09",
    "serverType": "oracle",
    "serverVersion": "19.0.0.0.ru-2020-01.rur-2020-01.r1.EE.3",
    "serviceName": "oracle-ee",
    "sessionId": 1080639707,
    "startTime": null,
    "statementId": 44,
    "substatementId": null,
    "transactionId": null,
    "engineNativeAuditFields": {
        "UNIFIED_AUDIT_POLICIES": "TEST_POL_EVERYTHING",
        "FGA_POLICY_NAME": null,
        "DV_OBJECT_STATUS": null,
        "SYSTEM_PRIVILEGE_USED": null,
        "OLS_LABEL_COMPONENT_TYPE": null,
        "XS_SESSIONID": null,
        "ADDITIONAL_INFO": null,
        "INSTANCE_ID": 1,
        "DV_COMMENT": null,
        "RMAN_SESSION_STAMP": null,
        "NEW_NAME": null,
        "DV_ACTION_NAME": null,
        "OLS_PROGRAM_UNIT_NAME": null,
        "OLS_STRING_LABEL": null,
        "RMAN_SESSION_RECID": null,
        "OBJECT_PRIVILEGES": null,
        "OLS_OLD_VALUE": null,
        "XS_TARGET_PRINCIPAL_NAME": null,
        "XS_NS_ATTRIBUTE": null,
        "XS_NS_NAME": null,
        "DBLINK_INFO": null,
        "AUTHENTICATION_TYPE": "(TYPE\u003d(DATABASE));(CLIENT ADDRESS\u003d((PROTOCOL\u003dbeq)(HOST\u003d123.456.789.0)));",
        "OBJECT_EDITION": null,
        "OLS_PRIVILEGES_GRANTED": null,
        "EXCLUDED_USER": null,
        "DV_ACTION_OBJECT_NAME": null,
        "OLS_LABEL_COMPONENT_NAME": null,
        "EXCLUDED_SCHEMA": null,
        "DP_TEXT_PARAMETERS1": null,
        "XS_USER_NAME": null,
        "XS_ENABLED_ROLE": null,
        "XS_NS_ATTRIBUTE_NEW_VAL": null,
        "DIRECT_PATH_NUM_COLUMNS_LOADED": null,
        "AUDIT_OPTION": null,
        "DV_EXTENDED_ACTION_CODE": null,
        "XS_PACKAGE_NAME": null,
        "OLS_NEW_VALUE": null,
        "DV_RETURN_CODE": null,
        "XS_CALLBACK_EVENT_TYPE": null,
        "USERHOST": "ip-12-34-5-678",
        "GLOBAL_USERID": null,
        "CLIENT_IDENTIFIER": null,
        "RMAN_OPERATION": null,
        "TERMINAL": "pts/1",
        "OS_USERNAME": "rdsdb",
        "OLS_MAX_READ_LABEL": null,
        "XS_PROXY_USER_NAME": null,
        "XS_DATASEC_POLICY_NAME": null,
        "DV_FACTOR_CONTEXT": null,
        "OLS_MAX_WRITE_LABEL": null,
        "OLS_PARENT_GROUP_NAME": null,
        "EXCLUDED_OBJECT": null,
        "DV_RULE_SET_NAME": null,
        "EXTERNAL_USERID": null,
        "EXECUTION_ID": null,
        "ROLE": null,
        "PROXY_SESSIONID": 0,
        "DP_BOOLEAN_PARAMETERS1": null,
        "OLS_POLICY_NAME": null,
        "OLS_GRANTEE": null,
        "OLS_MIN_WRITE_LABEL": null,
        "APPLICATION_CONTEXTS": null,
        "XS_SCHEMA_NAME": null,
        "DV_GRANTEE": null,
        "XS_COOKIE": null,
        "DBPROXY_USERNAME": null,
        "DV_ACTION_CODE": null,
        "OLS_PRIVILEGES_USED": null,
        "RMAN_DEVICE_TYPE": null,
        "XS_NS_ATTRIBUTE_OLD_VAL": null,
        "TARGET_USER": null,
        "XS_ENTITY_TYPE": null,
        "ENTRY_ID": 13,
        "XS_PROCEDURE_NAME": null,
        "XS_INACTIVITY_TIMEOUT": null,
        "RMAN_OBJECT_TYPE": null,
        "SYSTEM_PRIVILEGE": null,
        "NEW_SCHEMA": null,
        "SCN": 5136972
    }
}
```

### DatabaseActivityMonitoringRecords JSON object<a name="DBActivityStreams.AuditLog.DatabaseActivityMonitoringRecords"></a>

The database activity event records are in a JSON object that contains the following information\.


****  

| JSON Field | Data Type | Description | 
| --- | --- | --- | 
|  `type`  | string |  The type of JSON record\. The value is `DatabaseActivityMonitoringRecords`\.  | 
| version | string | The version of the database activity monitoring records\. Oracle DB uses version 1\.3\. This version introduces the engineNativeAuditFields JSON object\.  | 
|  [databaseActivityEvents](#DBActivityStreams.AuditLog.databaseActivityEvents)  | string |  A JSON object containing the activity events\.  | 
| key | string | An encryption key you use to decrypt the [databaseActivityEventList](#DBActivityStreams.AuditLog.databaseActivityEventList) databaseActivityEventList JSON array\. | 

### databaseActivityEvents JSON Object<a name="DBActivityStreams.AuditLog.databaseActivityEvents"></a>

The `databaseActivityEvents` JSON object contains the following information\.

#### Top\-level fields in JSON record<a name="DBActivityStreams.AuditLog.topLevel"></a>

 Each event in the audit log is wrapped inside a record in JSON format\. This record contains the following fields\. 

**type**  
 This field always has the value `DatabaseActivityMonitoringRecords`\. 

**version**  
 This field represents the version of the database activity stream data protocol or contract\. It defines which fields are available\.

**databaseActivityEvents**  
 An encrypted string representing one or more activity events\. It's represented as a base64 byte array\. When you decrypt the string, the result is a record in JSON format with fields as shown in the examples in this section\.

**key**  
 The encrypted data key used to encrypt the `databaseActivityEvents` string\. This is the same AWS KMS key that you provided when you started the database activity stream\.

 The following example shows the format of this record\.

```
{
  "type":"DatabaseActivityMonitoringRecords",
  "version":"1.3",
  "databaseActivityEvents":"encrypted audit records",
  "key":"encrypted key"
}
```

Take the following steps to decrypt the contents of the `databaseActivityEvents` field:

1.  Decrypt the value in the `key` JSON field using the KMS key you provided when starting database activity stream\. Doing so returns the data encryption key in clear text\. 

1.  Base64\-decode the value in the `databaseActivityEvents` JSON field to obtain the ciphertext, in binary format, of the audit payload\. 

1.  Decrypt the binary ciphertext with the data encryption key that you decoded in the first step\. 

1.  Decompress the decrypted payload\. 
   +  The encrypted payload is in the `databaseActivityEvents` field\. 
   +  The `databaseActivityEventList` field contains an array of audit records\. The `type` fields in the array can be `record` or `heartbeat`\. 

The audit log activity event record is a JSON object that contains the following information\.


****  

| JSON Field | Data Type | Description | 
| --- | --- | --- | 
|  `type`  | string |  The type of JSON record\. The value is `DatabaseActivityMonitoringRecord`\.  | 
| instanceId | string | The DB instance resource identifier\. It corresponds to the DB instance attribute DbiResourceId\. | 
|  [databaseActivityEventList](#DBActivityStreams.AuditLog.databaseActivityEventList)   | string |  An array of activity audit records or heartbeat messages\.  | 

### databaseActivityEventList JSON array<a name="DBActivityStreams.AuditLog.databaseActivityEventList"></a>

The audit log payload is an encrypted `databaseActivityEventList` JSON array\. The following table lists alphabetically the fields for each activity event in the decrypted `DatabaseActivityEventList` array of an audit log\. 

When unified auditing is enabled in Oracle Database, the audit records are populated in this new audit trail\. The `UNIFIED_AUDIT_TRAIL` view displays audit records in tabular form by retrieving the audit records from the audit trail\. When you start a database activity stream, a column in `UNIFIED_AUDIT_TRAIL` maps to a field in the `databaseActivityEventList` array\.

**Important**  
The event structure is subject to change\. Amazon RDS might add new fields to activity events in the future\. In applications that parse the JSON data, make sure that your code can ignore or take appropriate actions for unknown field names\. 


**databaseActivityEventList fields for Amazon RDS for Oracle**  

| Field | Data Type | Source | Description | 
| --- | --- | --- | --- | 
|  `class`  |  string  |  `AUDIT_TYPE` column in `UNIFIED_AUDIT_TRAIL`  |  The class of activity event\. This corresponds to the `AUDIT_TYPE` column in the `UNIFIED_AUDIT_TRAIL` view\. Valid values for Amazon RDS for Oracle are the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/DBActivityStreams.Monitoring.html) For more information, see [UNIFIED\_AUDIT\_TRAIL](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/UNIFIED_AUDIT_TRAIL.html#GUID-B7CE1C02-2FD4-47D6-80AA-CF74A60CDD1D) in the Oracle documentation\.  | 
|  `clientApplication`  |  string  |  `CLIENT_PROGRAM_NAME` in `UNIFIED_AUDIT_TRAIL`  |  The application the client used to connect as reported by the client\. The client doesn't have to provide this information, so the value can be null\. A sample value is `JDBC Thin Client`\.  | 
|  `command`  |  string  |  `ACTION_NAME` column in `UNIFIED_AUDIT_TRAIL`  |  Name of the action executed by the user\. To understand the complete action, read both the command name and the `AUDIT_TYPE` value\. A sample value is `ALTER DATABASE`\.  | 
|  `commandText`  |  string  |  `SQL_TEXT` column in `UNIFIED_AUDIT_TRAIL`  |  The SQL statement associated with the event\. A sample value is `ALTER DATABASE BEGIN BACKUP`\.  | 
|  `databaseName`  |  string  |  `NAME` column in `V$DATABASE`  |  The name of the database\.  | 
|  `dbid`  |  number  |  `DBID` column in `UNIFIED_AUDIT_TRAIL`  |  Numerical identifier for the database\. A sample value is `1559204751`\.  | 
|  `dbProtocol`  |  string  |  N/A  |  The database protocol\. In this beta, the value is `oracle`\.  | 
|  `dbUserName`  |  string  |  `DBUSERNAME` column in `UNIFIED_AUDIT_TRAIL`  |  Name of the database user whose actions were audited\. A sample value is `RDSADMIN`\.  | 
|  `endTime`  |  string  |  N/A  |  This field isn't used for RDS for Oracle and is always null\.  | 
|  `engineNativeAuditFields`  |  object  |  `UNIFIED_AUDIT_TRAIL`  |  By default, this object is empty\. When you start the activity stream with the `--engine-native-audit-fields-included` option, this object includes the following columns and their values: <pre>ADDITIONAL_INFO<br />APPLICATION_CONTEXTS<br />AUDIT_OPTION<br />AUTHENTICATION_TYPE<br />CLIENT_IDENTIFIER<br />CURRENT_USER<br />DBLINK_INFO<br />DBPROXY_USERNAME<br />DIRECT_PATH_NUM_COLUMNS_LOADED<br />DP_BOOLEAN_PARAMETERS1<br />DP_TEXT_PARAMETERS1<br />DV_ACTION_CODE<br />DV_ACTION_NAME<br />DV_ACTION_OBJECT_NAME<br />DV_COMMENT<br />DV_EXTENDED_ACTION_CODE<br />DV_FACTOR_CONTEXT<br />DV_GRANTEE<br />DV_OBJECT_STATUS<br />DV_RETURN_CODE<br />DV_RULE_SET_NAME<br />ENTRY_ID<br />EXCLUDED_OBJECT<br />EXCLUDED_SCHEMA<br />EXCLUDED_USER<br />EXECUTION_ID<br />EXTERNAL_USERID<br />FGA_POLICY_NAME<br />GLOBAL_USERID<br />INSTANCE_ID<br />KSACL_SERVICE_NAME<br />KSACL_SOURCE_LOCATION<br />KSACL_USER_NAME<br />NEW_NAME<br />NEW_SCHEMA<br />OBJECT_EDITION<br />OBJECT_PRIVILEGES<br />OLS_GRANTEE<br />OLS_LABEL_COMPONENT_NAME<br />OLS_LABEL_COMPONENT_TYPE<br />OLS_MAX_READ_LABEL<br />OLS_MAX_WRITE_LABEL<br />OLS_MIN_WRITE_LABEL<br />OLS_NEW_VALUE<br />OLS_OLD_VALUE<br />OLS_PARENT_GROUP_NAME<br />OLS_POLICY_NAME<br />OLS_PRIVILEGES_GRANTED<br />OLS_PRIVILEGES_USED<br />OLS_PROGRAM_UNIT_NAME<br />OLS_STRING_LABEL<br />OS_USERNAME<br />PROTOCOL_ACTION_NAME<br />PROTOCOL_MESSAGE<br />PROTOCOL_RETURN_CODE<br />PROTOCOL_SESSION_ID<br />PROTOCOL_USERHOST<br />PROXY_SESSIONID<br />RLS_INFO<br />RMAN_DEVICE_TYPE<br />RMAN_OBJECT_TYPE<br />RMAN_OPERATION<br />RMAN_SESSION_RECID<br />RMAN_SESSION_STAMP<br />ROLE<br />SCN<br />SYSTEM_PRIVILEGE<br />SYSTEM_PRIVILEGE_USED<br />TARGET_USER<br />TERMINAL<br />UNIFIED_AUDIT_POLICIES<br />USERHOST<br />XS_CALLBACK_EVENT_TYPE<br />XS_COOKIE<br />XS_DATASEC_POLICY_NAME<br />XS_ENABLED_ROLE<br />XS_ENTITY_TYPE<br />XS_INACTIVITY_TIMEOUT<br />XS_NS_ATTRIBUTE<br />XS_NS_ATTRIBUTE_NEW_VAL<br />XS_NS_ATTRIBUTE_OLD_VAL<br />XS_NS_NAME<br />XS_PACKAGE_NAME<br />XS_PROCEDURE_NAME<br />XS_PROXY_USER_NAME<br />XS_SCHEMA_NAME<br />XS_SESSIONID<br />XS_TARGET_PRINCIPAL_NAME<br />XS_USER_NAME</pre> For more information, see [UNIFIED\_AUDIT\_TRAIL](https://docs.oracle.com/database/121/REFRN/GUID-B7CE1C02-2FD4-47D6-80AA-CF74A60CDD1D.htm#REFRN29162) in the Oracle Database documentation\.  | 
|  `errorMessage`  |  string  |  N/A  |  This field isn't used for RDS for Oracle and is always null\.  | 
|  `exitCode`  |  number  |  `RETURN_CODE` column in `UNIFIED_AUDIT_TRAIL`  |  Oracle Database error code generated by the action\. If the action succeeded, the value is `0`\.  | 
|  `logTime`  |  string  |  `EVENT_TIMESTAMP_UTC` column in `UNIFIED_AUDIT_TRAIL`  |  Timestamp of the creation of the audit trail entry\. A sample value is `2020-11-27 06:56:14.981404`\.  | 
|  `netProtocol`  |  string  |  `AUTHENTICATION_TYPE` column in `UNIFIED_AUDIT_TRAIL`  |  The network communication protocol\. A sample value is `TCP`\.  | 
|  `objectName`  |  string  |  `OBJECT_NAME` column in `UNIFIED_AUDIT_TRAIL`  |  The name of the object affected by the action\. A sample value is `employees`\.  | 
|  `objectType`  |  string  |  `OBJECT_SCHEMA` column in `UNIFIED_AUDIT_TRAIL`  |  The schema name of object affected by the action\. A sample value is `hr`\.  | 
|  `paramList`  |  list  |  `SQL_BINDS` column in `UNIFIED_AUDIT_TRAIL`  |  The list of bind variables, if any, associated with `SQL_TEXT`\. A sample value is `parameter_1,parameter_2`\.  | 
|  `pid`  |  number  |  `OS_PROCESS` column in `UNIFIED_AUDIT_TRAIL`  |  Operating system process identifier of the Oracle database process\. A sample value is `22396`\.  | 
|  `remoteHost`  |  string  |  `AUTHENTICATION_TYPE` column in `UNIFIED_AUDIT_TRAIL`  |  Either the client IP address or name of the host from which the session was spawned\. A sample value is `123.456.789.123`\.  | 
|  `remotePort`  |  string  |  `AUTHENTICATION_TYPE` column in `UNIFIED_AUDIT_TRAIL`  |  The client port number\. A typical value in Oracle Database environments is `1521`\.  | 
|  `rowCount`  |  number  |  N/A  |  This field isn't used for RDS for Oracle and is always null\.  | 
|  `serverHost`  |  string  |  Database host  |  The IP address of the database server host\. A sample value is `123.456.789.123`\.  | 
|  `serverType`  |  string  |  N/A  |  The database server type\. The value is always `ORACLE`\.  | 
|  `serverVersion`  |  string  |  Database host  |  The Amazon RDS for Oracle version, Release Update \(RU\), and Release Update Revision \(RUR\)\. A sample value is `19.0.0.0.ru-2020-01.rur-2020-01.r1.EE.3`\.  | 
|  `serviceName`  |  string  |  Database host  |  The name of the service\. A sample value is `oracle-ee`\.   | 
|  `sessionId`  |  number  |  `SESSIONID` column in `UNIFIED_AUDIT_TRAIL`  |  The session identifier of the audit\. An example is `1894327130`\.  | 
|  `startTime`  |  string  |  N/A  |  This field isn't used for RDS for Oracle and is always null\.  | 
|  `statementId`  |  number  |  `STATEMENT_ID` column in `UNIFIED_AUDIT_TRAIL`  |  Numeric ID for each statement run\. A statement can cause many actions\. A sample value is `142197`\.  | 
|  `substatementId`  |  N/A  |  N/A  |  This field isn't used for RDS for Oracle and is always null\.  | 
|  `transactionId`  |  string  |  `TRANSACTION_ID` column in `UNIFIED_AUDIT_TRAIL`  |  The identifier of the transaction in which the object is modified\. A sample value is `02000800D5030000`\.  | 

## Processing a database activity stream using the AWS SDK<a name="DBActivityStreams.CodeExample"></a>

You can programmatically process an activity stream by using the AWS SDK\. 