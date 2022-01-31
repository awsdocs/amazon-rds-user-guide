# Upgrading the Oracle DB engine<a name="USER_UpgradeDBInstance.Oracle"></a>

When Amazon RDS supports a new version of Oracle, you can upgrade your DB instances to the new version\. For information about which Oracle versions are available on Amazon RDS, see [Oracle database engine release notes](Appendix.Oracle.PatchComposition.md)\.

**Important**  
RDS for Oracle Database 11g is deprecated\. If you maintain Oracle Database 11g snapshots, you can upgrade them to a later release\. For more information, see [Upgrading an Oracle DB snapshot](USER_UpgradeDBSnapshot.Oracle.md)\.

**Topics**
+ [Overview of Oracle DB engine upgrades](USER_UpgradeDBInstance.Oracle.Overview.md)
+ [Major version upgrades](USER_UpgradeDBInstance.Oracle.Major.md)
+ [Oracle minor version upgrades](USER_UpgradeDBInstance.Oracle.Minor.md)
+ [Oracle SE2 upgrade paths](USER_UpgradeDBInstance.Oracle.SE2.md)
+ [Considerations for Oracle DB upgrades](#USER_UpgradeDBInstance.Oracle.OGPG)
+ [Testing an Oracle DB upgrade](#USER_UpgradeDBInstance.Oracle.UpgradeTesting)
+ [Upgrading an Oracle DB instance](#USER_UpgradeDBInstance.Oracle.Upgrading)
+ [Upgrading an Oracle DB snapshot](USER_UpgradeDBSnapshot.Oracle.md)

## Considerations for Oracle DB upgrades<a name="USER_UpgradeDBInstance.Oracle.OGPG"></a>

Before upgrading, review the implications for option groups, parameter groups, and time zones\.

**Topics**
+ [Option group considerations](#USER_UpgradeDBInstance.Oracle.OGPG.OG)
+ [Parameter group considerations](#USER_UpgradeDBInstance.Oracle.OGPG.PG)
+ [Time zone considerations](#USER_UpgradeDBInstance.Oracle.OGPG.DST)

### Option group considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.OG"></a>

If your DB instance uses a custom option group, sometimes Amazon RDS can't automatically assign a new option group\. For example, this situation occurs when you upgrade to a new major version\. In such cases, specify a new option group when you upgrade\. We recommend that you create a new option group, and add the same options to it as in your existing custom option group\. 

For more information, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create) or [Copying an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Copy)\. 

If your DB instance uses a custom option group that contains the APEX option, you can sometimes reduce the upgrade time\. To do this, upgrade your version of APEX at the same time as your DB instance\. For more information, see [Upgrading the APEX version](Appendix.Oracle.Options.APEX.md#Appendix.Oracle.Options.APEX.Upgrade)\. 

### Parameter group considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.PG"></a>

If your DB instance uses a custom parameter group, sometimes Amazon RDS can't automatically assign your DB instance a new parameter group\. For example, this situation occurs when you upgrade to a new major version\. In such cases, make sure to specify a new parameter group when you upgrade\. We recommend that you create a new parameter group, and configure the parameters as in your existing custom parameter group\.

For more information, see [Creating a DB parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Creating) or [Copying a DB parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Copying)\. 

### Time zone considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.DST"></a>

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

## Testing an Oracle DB upgrade<a name="USER_UpgradeDBInstance.Oracle.UpgradeTesting"></a>

Before you upgrade your DB instance to a major version, thoroughly test your database and all applications that access the database for compatibility with the new version\. We recommend that you use the following procedure\. 

**To test a major version upgrade**

1. Review the Oracle upgrade documentation for the new version of the database engine to see if there are compatibility issues that might affect your database or applications\. For more information, see [Database Upgrade Guide](https://docs.oracle.com/database/121/UPGRD/toc.htm) in the Oracle documentation\. 

1. If your DB instance uses a custom option group, create a new option group compatible with the new version you are upgrading to\. For more information, see [Option group considerations](#USER_UpgradeDBInstance.Oracle.OGPG.OG)\. 

1. If your DB instance uses a custom parameter group, create a new parameter group compatible with the new version you are upgrading to\. For more information, see [Parameter group considerations](#USER_UpgradeDBInstance.Oracle.OGPG.PG)\. 

1. Create a DB snapshot of the DB instance to be upgraded\. For more information, see [Creating a DB snapshot](USER_CreateSnapshot.md)\. 

1. Restore the DB snapshot to create a new test DB instance\. For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\. 

1. Modify this new test DB instance to upgrade it to the new version, by using one of the following methods: 
   + [Console](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.Console)
   + [AWS CLI](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.CLI)
   + [RDS API](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.API)

1. Perform testing: 
   + Run as many of your quality assurance tests against the upgraded DB instance as needed to ensure that your database and application work correctly with the new version\. 
   + Implement any new tests needed to evaluate the impact of any compatibility issues that you identified in step 1\. 
   + Test all stored procedures, functions, and triggers\. 
   + Direct test versions of your applications to the upgraded DB instance\. Verify that the applications work correctly with the new version\. 
   + Evaluate the storage used by the upgraded instance to determine if the upgrade requires additional storage\. You might need to choose a larger instance class to support the new version in production\. For more information, see [DB instance classes](Concepts.DBInstanceClass.md)\. 

1. If all tests pass, upgrade your production DB instance\. We recommend that you confirm that the DB instance working correctly before allowing write operations to the DB instance\.

## Upgrading an Oracle DB instance<a name="USER_UpgradeDBInstance.Oracle.Upgrading"></a>

For information about manually or automatically upgrading an Oracle DB instance, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.