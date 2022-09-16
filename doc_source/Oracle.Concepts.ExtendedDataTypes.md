# Turning on extended data types in RDS for Oracle<a name="Oracle.Concepts.ExtendedDataTypes"></a>

Amazon RDS Oracle Database 12c supports extended data types\. With extended data types, the maximum size is 32,767 bytes for the VARCHAR2, NVARCHAR2, and RAW data types\. To use extended data types, set the `MAX_STRING_SIZE` parameter to `EXTENDED`\. For more information, see [Extended data types](https://docs.oracle.com/database/121/SQLRF/sql_elements001.htm#SQLRF55623) in the Oracle documentation\. 

If you don't want to use extended data types, keep the `MAX_STRING_SIZE` parameter set to `STANDARD` \(the default\)\. In this case, the size limits are 4,000 bytes for the `VARCHAR2` and `NVARCHAR2` data types, and 2,000 bytes for the RAW data type\.

You can turn on extended data types on a new or existing DB instance\. For new DB instances, DB instance creation time is typically longer when you turn on extended data types\. For existing DB instances, the DB instance is unavailable during the conversion process\.

The following are considerations for a DB instance with extended data types enabled:
+ When you turn on extended data types for a DB instance, you can't change the DB instance back to use the standard size for data types\. After a DB instance is converted to use extended data types, if you set the `MAX_STRING_SIZE` parameter back to `STANDARD` it results in the `incompatible-parameters` status\.
+ When you restore a DB instance that uses extended data types, you must specify a parameter group with the `MAX_STRING_SIZE` parameter set to `EXTENDED`\. During restore, if you specify the default parameter group or any other parameter group with `MAX_STRING_SIZE` set to `STANDARD` it results in the `incompatible-parameters` status\.
+ We recommend that you don't turn on extended data types for Oracle DB instances running on the t2\.micro DB instance class\.

When the DB instance status is `incompatible-parameters` because of the `MAX_STRING_SIZE` setting, the DB instance remains unavailable until you set the `MAX_STRING_SIZE` parameter to `EXTENDED` and reboot the DB instance\.

## Turning on extended data types for a new DB instance<a name="Oracle.Concepts.ExtendedDataTypes.CreateDBInstance"></a>

**To turn on extended data types for a new DB instance**

1. Set the `MAX_STRING_SIZE` parameter to `EXTENDED` in a parameter group\.

   To set the parameter, you can either create a new parameter group or modify an existing parameter group\.

   For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

1. Create a new Amazon RDS Oracle DB instance, and associate the parameter group with `MAX_STRING_SIZE` set to `EXTENDED` with the DB instance\.

   For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

## Turning on extended data types for an existing DB instance<a name="Oracle.Concepts.ExtendedDataTypes.ModifyDBInstance"></a>

When you modify a DB instance to turn on extended data types, the data in the database is converted to use the extended sizes\. The DB instance is unavailable during the conversion\. The amount of time it takes to convert the data depends on the DB instance class used by the DB instance and the size of the database\.

**Note**  
After you turn on extended data types, you can't perform a point\-in\-time restore to a time during the conversion\. You can restore to the time immediately before the conversion or after the conversion\.

**To turn on extended data types for an existing DB instance**

1. Take a snapshot of the database\.

   If there are invalid objects in the database, Amazon RDS tries to recompile them\. The conversion to extended data types can fail if Amazon RDS can't recompile an invalid object\. The snapshot enables you to restore the database if there is a problem with the conversion\. Always check for invalid objects before conversion and fix or drop those invalid objects\. For production databases, we recommend testing the conversion process on a copy of your DB instance first\.

   For more information, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

1. Set the `MAX_STRING_SIZE` parameter to `EXTENDED` in a parameter group\.

   To set the parameter, you can either create a new parameter group or modify an existing parameter group\.

   For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

1. Modify the DB instance to associate it with the parameter group with `MAX_STRING_SIZE` set to `EXTENDED`\.

   For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

1. Reboot the DB instance for the parameter change to take effect\.

   For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\.