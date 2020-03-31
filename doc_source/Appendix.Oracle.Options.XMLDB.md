# Oracle XML DB<a name="Appendix.Oracle.Options.XMLDB"></a>

Oracle XML DB adds native XML support to your DB instance\. With XML DB, you can store and retrieve structured or unstructured XML, in addition to relational data\. 

XML DB is pre\-installed on Oracle version 12c and later\. Amazon RDS supports Oracle XML DB for version 11g through the use of the XMLDB option\. After you apply the XMLDB option to your DB instance, you have full access to the Oracle XML DB repository; no post\-installation tasks are required\. 

**Note**  
The Amazon RDS XMLDB option does not provide support for the Oracle XML DB Protocol Server\. 

## Adding the Oracle XML DB Option<a name="Oracle.Options.XMLDB.Add"></a>

The general process for adding the Oracle XML DB option to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

After you add the XML DB option, as soon as the option group is active, XML DB is active\. 

**To add the XML DB option to a DB instance**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine**, choose the edition of Oracle you want to use\. 

   1. For **Major engine version**, choose **11\.2**\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **XMLDB** option to the option group\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Removing the Oracle XML DB Option<a name="Oracle.Options.XMLDB.Remove"></a>

You can remove the XML DB option from a DB instance running version 11g\. 

To remove the XML DB option from a DB instance running version 11g, do one of the following: 
+ To remove the XMLDB option from multiple DB instances, remove the XMLDB option from the option group they belong to\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ To remove the XMLDB option from a single DB instance, modify the DB instance and specify a different option group that doesn't include the XMLDB option\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 