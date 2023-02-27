# Considerations for Oracle DB upgrades<a name="USER_UpgradeDBInstance.Oracle.OGPG"></a>

Before you upgrade your Oracle instance, review the following information\.

**Topics**
+ [Oracle Multitenant considerations](#USER_UpgradeDBInstance.Oracle.multi)
+ [Option group considerations](#USER_UpgradeDBInstance.Oracle.OGPG.OG)
+ [Parameter group considerations](#USER_UpgradeDBInstance.Oracle.OGPG.PG)
+ [Time zone considerations](#USER_UpgradeDBInstance.Oracle.OGPG.DST)

## Oracle Multitenant considerations<a name="USER_UpgradeDBInstance.Oracle.multi"></a>

The following table describes the architectures supported in different releases\.


| Oracle Database release | RDS support status | Architecture | 
| --- | --- | --- | 
|  Oracle Database 21c  | Supported |  CDB only  | 
|  Oracle Database 19c  | Supported |  CDB or non\-CDB  | 
|  Oracle Database 12c Release 2 \(12\.2\)  | Desupported |  Non\-CDB only  | 
|  Oracle Database 12c Release 1 \(12\.1\)  | Desupported |  Non\-CDB only  | 

The following table describes supported and unsupported upgrade paths\.


| Upgrade path | Supported? | 
| --- | --- | 
|  Non\-CDB to non\-CDB  |  Yes  | 
|  CDB to CDB  |  Yes  | 
|  Non\-CDB to CDB  |  No  | 
|  CDB to non\-CDB  |  No  | 

For more information about Oracle Multitenant in RDS for Oracle, see [RDS for Oracle architecture](Oracle.Concepts.single-tenant.md)\.

## Option group considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.OG"></a>

If your DB instance uses a custom option group, sometimes Amazon RDS can't automatically assign a new option group\. For example, this situation occurs when you upgrade to a new major version\. In such cases, specify a new option group when you upgrade\. We recommend that you create a new option group, and add the same options to it as in your existing custom option group\. 

For more information, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create) or [Copying an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Copy)\. 

If your DB instance uses a custom option group that contains the APEX option, you can sometimes reduce the upgrade time\. To do this, upgrade your version of APEX at the same time as your DB instance\. For more information, see [Upgrading the APEX version](Appendix.Oracle.Options.APEX.md#Appendix.Oracle.Options.APEX.Upgrade)\. 

## Parameter group considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.PG"></a>

If your DB instance uses a custom parameter group, sometimes Amazon RDS can't automatically assign your DB instance a new parameter group\. For example, this situation occurs when you upgrade to a new major version\. In such cases, make sure to specify a new parameter group when you upgrade\. We recommend that you create a new parameter group, and configure the parameters as in your existing custom parameter group\.

For more information, see [Creating a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Creating) or [Copying a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Copying)\. 

## Time zone considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.DST"></a>

You can use the time zone option to change the *system time zone* used by your Oracle DB instance\. For example, you might change the time zone of a DB instance to be compatible with an on\-premises environment, or a legacy application\. The time zone option changes the time zone at the host level\. Amazon RDS for Oracle updates the system time zone automatically throughout the year\. For more information about the system time zone, see [Oracle time zone](Appendix.Oracle.Options.Timezone.md)\.

When you create an Oracle DB instance, the database automatically sets the *database time zone*\. The database time zone is also known as the Daylight Saving Time \(DST\) time zone\. The database time zone is distinct from the system time zone\.

Between Oracle Database releases, patch sets or individual patches may include new DST versions\. These patches reflect the changes in transition rules for various time zone regions\. For example, a government might change when DST takes effect\. Changes to DST rules may affect existing data of the `TIMESTAMP WITH TIME ZONE` data type\.

If you upgrade an RDS for Oracle DB instance, Amazon RDS doesn't upgrade the database time zone file automatically\. To upgrade the time zone file automatically, you can include the `TIMEZONE_FILE_AUTOUPGRADE` option in the option group associated with your DB instance during or after the engine version upgrade\. For more information, see [Oracle time zone file autoupgrade](Appendix.Oracle.Options.Timezone-file-autoupgrade.md)\.

Alternatively, to upgrade the database time zone file manually, create a new Oracle DB instance that has the desired DST patch\. However, we recommend that you upgrade the database time zone file using the `TIMEZONE_FILE_AUTOUPGRADE` option\.

After upgrading the time zone file, migrate the data from your current instance to the new instance\. You can migrate data using several techniques, including the following:
+ AWS Database Migration Service
+ Oracle GoldenGate
+ Oracle Data Pump
+ Original Export/Import \(desupported for general use\)

**Note**  
When you migrate data using Oracle Data Pump, the utility raises the error ORA\-39405 when the target time zone version is lower than the source time zone version\.

For more information, see [TIMESTAMP WITH TIMEZONE restrictions](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-9B6C92EE-860E-43DD-9728-735B17B9DA89) in the Oracle documentation\. 