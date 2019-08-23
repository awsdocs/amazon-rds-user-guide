# Oracle Spatial<a name="Oracle.Options.Spatial"></a>

Amazon RDS supports Oracle Spatial through the use of the `SPATIAL` option\. Oracle Spatial provides a SQL schema and functions that facilitate the storage, retrieval, update, and query of collections of spatial data in an Oracle database\. For more information, see [Spatial Concepts](http://docs.oracle.com/database/121/SPATL/spatial-concepts.htm#SPATL010) in the Oracle documentation\. 

**Important**  
If you use Oracle Spatial, Amazon RDS automatically updates your DB instance to the latest Oracle PSU if there are security vulnerabilities with a Common Vulnerability Scoring System \(CVSS\) score of 9\+ or other announced security vulnerabilities\. 

Amazon RDS supports Oracle Spatial for the following editions and versions of Oracle: 
+ Oracle Enterprise Edition, version 18\.0\.0\.0, all versions
+ Oracle Enterprise Edition, version 12\.2\.0\.1, all versions
+ Oracle Enterprise Edition, version 12\.1\.0\.2\.v13 or later
+ Oracle Enterprise Edition, version 11\.2\.0\.4\.v17 or later

## Prerequisites for Oracle Spatial<a name="Oracle.Options.Spatial.PreReqs"></a>

The following are prerequisites for using Oracle Spatial: 
+ Your DB instance must be inside a virtual private cloud \(VPC\)\. For more information, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\. 
+ Your DB instance must be of sufficient class\. Oracle Spatial is not supported for the db\.m1\.small, db\.t2\.micro, or db\.t2\.small DB instance classes\. For more information, see [DB Instance Class Support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\. 
+ Your DB instance must have **Auto Minor Version Upgrade** enabled\. This option enables your DB instance to receive minor DB engine version upgrades automatically when they become available\. Amazon RDS uses this option to update your DB instance to the latest Oracle Patch Set Update \(PSU\) or Release Update \(RU\)\. For more information, see [Settings for Oracle DB Instances](USER_ModifyInstance.Oracle.md#USER_ModifyInstance.Oracle.Settings)\. 
+ If your DB instance is running on major version 11\.2, you must install the `XMLDB` option\. For more information, see [Oracle XML DB](Appendix.Oracle.Options.XMLDB.md)\. 
+ An Oracle Spatial license from Oracle\. For more information, see [Oracle Spatial and Graph](https://shop.oracle.com/apex/product?p1=OracleSpatialandGraph) in the Oracle documentation\. 

## Best Practices for Oracle Spatial<a name="Oracle.Options.Spatial.BestPractces"></a>

The following are best practices for using Oracle Spatial: 
+ For maximum security, use the `SPATIAL` option with Secure Sockets Layer \(SSL\)\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 
+ Configure your DB instance to restrict access to your DB instance\. For more information, see [Scenarios for Accessing a DB Instance in a VPC](USER_VPC.Scenarios.md) and [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\. 

## Adding the Oracle Spatial Option<a name="Oracle.Options.Spatial.Add"></a>

The following is the general process for adding the `SPATIAL` option to a DB instance: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

There is a brief outage while the `SPATIAL` option is added\. After you add the option, you don't need to restart your DB instance\. As soon as the option group is active, Oracle Spatial is available\. 

**To add the `SPATIAL` option to a DB instance**

1. Determine the option group that you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine**, choose **oracle\-ee**\. 

   1. For **Major engine version**, choose the version of your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **SPATIAL** option to the option group\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating a DB Instance Running the Oracle Database Engine](USER_CreateOracleInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 

## Removing the Oracle Spatial Option<a name="Oracle.Options.Spatial.Remove"></a>

You can remove the `SPATIAL` option from a DB instance\. There is a brief outage while the option is removed\. After you remove the `SPATIAL` option, you don't need to restart your DB instance\. 

**Warning**  
 Removing the `SPATIAL` option can result in data loss if the DB instance is using data types that were enabled as part of the option\. Back up your data before proceeding\. For more information, see [Backing Up and Restoring Amazon RDS DB Instances](CHAP_CommonTasks.BackupRestore.md)\. 

To remove the `SPATIAL` option from a DB instance, do one of the following: 
+ Remove the `SPATIAL` option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ Modify the DB instance and specify a different option group that doesn't include the `SPATIAL` option\. This change affects a single DB instance\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 

## Related Topics<a name="Oracle.Options.Spatial.Related"></a>
+ [Oracle Locator](Oracle.Options.Locator.md)
+ [Options for Oracle DB Instances](Appendix.Oracle.Options.md)
+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)