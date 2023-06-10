# RDS for Oracle database architecture<a name="oracle-multi-architecture"></a>

The multitenant architecture enables an Oracle database to function as a multitenant container database \(CDB\)\. A CDB can include customer\-created pluggable databases \(PDBs\)\. A non\-CDB is an Oracle database that uses the traditional architecture, which can't contain PDBs\. For more information about the multitenant architecture, see [https://docs.oracle.com/en/database/oracle/oracle-database/19/multi/introduction-to-the-multitenant-architecture.html#GUID-267F7D12-D33F-4AC9-AA45-E9CD671B6F22](https://docs.oracle.com/en/database/oracle/oracle-database/19/multi/introduction-to-the-multitenant-architecture.html#GUID-267F7D12-D33F-4AC9-AA45-E9CD671B6F22)\.

For Oracle Database 19c and higher, RDS for Oracle supports the single\-tenant configuration of the multitenant architecture\. In this case, your CDB contains only one PDB\. The single\-tenant configuration of the multitenant architecture uses the same RDS APIs as the non\-CDB architecture\. Thus, your experience with a PDB is mostly identical to your experience with a non\-CDB\. 

**Note**  
You can't access the CDB itself\.

In Oracle Database 21c and higher, all databases are CDBs\. In contrast, you can create an Oracle Database 19c DB instance as either a CDB or non\-CDB\. You can't upgrade a non\-CDB to a CDB, but you convert an Oracle Database 19c non\-CDB to a CDB, and then upgrade it\. You can't convert a CDB to a non\-CDB\.

For more information, see the following resources:
+ [Working with CDBs in RDS for Oracle](oracle-multitenant.md)
+ [Limitations of a single\-tenant CDB](Oracle.Concepts.limitations.md#Oracle.Concepts.single-tenant-limitations)
+ [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)