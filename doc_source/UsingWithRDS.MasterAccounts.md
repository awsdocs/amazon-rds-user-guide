# Master User Account Privileges<a name="UsingWithRDS.MasterAccounts"></a>

When you create a new DB instance, the default master user that you use gets certain privileges for that DB instance\. The following table shows the privileges and database roles the master user gets for each of the database engines\.

**Important**  
We strongly recommend that you do not use the master user directly in your applications\. Instead, adhere to the best practice of using a database user created with the minimal privileges required for your application\.

**Note**  
If you accidentally delete the permissions for the master user, you can restore them by modifying the DB instance and setting a new master user password\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.


| Database Engine | System Privilege | Database Role | 
| --- | --- | --- | 
| MySQL and MariaDB | SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON \*\.\* WITH GRANT OPTION, REPLICATION SLAVE \(only for Amazon RDS MySQL versions 5\.6, 5\.7 and 8\.0, Amazon RDS MariaDB\)  | — | 
| PostgreSQL | CREATE ROLE, CREATE DB, PASSWORD VALID UNTIL INFINITY, CREATE EXTENSION, ALTER EXTENSION, DROP EXTENSION, CREATE TABLESPACE, ALTER < OBJECT> OWNER, CHECKPOINT, PG\_CANCEL\_BACKEND\(\), PG\_TERMINATE\_BACKEND\(\), SELECT PG\_STAT\_REPLICATION, EXECUTE PG\_STAT\_STATEMENTS\_RESET\(\), OWN POSTGRES\_FDW\_HANDLER\(\), OWN POSTGRES\_FDW\_VALIDATOR\(\), OWN POSTGRES\_FDW, EXECUTE PG\_BUFFERCACHE\_PAGES\(\), SELECT PG\_BUFFERCACHE  | RDS\_SUPERUSER | 
| Oracle | ALTER DATABASE LINK, ALTER PUBLIC DATABASE LINK, DROP ANY DIRECTORY, EXEMPT ACCESS POLICY, EXEMPT IDENTITY POLICY, GRANT ANY OBJECT PRIVILEGE, RESTRICTED SESSION, EXEMPT REDACTION POLICY | AQ\_ADMINISTRATOR\_ROLE, AQ\_USER\_ROLE, CONNECT, CTXAPP, DBA, EXECUTE\_CATALOG\_ROLE, RECOVERY\_CATALOG\_OWNER, RESOURCE, SELECT\_CATALOG\_ROLE  | 
| Microsoft SQL Server | ADMINISTER BULK OPERATIONS, ALTER ANY CONNECTION, ALTER ANY LINKED SERVER, ALTER ANY LOGIN, ALTER SERVER STATE, ALTER TRACE, CONNECT SQL, CREATE ANY DATABASE, VIEW ANY DATABASE, VIEW ANY DEFINITION, VIEW SERVER STATE, ALTER ANY SERVER ROLE, ALTER ANY USER, ALTER ON ROLE SQLAgentOperatorRole | DB\_OWNER \(Database Level Role\) PROCESSADMIN \(Server Level Role\) SETUPADMIN\(Server Level Role\) SQLAgentUserRole\(Server Level Role\)  | 