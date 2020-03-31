# Oracle Java Virtual Machine<a name="oracle-options-java"></a>

Amazon RDS supports Oracle Java Virtual Machine \(JVM\) through the use of the `JVM` option\. Oracle Java provides a SQL schema and functions that facilitate Oracle Java features in an Oracle database\. For more information, see [ Introduction to Java in Oracle Database](https://docs.oracle.com/database/121/JJDEV/chone.htm) in the Oracle documentation\.

You can use Oracle JVM with the following Oracle Database versions:
+ Oracle 19c, 19\.0\.0\.0, all versions
+ Oracle 18c, 18\.0\.0\.0, all versions
+ Oracle 12c, 12\.2\.0\.1, all versions
+ Oracle 12c, 12\.1\.0\.2\.v13 or later
+ Oracle 11g, 11\.2\.0\.4\.v17 or later

Java implementation in Amazon RDS has a limited set of permissions\. The master user is granted the `RDS_JAVA_ADMIN` role, which grants a subset of the privileges granted by the `JAVA_ADMIN` role\. To list the privileges granted to the `RDS_JAVA_ADMIN` role, run the following query on your DB instance:

```
SELECT * FROM dba_java_policy 
   WHERE grantee IN ('RDS_JAVA_ADMIN', 'PUBLIC') 
   AND enabled = 'ENABLED' 
   ORDER BY type_name, name, grantee;
```

## Prerequisites for Oracle JVM<a name="oracle-options-java.prerequisites"></a>

The following are prerequisites for using Oracle Java:
+ Your DB instance must be inside a virtual private cloud \(VPC\)\. For more information, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\. 
+ Your DB instance must be of a large enough class\. Oracle Java isn't supported for the db\.t3\.micro or db\.t3\.small DB instance classes\. For more information, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.
+ Your DB instance must have **Auto Minor Version Upgrade** enabled\. This option enables your DB instance to receive minor DB engine version upgrades automatically when they become available\. Amazon RDS uses this option to update your DB instance to the latest Oracle Patch Set Update \(PSU\) or Release Update \(RU\)\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 
+ If your DB instance is running on major version 11\.2, you must install the `XMLDB` option\. For more information, see [Oracle XML DB](Appendix.Oracle.Options.XMLDB.md)\.

## Best Practices for Oracle JVM<a name="oracle-options-java.best-practices"></a>

The following are best practices for using Oracle Java: 
+ For maximum security, use the `JVM` option with Secure Sockets Layer \(SSL\)\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 
+ Configure your DB instance to restrict network access\. For more information, see [Scenarios for Accessing a DB Instance in a VPC](USER_VPC.Scenarios.md) and [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\. 

## Adding the Oracle JVM Option<a name="oracle-options-java.add"></a>

The following is the general process for adding the `JVM` option to a DB instance: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

There is a brief outage while the `JVM` option is added\. After you add the option, you don't need to restart your DB instance\. As soon as the option group is active, Oracle Java is available\. 

**Note**  
During this outage, password verification functions are disabled briefly\. You can also expect to see events related to password verification functions during the outage\. Password verification functions are enabled again before the Oracle DB instance is available\.

**To add the JVM option to a DB instance**

1. Determine the option group that you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 
   + For **Engine**, choose the DB engine used by the DB instance \(**oracle\-ee**, **oracle\-se**, **oracle\-se1**, or **oracle\-se2**\)\. 
   + For **Major engine version**, choose the version of your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **JVM** option to the option group\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.
   + For an existing DB instance, apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. Grant the required permissions to users\.

   The Amazon RDS master user has the permissions to use the `JVM` option by default\. If other users require these permissions, connect to the DB instance as the master user in a SQL client and grant the permissions to the users\.

   The following example grants the permissions to use the `JVM` option to the `test_proc` user\.

   ```
   create user test_proc identified by password;
   CALL dbms_java.grant_permission('TEST_PROC', 'oracle.aurora.security.JServerPermission', 'LoadClassInPackage.*', '');
   ```

   After the user is granted the permissions, the following query should return output\.

   ```
   select * from dba_java_policy where grantee='TEST_PROC';                
   ```
**Note**  
The Oracle user name is case\-sensitive, and it usually has all uppercase characters\.

## Removing the Oracle JVM Option<a name="oracle-options-java.remove"></a>

You can remove the `JVM` option from a DB instance\. There is a brief outage while the option is removed\. After you remove the `JVM` option, you don't need to restart your DB instance\. 

**Warning**  
 Removing the `JVM` option can result in data loss if the DB instance is using data types that were enabled as part of the option\. Back up your data before proceeding\. For more information, see [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)\. 

To remove the `JVM` option from a DB instance, do one of the following: 
+ Remove the `JVM` option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ Modify the DB instance and specify a different option group that doesn't include the `JVM` option\. This change affects a single DB instance\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 