# RDS for Oracle limitations<a name="Oracle.Concepts.limitations"></a>

Following are important limitations of using RDS for Oracle\.

**Note**  
This list is not exhaustive\.

**Topics**
+ [Oracle file size limits in Amazon RDS](#Oracle.Concepts.file-size-limits)
+ [Public synonyms for Oracle\-supplied schemas](#Oracle.Concepts.PublicSynonyms)
+ [Schemas for unsupported features](#Oracle.Concepts.unsupported-features)
+ [Limitations for Oracle DBA privileges](#Oracle.Concepts.dba-limitations)
+ [Limitations of a single\-tenant CDB](#Oracle.Concepts.single-tenant-limitations)
+ [Deprecation of TLS 1\.0 and 1\.1 Transport Layer Security](#Oracle.Concepts.tls)

## Oracle file size limits in Amazon RDS<a name="Oracle.Concepts.file-size-limits"></a>

The maximum size of a single file on RDS for Oracle DB instances is 16 TiB \(tebibytes\)\. If you try to resize a data file in a bigfile tablespace to a value over the limit, you receive an error such as the following\.

```
ORA-01237: cannot extend datafile 6
ORA-01110: data file 6: '/rdsdbdata/db/mydir/datafile/myfile.dbf'
ORA-27059: could not reduce file size
Linux-x86_64 Error: 27: File too large
Additional information: 2
```

## Public synonyms for Oracle\-supplied schemas<a name="Oracle.Concepts.PublicSynonyms"></a>

Don't create or modify public synonyms for Oracle\-supplied schemas, including `SYS`, `SYSTEM`, and `RDSADMIN`\. Such actions might result in invalidation of core database components and affect the availability of your DB instance\.

You can create public synonyms referencing objects in your own schemas\.

## Schemas for unsupported features<a name="Oracle.Concepts.unsupported-features"></a>

In general, Amazon RDS doesn't prevent you from creating schemas for unsupported features\. However, if you create schemas for Oracle features and components that require SYS privileges, you can damage the data dictionary and affect your instance availability\. Use only supported features and schemas that are available in [Adding options to Oracle DB instances](Appendix.Oracle.Options.md)\.

## Limitations for Oracle DBA privileges<a name="Oracle.Concepts.dba-limitations"></a>

In the database, a role is a collection of privileges that you can grant to or revoke from a user\. An Oracle database uses roles to provide security\.

The predefined role `DBA` normally allows all administrative privileges on an Oracle database\. When you create a DB instance, your master user account gets DBA privileges \(with some limitations\)\. To deliver a managed experience, an RDS for Oracle database doesn't provide the following privileges for the `DBA` role: 
+ `ALTER DATABASE`
+ `ALTER SYSTEM`
+ `CREATE ANY DIRECTORY`
+ `DROP ANY DIRECTORY`
+ `GRANT ANY PRIVILEGE`
+ `GRANT ANY ROLE`

Use the master user account for administrative tasks such as creating additional user accounts in the database\. You can't use `SYS`, `SYSTEM`, and other Oracle\-supplied administrative accounts\. 

## Limitations of a single\-tenant CDB<a name="Oracle.Concepts.single-tenant-limitations"></a>

The following options aren't supported for the single\-tenant architecture:
+ Database Activity Streams
+ Oracle Data Guard
+ Oracle Enterprise Manager
+ Oracle Enterprise Manager Agent
+ Oracle Label Security

The following operations work in a single\-tenant CDB, but no customer\-visible mechanism can detect the current status of the operations:
+ [Enabling and disabling block change tracking](Appendix.Oracle.CommonDBATasks.RMAN.md#Appendix.Oracle.CommonDBATasks.BlockChangeTracking)
+ [Enabling auditing for the SYS\.AUD$ table](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.EnablingAuditing)

**Note**  
Auditing information isn't available from within the PDB\.

## Deprecation of TLS 1\.0 and 1\.1 Transport Layer Security<a name="Oracle.Concepts.tls"></a>

Transport Layer Security protocol versions 1\.0 and 1\.1 \(TLS 1\.0 and TLS 1\.1\) are deprecated\. In accordance with security best practices, Oracle has deprecated the use of TLS 1\.0 and TLS 1\.1\. To meet your security requirements, RDS for Oracle strongly recommends that you use TLS 1\.2 instead\.