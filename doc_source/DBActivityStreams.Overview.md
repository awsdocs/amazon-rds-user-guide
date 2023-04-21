# Overview of Database Activity Streams<a name="DBActivityStreams.Overview"></a>

As an Amazon RDS database administrator, you need to safeguard your database and meet compliance and regulatory requirements\. One strategy is to integrate database activity streams with your monitoring tools\. In this way, you monitor and set alarms for auditing activity in your database\.

Security threats are both external and internal\. To protect against internal threats, you can control administrator access to data streams by configuring the Database Activity Streams feature\. Amazon RDS  DBAs don't have access to the collection, transmission, storage, and processing of the streams\.

**Topics**
+ [How database activity streams work](#DBActivityStreams.Overview.how-they-work)
+ [Auditing in Oracle Database and Microsoft SQL Server Database](#DBActivityStreams.Overview.auditing)
+ [Asynchronous mode for database activity streams](#DBActivityStreams.Overview.sync-mode)
+ [Requirements and limitations for database activity streams](#DBActivityStreams.Overview.requirements)
+ [Region and version availability](#DBActivityStreams.RegionVersionAvailability)
+ [Supported DB instance classes for database activity streams](#DBActivityStreams.Overview.requirements.classes)

## How database activity streams work<a name="DBActivityStreams.Overview.how-they-work"></a>

Amazon RDS pushes activities to an Amazon Kinesis data stream in near real time\. The Kinesis stream is created automatically\. From Kinesis, you can configure AWS services such as Amazon Kinesis Data Firehose and AWS Lambda to consume the stream and store the data\.

**Important**  
Use of the database activity streams feature in Amazon RDS is free, but Amazon Kinesis charges for a data stream\. For more information, see [Amazon Kinesis Data Streams pricing](https://aws.amazon.com/kinesis/data-streams/pricing/)\.

You can configure applications for compliance management to consume database activity streams\. These applications can use the stream to generate alerts and audit activity on your database\.



Amazon RDS supports database activity streams in Multi\-AZ deployments\. In this case, database activity streams audit both the primary and standby instances\.

## Auditing in Oracle Database and Microsoft SQL Server Database<a name="DBActivityStreams.Overview.auditing"></a>

Auditing is the monitoring and recording of configured database actions\. Amazon RDS doesn't capture database activity by default\. You create and manage audit policies in your database yourself\.

**Topics**
+ [Unified auditing in Oracle Database](#DBActivityStreams.Overview.unified-auditing)
+ [Auditing in Microsoft SQL Server](#DBActivityStreams.Overview.SQLServer-auditing)
+ [Non\-native audit fields for Oracle Database and SQL Server](#DBActivityStreams.Overview.unified-auditing.non-native)
+ [DB parameter group override](#DBActivityStreams.Overview.unified-auditing.parameter-group)

### Unified auditing in Oracle Database<a name="DBActivityStreams.Overview.unified-auditing"></a>

In an Oracle database, a *unified audit policy* is a named group of audit settings that you can use to audit an aspect of user behavior\. A policy can be as simple as auditing the activities of a single user\. You can also create complex audit policies that use conditions\.

An Oracle database writes audit records, including `SYS` audit records, to the *unified audit trail*\. For example, if an error occurs during an `INSERT` statement, standard auditing indicates the error number and the SQL that was run\. The audit trail resides in a read\-only table in the `AUDSYS` schema\. To access these records, query the `UNIFIED_AUDIT_TRAIL` data dictionary view\.

Typically, you configure database activity streams as follows:

1. Create an Oracle Database audit policy by using the `CREATE AUDIT POLICY` command\.

   The Oracle Database generates audit records\.

1. Activate the audit policy by using the `AUDIT POLICY` command\.

1. Configure database activity streams\.

   Only activities that match the Oracle Database audit policies are captured and sent to the Amazon Kinesis data stream\. When database activity streams are enabled, an Oracle database administrator can't alter the audit policy or remove audit logs\.

To learn more about unified audit policies, see [About Auditing Activities with Unified Audit Policies and AUDIT](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/configuring-audit-policies.html#GUID-2435D929-10AD-43C7-8A6C-5133170074D0) in the *Oracle Database Security Guide*\.

### Auditing in Microsoft SQL Server<a name="DBActivityStreams.Overview.SQLServer-auditing"></a>

Database Activity Stream uses SQLAudit feature to audit the SQL Server database\.

RDS for SQL Server instance contains the following:
+ Server audit – The SQL server audit collects a single instance of server or database\-level actions, and a group of actions to monitor\. The server\-level audits `RDS_DAS_AUDIT` and `RDS_DAS_AUDIT_CHANGES` are managed by RDS\.
+ Server audit specification – The server audit specification records the server\-level events\. You can modify the `RDS_DAS_SERVER_AUDIT_SPEC` specification\. This specification is linked to the server audit `RDS_DAS_AUDIT`\. The `RDS_DAS_CHANGES_AUDIT_SPEC` specification is managed by RDS\.
+ Database audit specification – The database audit specification records the database\-level events\. You can create a database audit specification `RDS_DAS_DB_<name>` and link it to `RDS_DAS_AUDIT` server audit\.

You can configure database activity streams by using the console or CLI\. Typically, you configure database activity streams as follows:

1. \(Optional\) Create a database audit specification with the `CREATE DATABASE AUDIT SPECIFICATION` command and link it to `RDS_DAS_AUDIT` server audit\. 

1. \(Optional\) Modify the server audit specification with the `ALTER SERVER AUDIT SPECIFICATION` command and define the policies\. 

1. Activate the database and server audit policies\. For example:

   `ALTER DATABASE AUDIT SPECIFICATION [<Your database specification>] WITH (STATE=ON)`

   `ALTER SERVER AUDIT SPECIFICATION [RDS_DAS_SERVER_AUDIT_SPEC] WITH (STATE=ON)`

1. Configure database activity streams\.

   Only activities that match the server and database audit policies are captured and sent to the Amazon Kinesis data stream\. When database activity streams are enabled and the policies are locked, a database administrator can't alter the audit policy or remove audit logs\. 
**Important**  
If the database audit specification for a specific database is enabled and the policy is in a locked state, then the database can't be dropped\.

For more information about SQL Server auditing, see [SQL Server Audit Components](https://learn.microsoft.com/en-us/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-ver16) in the *Microsoft SQL Server documentation*\.



### Non\-native audit fields for Oracle Database and SQL Server<a name="DBActivityStreams.Overview.unified-auditing.non-native"></a>

When you start a database activity stream, every database event generates a corresponding activity stream event\. For example, a database user might run `SELECT` and `INSERT` statements\. The database audits these events and sends them to an Amazon Kinesis data stream\.

The events are represented in the stream as JSON objects\. A JSON object contains a `DatabaseActivityMonitoringRecord`, which contains a `databaseActivityEventList` array\. Predefined fields in the array include `class`, `clientApplication`, and `command`\.

By default, an activity stream doesn't include engine\-native audit fields\. You can configure Amazon RDS for Oracle and SQL Server so that it includes these extra fields in the `engineNativeAuditFields` JSON object\.

In Oracle Database, most events in the unified audit trail map to fields in the RDS data activity stream\. For example, the `UNIFIED_AUDIT_TRAIL.SQL_TEXT` field in unified auditing maps to the `commandText` field in a database activity stream\. However, Oracle Database audit fields such as `OS_USERNAME` don't map to predefined fields in a database activity stream\.

In SQL Server, most of the event's fields that are recorded by the SQLAudit map to the fields in RDS database activity stream\. For example, the `code` field from `sys.fn_get_audit_file` in the audit maps to the `commandText` field in a database activity stream\. However, SQL Server database audit fields, such as `permission_bitmask`, don’t map to predefined fields in a database activity stream\.

For more information about databaseActivityEventList, see [databaseActivityEventList JSON array](DBActivityStreams.Monitoring.md#DBActivityStreams.AuditLog.databaseActivityEventList)\.

### DB parameter group override<a name="DBActivityStreams.Overview.unified-auditing.parameter-group"></a>

Typically, you turn on unified auditing in RDS for Oracle by attaching a parameter group\. However, Database Activity Streams require additional configuration\. To improve your customer experience, Amazon RDS performs the following:
+ If you activate an activity stream, RDS for Oracle ignores the auditing parameters in the parameter group\.
+ If you deactivate an activity stream, RDS for Oracle stops ignoring the auditing parameters\.

The database activity stream for SQL Server is independent of any parameters you set in the SQL Audit option\.

## Asynchronous mode for database activity streams<a name="DBActivityStreams.Overview.sync-mode"></a>

Activity streams in Amazon RDS are always asynchronous\. When a database session generates an activity stream event, the session returns to normal activities immediately\. In the background, Amazon RDS makes the activity stream event into a durable record\.

If an error occurs in the background task, Amazon RDS generates an event\. This event indicates the beginning and end of any time windows where activity stream event records might have been lost\. Asynchronous mode favors database performance over the accuracy of the activity stream\.

## Requirements and limitations for database activity streams<a name="DBActivityStreams.Overview.requirements"></a>

In RDS, database activity streams have the following requirements and limitations:
+ Amazon Kinesis is required for database activity streams\.
+ AWS Key Management Service \(AWS KMS\) is required for database activity streams because they are always encrypted\.
+ Applying additional encryption to your Amazon Kinesis data stream is incompatible with database activity streams, which are already encrypted with your AWS KMS key\.
+ You create and manage audit policies yourself\. Unlike Amazon Aurora, RDS for Oracle doesn't capture database activities by default\.
+ You create and manage audit policies or specifications yourself\. Unlike Amazon Aurora, Amazon RDS doesn't capture database activities by default\.
+ In a Multi\-AZ deployment, start the database activity stream only on the primary DB instance\. The activity stream audits both the primary and standby DB instances automatically\. No additional steps are required during a failover\.
+ Renaming a DB instance doesn't create a new Kinesis stream\.
+ CDBs aren't supported for RDS for Oracle\.
+ Read replicas aren't supported\.

## Region and version availability<a name="DBActivityStreams.RegionVersionAvailability"></a>

Feature availability and support varies across specific versions of each database engine, and across AWS Regions\. For more information on version and Region availability with database activity streams, see [Database activity streams](Concepts.RDS_Fea_Regions_DB-eng.Feature.DBActivityStreams.md)\.

## Supported DB instance classes for database activity streams<a name="DBActivityStreams.Overview.requirements.classes"></a>

For RDS for Oracle you can use database activity streams with the following DB instance classes:
+ db\.m4\.\*large
+ db\.m5\.\*large
+ db\.m5d\.\*large
+ db\.m6i\.\*large
+ db\.r4\.\*large
+ db\.r5\.\*large
+ db\.r5\.\*large\.tpc\*\.mem\*x
+ db\.r5b\.\*large
+ db\.r5b\.\*large\.tpc\*\.mem\*x
+ db\.r5d\.\*large
+ db\.r6i\.\*large
+ db\.x2idn\.\*large
+ db\.x2iedn\.\*large
+ db\.x2iezn\.\*large
+ db\.z1d\.\*large

For RDS for SQL Server you can use database activity streams with the following DB instance classes:
+ db\.m4\.\*large
+ db\.m5\.\*large
+ db\.m5d\.\*large
+ db\.m6i\.\*large
+ db\.r4\.\*large
+ db\.r5\.\*large
+ db\.r5b\.\*large
+ db\.r5d\.\*large
+ db\.r6i\.\*large
+ db\.x1e\.\*large
+ db\.z1d\.\*large

For more information about instance class types, see [DB instance classes](Concepts.DBInstanceClass.md)\.