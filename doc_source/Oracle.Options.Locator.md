# Oracle Locator<a name="Oracle.Options.Locator"></a>

Amazon RDS supports Oracle Locator through the use of the `LOCATOR` option\. Oracle Locator provides capabilities that are typically required to support internet and wireless service\-based applications and partner\-based GIS solutions\. Oracle Locator is a limited subset of Oracle Spatial\. For more information, see [Oracle Locator](https://docs.oracle.com/database/121/SPATL/sdo_locator.htm#SPATL340) in the Oracle documentation\. 

**Important**  
If you use Oracle Locator, Amazon RDS automatically updates your DB instance to the latest Oracle PSU if there are security vulnerabilities with a Common Vulnerability Scoring System \(CVSS\) score of 9\+ or other announced security vulnerabilities\. 

Amazon RDS supports Oracle Locator for the following editions and versions of Oracle: 
+ Oracle Standard Edition \(SE2\) or Enterprise Edition, version 19\.0\.0\.0, all versions
+ Oracle Standard Edition \(SE2\) or Enterprise Edition, version 18\.0\.0\.0, all versions
+ Oracle Standard Edition \(SE2\) or Enterprise Edition, version 12\.2\.0\.1, all versions
+ Oracle Standard Edition \(SE2\) or Enterprise Edition, version 12\.1\.0\.2\.v13 or later
+ Oracle Standard Edition \(SE, SE1\) or Enterprise Edition, version 11\.2\.0\.4\.v17 or later

## Prerequisites for Oracle Locator<a name="Oracle.Options.Locator.PreReqs"></a>

The following are prerequisites for using Oracle Locator: 
+ Your DB instance must be inside a virtual private cloud \(VPC\)\. For more information, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\. 
+ Your DB instance must be of sufficient class\. Oracle Locator is not supported for the db\.t3\.micro or db\.t3\.small DB instance classes\. For more information, see [DB Instance Class Support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\. 
+ Your DB instance must have **Auto Minor Version Upgrade** enabled\. This option enables your DB instance to receive minor DB engine version upgrades automatically when they become available and is required for any options that install the Oracle Java Virtual Machine \(JVM\)\. Amazon RDS uses this option to update your DB instance to the latest Oracle Patch Set Update \(PSU\) or Release Update \(RU\)\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 
+ If your DB instance is running on major version 11\.2, you must install the `XMLDB` option\. For more information, see [Oracle XML DB](Appendix.Oracle.Options.XMLDB.md)\. 

## Best Practices for Oracle Locator<a name="Oracle.Options.Locator.BestPractces"></a>

The following are best practices for using Oracle Locator: 
+ For maximum security, use the `LOCATOR` option with Secure Sockets Layer \(SSL\)\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 
+ Configure your DB instance to restrict access to your DB instance\. For more information, see [Scenarios for Accessing a DB Instance in a VPC](USER_VPC.Scenarios.md) and [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\. 

## Adding the Oracle Locator Option<a name="Oracle.Options.Locator.Add"></a>

The following is the general process for adding the `LOCATOR` option to a DB instance: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

If Oracle Java Virtual Machine \(JVM\) is *not* installed on the DB instance, there is a brief outage while the `LOCATOR` option is added\. There is no outage if Oracle Java Virtual Machine \(JVM\) is already installed on the DB instance\. After you add the option, you don't need to restart your DB instance\. As soon as the option group is active, Oracle OLAP is available\. 

**Note**  
During this outage, password verification functions are disabled briefly\. You can also expect to see events related to password verification functions during the outage\. Password verification functions are enabled again before the Oracle DB instance is available\.

**To add the `LOCATOR` option to a DB instance**

1. Determine the option group that you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine**, choose the oracle edition for your DB instance\. 

   1. For **Major engine version**, choose the version of your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **LOCATOR** option to the option group\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Using Oracle Locator<a name="Oracle.Options.Locator.Using"></a>

After you enable the Oracle Locator option, you can begin using it\. You should only use Oracle Locator features\. Don't use any Oracle Spatial features unless you have a license for Oracle Spatial\. 

For a list of features that are supported for Oracle Locator, see [Features Included with Locator](https://docs.oracle.com/database/121/SPATL/sdo_locator.htm#GUID-EC6DEA23-8FD7-4109-A0C1-93C0CE3D6FF2__CFACCEEG) in the Oracle documentation\. 

For a list of features that are not supported for Oracle Locator, see [Features Not Included with Locator](https://docs.oracle.com/database/121/SPATL/sdo_locator.htm#GUID-EC6DEA23-8FD7-4109-A0C1-93C0CE3D6FF2__CFABACEA) in the Oracle documentation\. 

## Removing the Oracle Locator Option<a name="Oracle.Options.Locator.Remove"></a>

You can remove the `LOCATOR` option from a DB instance\. If Oracle Java Virtual Machine \(JVM\) is *not* installed on the DB instance, there is a brief outage while the `OLAP` option is removed\. There is no outage if Oracle Java Virtual Machine \(JVM\) is already installed on the DB instance\. After you remove the `LOCATOR` option, you don't need to restart your DB instance\. 

**Warning**  
 Removing the `LOCATOR` option can result in data loss if the DB instance is using data types that were enabled as part of the option\. Back up your data before proceeding\. For more information, see [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)\. 

To remove the `LOCATOR` option from a DB instance, do one of the following: 
+ Remove the `LOCATOR` option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ Modify the DB instance and specify a different option group that doesn't include the `LOCATOR` option\. This change affects a single DB instance\. You can specify the default \(empty\) option group or a different custom option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Related Topics<a name="Oracle.Options.Locator.Related"></a>
+ [Oracle Spatial](Oracle.Options.Spatial.md)
+ [Options for Oracle DB Instances](Appendix.Oracle.Options.md)
+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)