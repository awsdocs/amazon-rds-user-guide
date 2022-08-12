# Oracle OLAP<a name="Oracle.Options.OLAP"></a>

Amazon RDS supports Oracle OLAP through the use of the `OLAP` option\. This option provides On\-line Analytical Processing \(OLAP\) for Oracle DB instances\. You can use Oracle OLAP to analyze large amounts of data by creating dimensional objects and cubes in accordance with the OLAP standard\. For more information, see [the Oracle documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/olaug/index.html)\. 

**Important**  
If you use Oracle OLAP, Amazon RDS automatically updates your DB instance to the latest Oracle PSU if there are security vulnerabilities with a Common Vulnerability Scoring System \(CVSS\) score of 9\+ or other announced security vulnerabilities\. 

Amazon RDS supports Oracle OLAP for the following editions and versions of Oracle: 
+ Oracle Database 21c Enterprise Edition, all versions
+ Oracle Database 19c Enterprise Edition, all versions
+ Oracle Database 12c Release 2 \(12\.2\.0\.1\) Enterprise Edition, all versions
+ Oracle Database 12c Release 1 \(12\.1\.0\.2\) Enterprise Edition, version 12\.1\.0\.2\.v13 or later

## Prerequisites for Oracle OLAP<a name="Oracle.Options.OLAP.PreReqs"></a>

The following are prerequisites for using Oracle OLAP: 
+ You must have an Oracle OLAP license from Oracle\. For more information, see [Licensing Information](https://docs.oracle.com/en/database/oracle/oracle-database/19/dblic/Licensing-Information.html#GUID-B6113390-9586-46D7-9008-DCC9EDA45AB4) in the Oracle documentation\. 
+ Your DB instance must be of a sufficient instance class\. Oracle OLAP isn't supported for the db\.t3\.micro or db\.t3\.small DB instance classes\. For more information, see [RDS for Oracle instance classes](Oracle.Concepts.InstanceClasses.md)\. 
+ Your DB instance must have **Auto Minor Version Upgrade** enabled\. This option enables your DB instance to receive minor DB engine version upgrades automatically when they become available and is required for any options that install the Oracle Java Virtual Machine \(JVM\)\. Amazon RDS uses this option to update your DB instance to the latest Oracle Patch Set Update \(PSU\) or Release Update \(RU\)\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 
+ Your DB instance must not have a user named `OLAPSYS`\. If it does, the OLAP option installation fails\.

## Best practices for Oracle OLAP<a name="Oracle.Options.OLAP.BestPractces"></a>

The following are best practices for using Oracle OLAP: 
+ For maximum security, use the `OLAP` option with Secure Sockets Layer \(SSL\)\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 
+ Configure your DB instance to restrict access to your DB instance\. For more information, see [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md) and [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\. 

## Adding the Oracle OLAP option<a name="Oracle.Options.OLAP.Add"></a>

The following is the general process for adding the `OLAP` option to a DB instance: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

If Oracle Java Virtual Machine \(JVM\) is *not* installed on the DB instance, there is a brief outage while the `OLAP` option is added\. There is no outage if Oracle Java Virtual Machine \(JVM\) is already installed on the DB instance\. After you add the option, you don't need to restart your DB instance\. As soon as the option group is active, Oracle OLAP is available\. 

**To add the OLAP option to a DB instance**

1. Determine the option group that you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 
   + For **Engine**, choose the Oracle edition for your DB instance\. 
   + For **Major engine version**, choose the version of your DB instance\. 

   For more information, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **OLAP** option to the option group\. For more information about adding options, see [Adding an option to an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

## Using Oracle OLAP<a name="Oracle.Options.OLAP.Using"></a>

After you enable the Oracle OLAP option, you can begin using it\. For a list of features that are supported for Oracle OLAP, see [the Oracle documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/olaug/overview.html#GUID-E2056FE4-C623-4D29-B7D8-C4762F941966)\. 

## Removing the Oracle OLAP option<a name="Oracle.Options.OLAP.Remove"></a>

After you drop all objects that use data types provided by the `OLAP` option, you can remove the option from a DB instance\. If Oracle Java Virtual Machine \(JVM\) is *not* installed on the DB instance, there is a brief outage while the `OLAP` option is removed\. There is no outage if Oracle Java Virtual Machine \(JVM\) is already installed on the DB instance\. After you remove the `OLAP` option, you don't need to restart your DB instance\.

**To drop the `OLAP` option**

1. Back up your data\.
**Warning**  
If the instance uses data types that were enabled as part of the option, and if you remove the `OLAP` option, you can lose data\. For more information, see [Backing up and restoring an Amazon RDS DB instance](CHAP_CommonTasks.BackupRestore.md)\.

1. Check whether any existing objects reference data types or features of the `OLAP` option\. 

1. Drop any objects that reference data types or features of the `OLAP` option\.

1. Do one of the following:
   + Remove the `OLAP` option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an option from an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\.
   + Modify the DB instance and specify a different option group that doesn't include the `OLAP` option\. This change affects a single DB instance\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 