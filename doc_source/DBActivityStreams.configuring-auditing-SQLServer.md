# Configuring auditing policy for Microsoft SQL Server<a name="DBActivityStreams.configuring-auditing-SQLServer"></a>

A SQL Server database instance has the server audit `RDS_DAS_AUDIT`, which is managed by Amazon RDS\. You can define the policies to record server events in the server audit specification `RDS_DAS_SERVER_AUDIT_SPEC`\. You can create a database audit specification, such as `RDS_DAS_DB_<name>`, and define the policies to record database events\. For the list of server and database level audit action groups, see [SQL Server Audit Action Groups and Actions](https://learn.microsoft.com/en-us/sql/relational-databases/security/auditing/sql-server-audit-action-groups-and-actions) in the *Microsoft SQL Server documentation*\.

The default server policy monitors only failed logins and changes to any database or server audit specifications for database activity streams\.

Limitations for the audit and audit specifications include the following:
+ You can't modify the server or database audit specifications when the database activity stream is in a *locked* state\.
+ You can't modify the server audit `RDS_DAS_AUDIT` specification\.
+ You can't modify the SQL Server audit `RDS_DAS_CHANGES` or its related server audit specification `RDS_DAS_CHANGES_AUDIT_SPEC`\.
+ When creating a database audit specification, you must use the format `RDS_DAS_DB_<name>` for example, `RDS_DAS_DB_databaseActions`\.

**Important**  
For smaller instance classes, we recommend that you don't audit all but only the data that is required\. This helps to reduce the performance impact of Database Activity Streams on these instance classes\.

The following sample code modifies the server audit specification `RDS_DAS_SERVER_AUDIT_SPEC` and audits any logout and successful login actions:

```
ALTER SERVER AUDIT SPECIFICATION [RDS_DAS_SERVER_AUDIT_SPEC]
      WITH (STATE=OFF);
ALTER SERVER AUDIT SPECIFICATION [RDS_DAS_SERVER_AUDIT_SPEC]
      ADD (LOGOUT_GROUP),
      ADD (SUCCESSFUL_LOGIN_GROUP)
      WITH (STATE = ON );
```

The following sample code creates a database audit specification `RDS_DAS_DB_database_spec` and attaches it to the server audit `RDS_DAS_AUDIT`:

```
USE testDB;
CREATE DATABASE AUDIT SPECIFICATION [RDS_DAS_DB_database_spec]
     FOR SERVER AUDIT [RDS_DAS_AUDIT]
     ADD ( INSERT, UPDATE, DELETE  
          ON testTable BY testUser )  
     WITH (STATE = ON);
```

After the audit specifications are configured, make sure that the specifications `RDS_DAS_SERVER_AUDIT_SPEC` and `RDS_DAS_DB_<name>` are set to a state of `ON`\. Now they can send the audit data to your database activity stream\.