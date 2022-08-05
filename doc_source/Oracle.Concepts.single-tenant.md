# RDS for Oracle architecture<a name="Oracle.Concepts.single-tenant"></a>

The multitenant architecture enables an Oracle database to function as a multitenant container database \(CDB\)\. A CDB can include customer\-created pluggable databases \(PDBs\)\. A non\-CDB is an Oracle database that uses the traditional architecture, which can't contain PDBs\. For more information about the multitenant architecture, see [https://docs.oracle.com/en/database/oracle/oracle-database/19/multi/introduction-to-the-multitenant-architecture.html#GUID-267F7D12-D33F-4AC9-AA45-E9CD671B6F22](https://docs.oracle.com/en/database/oracle/oracle-database/19/multi/introduction-to-the-multitenant-architecture.html#GUID-267F7D12-D33F-4AC9-AA45-E9CD671B6F22)\.

The architecture is a permanent characteristic that you can't change later\. The architecture requirements are as follows:

Oracle Database 21c  
You must create the instance as a CDB\.

Oracle Database 19c  
You can create the instance as either a CDB or non\-CDB\.

Oracle Database 12c  
You must create the instance as a non\-CDB\.

For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

Currently, Amazon RDS supports a subset of multitenant architecture called the single\-tenant architecture\. In this case, your CDB contains only one PDB\. The single\-tenant architecture uses the same RDS APIs as the non\-CDB architecture\. Your experience with a non\-CDB is mostly identical to your experience with a PDB\. You can't access the CDB itself\.

The following sections explain the principal differences between the non\-multitenant and single\-tenant architectures\. For more information, see [Limitations of a single\-tenant CDB](Oracle.Concepts.limitations.md#Oracle.Concepts.single-tenant-limitations)\.

**Topics**
+ [Database creation and connections in a single\-tenant architecture](#Oracle.Concepts.single-tenant.creation)
+ [Database upgrades in a single\-tenant architecture](#Oracle.Concepts.single-tenant.upgrades)
+ [User accounts and privileges in a single\-tenant architecture](#Oracle.Concepts.single-tenant.users)
+ [Parameters in a single\-tenant architecture](#Oracle.Concepts.single-tenant.parameters)
+ [Snapshots in a single\-tenant architecture](#Oracle.Concepts.single-tenant.snapshots)
+ [Data migration in a single\-tenant architecture](#Oracle.Concepts.single-tenant.migration)

## Database creation and connections in a single\-tenant architecture<a name="Oracle.Concepts.single-tenant.creation"></a>

When you create a CDB, specify the DB instance identifier just as for a non\-CDB\. The instance identifier forms the first part of your endpoint\. The system identifier \(SID\) is the name of the CDB\. The SID of every CDB is `RDSCDB`\. You can't choose a different value\.

In the single\-tenant architecture, you always connect to the PDB rather than the CDB\. Specify the endpoint for the PDB just as for a non\-CDB\. The only difference is that you specify *pdb\_name* for the database name, where *pdb\_name* is the name you chose for your PDB\. The following example shows the format for the connection string in SQL\*Plus\.

```
sqlplus 'dbuser@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=endpoint)(PORT=port))(CONNECT_DATA=(SID=pdb_name)))'
```

## Database upgrades in a single\-tenant architecture<a name="Oracle.Concepts.single-tenant.upgrades"></a>

You can upgrade a CDB to a different Oracle Database release\. For example, you can upgrade a DB instance from Oracle Database 19c to Oracle Database 21c\. You can't upgrade a non\-CDB to a CDB\.

## User accounts and privileges in a single\-tenant architecture<a name="Oracle.Concepts.single-tenant.users"></a>

In the Oracle multitenant architecture, all users accounts are either common users or local users\. A CDB common user is a database user whose single identity and password are known in the CDB root and in every existing and future PDB\. In contrast, a local user exists only in a single PDB\.

The RDS master user is a local user account in the PDB\. If you create new user accounts, these users will also be local users residing in the PDB\. You can't use any user accounts to create new PDBs or modify the state of the existing PDB\.

The `rdsadmin` user is a common user account\. You can run Oracle for RDS packages that exist in this account, but you can't log in as `rdsadmin`\. For more information, see [About Common Users and Local Users](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/managing-security-for-oracle-database-users.html#GUID-BBBD9904-F2F3-442B-9AFC-8ACDD9A588D8) in the Oracle documentation\.

## Parameters in a single\-tenant architecture<a name="Oracle.Concepts.single-tenant.parameters"></a>

CDBs have their own parameter classes and different default parameter values\. The CDB parameter classes are as follows:
+ oracle\-ee\-cdb\-21
+ oracle\-se2\-cdb\-21
+ oracle\-ee\-cdb\-19
+ oracle\-se2\-cdb\-19

You specify parameters at the CDB level rather than the PDB level\. The PDB inherits parameter settings from the CDB\. For more information about setting parameters, see [Working with DB parameter groups](CHAP_BestPractices.md#CHAP_BestPractices.DBParameterGroup)\.

## Snapshots in a single\-tenant architecture<a name="Oracle.Concepts.single-tenant.snapshots"></a>

Snapshots work the same in a single\-tenant and non\-multitenant architecture\. The only difference is that when you restore a snapshot, you can only rename the PDB, not the CDB\. The CDB is always named `RDSCDB`\. For more information, see [Oracle Database considerations](USER_RestoreFromSnapshot.md#USER_RestoreFromSnapshot.Oracle)\.

## Data migration in a single\-tenant architecture<a name="Oracle.Concepts.single-tenant.migration"></a>

RDS for Oracle doesn't support unplugging and plugging in PDBs\. For more information about migrating data, see [Importing data into Oracle on Amazon RDS](Oracle.Procedural.Importing.md)\.