# Configuring unified auditing for Oracle Database<a name="DBActivityStreams.configuring-auditing"></a>

When you configure unified auditing for use with database activity streams, the following situations are possible:
+ Unified auditing isn't configured for your Oracle database\.

  In this case, create new policies with the `CREATE AUDIT POLICY` command, and then enable them with the `AUDIT POLICY` command\. The following example creates and enables a policy to monitor users with specific privileges and roles\.

  ```
  CREATE AUDIT POLICY table_pol
  PRIVILEGES CREATE ANY TABLE, DROP ANY TABLE
  ROLES emp_admin, sales_admin;
  
  AUDIT POLICY table_pol;
  ```

  For complete instructions, see [Configuring Audit Policies](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/configuring-audit-policies.html#GUID-22CDB667-5AA2-4051-A262-FBD0236763CB) in the Oracle Database documentation\.
+ Unified auditing is configured for your Oracle database\.

  When you enable a database activity stream, RDS for Oracle automatically clears existing audit data\. It also revokes audit trail privileges\. RDS for Oracle can no longer do the following:
  + Purge unified audit trail records\.
  + Add, delete, or modify the unified audit policy\.
  + Update the last archived time stamp\.
**Important**  
We strongly recommend that you back up your audit data before enabling a database activity stream\.

  For a description of the `UNIFIED_AUDIT_TRAIL` view, see [UNIFIED\_AUDIT\_TRAIL](https://docs.oracle.com/database/121/REFRN/GUID-B7CE1C02-2FD4-47D6-80AA-CF74A60CDD1D.htm#REFRN29162)\. If you have an account with Oracle Support, see [How To Purge The UNIFIED AUDIT TRAIL](https://support.oracle.com/knowledge/Oracle%20Database%20Products/1582627_1.html)\.