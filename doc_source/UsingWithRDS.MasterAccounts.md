# Master user account privileges<a name="UsingWithRDS.MasterAccounts"></a>

When you create a new DB instance, the default master user that you use gets certain privileges for that DB instance\. You can't change the master user name after the DB instance is created\.

**Important**  
We strongly recommend that you do not use the master user directly in your applications\. Instead, adhere to the best practice of using a database user created with the minimal privileges required for your application\.

**Note**  
If you accidentally delete the permissions for the master user, you can restore them by modifying the DB instance and setting a new master user password\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

The following table shows the privileges and database roles the master user gets for each of the database engines\.


| Database engine | System privilege | Database role | 
| --- | --- | --- | 
| MySQL and MariaDB | `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `CREATE`, `DROP`, `RELOAD`, `PROCESS`, `REFERENCES`, `INDEX`, `ALTER`, `SHOW DATABASES`, `CREATE TEMPORARY TABLES`, `LOCK TABLES`, `EXECUTE`, `REPLICATION CLIENT`, `CREATE VIEW`, `SHOW VIEW`, `CREATE ROUTINE`, `ALTER ROUTINE`, `CREATE USER`, `EVENT`, `TRIGGER ON *.* WITH GRANT OPTION`, `REPLICATION SLAVE` | — | 
| PostgreSQL | `CREATE ROLE`, `CREATE DB`, `PASSWORD VALID UNTIL INFINITY`, `CREATE EXTENSION`, `ALTER EXTENSION`, `DROP EXTENSION`, `CREATE TABLESPACE`, `ALTER <OBJECT> OWNER`, `CHECKPOINT`, `PG_CANCEL_BACKEND()`, `PG_TERMINATE_BACKEND()`, `SELECT PG_STAT_REPLICATION`, `EXECUTE PG_STAT_STATEMENTS_RESET()`, `OWN POSTGRES_FDW_HANDLER()`, `OWN POSTGRES_FDW_VALIDATOR()`, `OWN POSTGRES_FDW`, `EXECUTE PG_BUFFERCACHE_PAGES()`, `SELECT PG_BUFFERCACHE` | `RDS_SUPERUSER` For more information about RDS\_SUPERUSER, see [Understanding PostgreSQL roles and permissions](Appendix.PostgreSQL.CommonDBATasks.Roles.md)\.  | 
| Oracle | `ALTER DATABASE LINK`, `ALTER PUBLIC DATABASE LINK`, `DROP ANY DIRECTORY`, `EXEMPT ACCESS POLICY`, `EXEMPT IDENTITY POLICY`, `GRANT ANY OBJECT PRIVILEGE`, `RESTRICTED SESSION`, `EXEMPT REDACTION POLICY` | `AQ_ADMINISTRATOR_ROLE`, `AQ_USER_ROLE`, `CONNECT`, `CTXAPP`, `DBA`, `EXECUTE_CATALOG_ROLE`, `RECOVERY_CATALOG_OWNER`, `RESOURCE`, `SELECT_CATALOG_ROLE` | 
| Microsoft SQL Server | `ADMINISTER BULK OPERATIONS`, `ALTER ANY CONNECTION`, `ALTER ANY CREDENTIAL`, `ALTER ANY EVENT SESSION`, `ALTER ANY LINKED SERVER`, `ALTER ANY LOGIN`, `ALTER ANY SERVER AUDIT`, `ALTER ANY SERVER ROLE`, `ALTER SERVER STATE`, `ALTER TRACE`, `CONNECT SQL`, `CREATE ANY DATABASE`, `VIEW ANY DATABASE`, `VIEW ANY DEFINITION`, `VIEW SERVER STATE`, `ALTER ON ROLE SQLAgentOperatorRole` | `DB_OWNER` \(database\-level role\), `PROCESSADMIN` \(server\-level role\), `SETUPADMIN` \(server\-level role\), `SQLAgentUserRole` \(database\-level role\) | 