# Oracle Multimedia<a name="Oracle.Options.Multimedia"></a>

Amazon RDS supports Oracle Multimedia through the use of the `MULTIMEDIA` option\. You can use Oracle Multimedia to store, manage, and retrieve images, audio, video, and other heterogeneous media data\. For more information, see [Oracle Multimedia](https://docs.oracle.com/database/121/IMURG/title.htm) in the Oracle documentation\. 

**Important**  
If you use Oracle Multimedia, Amazon RDS automatically updates your DB instance to the latest Oracle PSU if there are security vulnerabilities with a Common Vulnerability Scoring System \(CVSS\) score of 9\+ or other announced security vulnerabilities\. 

Amazon RDS supports Oracle Multimedia for the following editions and versions of Oracle: 
+ Oracle Standard Edition \(SE2\) or Oracle Enterprise Edition, version 18\.0\.0\.0, all versions
+ Oracle Standard Edition \(SE2\) or Oracle Enterprise Edition, version 12\.2\.0\.1, all versions
+ Oracle Standard Edition \(SE2\) or Oracle Enterprise Edition, version 12\.1\.0\.2\.v13 or later
+ Oracle Standard Edition \(SE and SE1\) or Oracle Enterprise Edition, version 11\.2\.0\.4\.v17 or later

**Note**  
Oracle desupported Oracle Multimedia in Oracle Database 19c\. So, Oracle Multimedia isn't supported for Oracle 19c DB instances\. For more information, see [Desupport of Oracle Multimedia](https://docs.oracle.com/en/database/oracle/oracle-database/19/upgrd/behavior-changes-deprecated-desupport-oracle-database.html#GUID-BABC1C60-EA07-4EBE-8C67-B69B59E4F742) in the Oracle documentation\.

## Prerequisites for Oracle Multimedia<a name="Oracle.Options.Multimedia.PreReqs"></a>

The following are prerequisites for using Oracle Multimedia: 
+ Your DB instance must be inside a virtual private cloud \(VPC\)\. For more information, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\. 
+ Your DB instance must be of sufficient class\. Oracle Multimedia is not supported for the db\.t3\.micro or db\.t3\.small DB instance classes\. For more information, see [DB Instance Class Support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\. 
+ Your DB instance must have **Auto Minor Version Upgrade** enabled\. This option enables your DB instance to receive minor DB engine version upgrades automatically when they become available and is required for any options that install the Oracle Java Virtual Machine \(JVM\)\. Amazon RDS uses this option to update your DB instance to the latest Oracle Patch Set Update \(PSU\) or Release Update \(RU\)\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 
+ If your DB instance is running on major version 11\.2, you must install the `XMLDB` option\. For more information, see [Oracle XML DB](Appendix.Oracle.Options.XMLDB.md)\. 

## Best Practices for Oracle Multimedia<a name="Oracle.Options.Multimedia.BestPractces"></a>

The following are best practices for using Oracle Multimedia: 
+ For maximum security, use the `MULTIMEDIA` option with Secure Sockets Layer \(SSL\)\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 
+ Configure your DB instance to restrict access to your DB instance\. For more information, see [Scenarios for Accessing a DB Instance in a VPC](USER_VPC.Scenarios.md) and [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\. 

## Adding the Oracle Multimedia Option<a name="Oracle.Options.Multimedia.Add"></a>

The following is the general process for adding the `MULTIMEDIA` option to a DB instance: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

If Oracle Java Virtual Machine \(JVM\) is *not* installed on the DB instance, there is a brief outage while the `MULTIMEDIA` option is added\. There is no outage if Oracle Java Virtual Machine \(JVM\) is already installed on the DB instance\. After you add the option, you don't need to restart your DB instance\. As soon as the option group is active, Oracle OLAP is available\. 

**Note**  
During this outage, password verification functions are disabled briefly\. You can also expect to see events related to password verification functions during the outage\. Password verification functions are enabled again before the Oracle DB instance is available\.

**To add the `MULTIMEDIA` option to a DB instance**

1. Determine the option group that you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine**, choose **oracle\-ee**\. 

   1. For **Major engine version**, choose the version of your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **MULTIMEDIA** option to the option group\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Removing the Oracle Multimedia Option<a name="Oracle.Options.Multimedia.Remove"></a>

You can remove the `MULTIMEDIA` option from a DB instance\. If Oracle Java Virtual Machine \(JVM\) is *not* installed on the DB instance, there is a brief outage while the `OLAP` option is removed\. There is no outage if Oracle Java Virtual Machine \(JVM\) is already installed on the DB instance\. After you remove the `MULTIMEDIA` option, you don't need to restart your DB instance\. 

**Warning**  
 Removing the `MULTIMEDIA` option can result in data loss if the DB instance is using data types that were enabled as part of the option\. Back up your data before proceeding\. For more information, see [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)\. 

To remove the `MULTIMEDIA` option from a DB instance, do one of the following: 
+ Remove the `MULTIMEDIA` option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ Modify the DB instance and specify a different option group that doesn't include the `MULTIMEDIA` option\. This change affects a single DB instance\. You can specify the default \(empty\) option group or a different custom option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 