# Oracle Label Security<a name="Oracle.Options.OLS"></a>

Amazon RDS supports Oracle Label Security for Oracle Enterprise Edition, version 12c, through the use of the OLS option\. 

Most database security controls access at the object level\. Oracle Label Security provides fine\-grained control of access to individual table rows\. For example, you can use Label Security to enforce regulatory compliance with a policy\-based administration model\. You can use Label Security policies to control access to sensitive data, and restrict access to only users with the appropriate clearance level\. For more information, see [Introduction to Oracle Label Security](https://docs.oracle.com/database/121/OLSAG/intro.htm#OLSAG001) in the Oracle documentation\. 

**Important**  
For Oracle 19c, Oracle 18c, and Oracle 12c version 12\.2 on Amazon RDS, Oracle Label Security is a permanent and persistent option\. You can't remove Oracle Label Security from an Oracle version 19c, 18c, or 12\.2 DB instance\.

## Prerequisites for Oracle Label Security<a name="Oracle.Options.OLS.PreReqs"></a>

The following are prerequisites for using Oracle Label Security: 
+ Your DB instance must use the Bring Your Own License model\. For more information, see [Oracle Licensing](CHAP_Oracle.md#Oracle.Concepts.Licensing)\. 
+ You must have a valid license for Oracle Enterprise Edition with Software Update License and Support\. 
+ Your Oracle license must include the Label Security option\. 

## Adding the Oracle Label Security Option<a name="Oracle.Options.OLS.Add"></a>

The general process for adding the Oracle Label Security option to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

After you add the Label Security option, as soon as the option group is active, Label Security is active\. 

**To add the Label Security option to a DB instance**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine**, choose **oracle\-ee**\. 

   1. For **Major engine version**, choose the version of your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **OLS** option to the option group\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.  
**Important**  
If you add Label Security to an existing option group that is already attached to one or more DB instances, all the DB instances are restarted\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. When you add the Label Security option to an existing DB instance, a brief outage occurs while your DB instance is automatically restarted\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Using Oracle Label Security<a name="Oracle.Options.OLS.Using"></a>

To use Oracle Label Security, you create policies that control access to specific rows in your tables\. For more information, see [Creating an Oracle Label Security Policy](https://docs.oracle.com/database/121/OLSAG/getstrtd.htm#OLSAG3096) in the Oracle documentation\. 

When you work with Label Security, you perform all actions as the LBAC\_DBA role\. The master user for your DB instance is granted the LBAC\_DBA role\. You can grant the LBAC\_DBA role to other users so that they can administer Label Security policies\. 

For Amazon RDS for Oracle 19c, 18c, and 12\.2 DB instances, you must grant access to the `OLS_ENFORCEMENT` package to any new users who require access to Oracle Label Security\. To grant access to the `OLS_ENFORCEMENT` package, connect to the DB instance as the master user and run the following SQL statement:

```
GRANT ALL ON LBACSYS.OLS_ENFORCEMENT TO username;
```

You can configure Label Security through the Oracle Enterprise Manager \(OEM\) Cloud Control\. Amazon RDS supports the OEM Cloud Control through the Management Agent option\. For more information, see [Oracle Management Agent for Enterprise Manager Cloud Control](Oracle.Options.OEMAgent.md)\. 

## Removing the Oracle Label Security Option<a name="Oracle.Options.OLS.Remove"></a>

You can remove Oracle Label Security from a DB instance\. 

To remove Label Security from a DB instance, do one of the following: 
+ To remove Label Security from multiple DB instances, remove the Label Security option from the option group they belong to\. This change affects all DB instances that use the option group\. When you remove Label Security from an option group that is attached to multiple DB instances, all the DB instances are restarted\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ To remove Label Security from a single DB instance, modify the DB instance and specify a different option group that doesn't include the Label Security option\. You can specify the default \(empty\) option group, or a different custom option group\. When you remove the Label Security option, a brief outage occurs while your DB instance is automatically restarted\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Troubleshooting<a name="Oracle.Options.OLS.Troubleshooting"></a>

The following are issues you might encounter when you use Oracle Label Security\. 


****  

| Issue | Troubleshooting Suggestions | 
| --- | --- | 
|  When you try to create a policy, you see an error message similar to the following: `insufficient authorization for the SYSDBA package`\.   |  A known issue with Oracle's Label Security feature prevents users with usernames of 16 or 24 characters from running Label Security commands\. You can create a new user with a different number of characters, grant LBAC\_DBA to the new user, log in as the new user, and run the OLS commands as the new user\. For additional information, please contact Oracle support\.   | 

## Related Topics<a name="Oracle.Options.OLS.Related"></a>
+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)
+ [Options for Oracle DB Instances](Appendix.Oracle.Options.md)