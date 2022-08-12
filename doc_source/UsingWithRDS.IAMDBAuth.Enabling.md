# Enabling and disabling IAM database authentication<a name="UsingWithRDS.IAMDBAuth.Enabling"></a>

By default, IAM database authentication is disabled on DB instances\. You can enable or disable IAM database authentication using the AWS Management Console, AWS CLI, or the API\.

You can enable IAM database authentication when you perform one of the following actions:
+ To create a new DB instance with IAM database authentication enabled, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.
+ To modify a DB instance to enable IAM database authentication, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.
+ To restore a DB instance from a snapshot with IAM database authentication enabled, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.
+ To restore a DB instance to a point in time with IAM database authentication enabled, see [Restoring a DB instance to a specified time](USER_PIT.md)\.

IAM authentication for PostgreSQL DB instances requires that the SSL value be 1\. You can't enable IAM authentication for a PostgreSQL DB instance if the SSL value is 0\. You can't change the SSL value to 0 if IAM authentication is enabled for a PostgreSQL DB instance\. 

## Console<a name="UsingWithRDS.IAMDBAuth.Enabling.Console"></a>

Each creation or modification workflow has a **Database authentication** section, where you can enable or disable IAM database authentication\. In that section, choose **Password and IAM database authentication** to enable IAM database authentication\.

**To enable or disable IAM database authentication for an existing DB instance**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify\.
**Note**  
 Make sure that the DB instance is compatible with IAM authentication\. Check the compatibility requirements in [Region and version availability](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability)\.

1. Choose **Modify**\.

1. In the **Database authentication** section, choose **Password and IAM database authentication** to enable IAM database authentication\.

1. Choose **Continue**\.

1. To apply the changes immediately, choose **Immediately** in the **Scheduling of modifications** section\.

1. Choose **Modify DB instance** \.

## AWS CLI<a name="UsingWithRDS.IAMDBAuth.Enabling.CLI"></a>

To create a new DB instance with IAM authentication by using the AWS CLI, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command\. Specify the `--enable-iam-database-authentication` option, as shown in the following example\.

```
aws rds create-db-instance \
    --db-instance-identifier mydbinstance \
    --db-instance-class db.m3.medium \
    --engine MySQL \
    --allocated-storage 20 \
    --master-username masterawsuser \
    --master-user-password masteruserpassword \
    --enable-iam-database-authentication
```

To update an existing DB instance to have or not have IAM authentication, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. Specify either the `--enable-iam-database-authentication` or `--no-enable-iam-database-authentication` option, as appropriate\.

**Note**  
 Make sure that the DB instance is compatible with IAM authentication\. Check the compatibility requirements in [Region and version availability](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability)\.

By default, Amazon RDS performs the modification during the next maintenance window\. If you want to override this and enable IAM DB authentication as soon as possible, use the `--apply-immediately` parameter\. 

The following example shows how to immediately enable IAM authentication for an existing DB instance\.

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --apply-immediately \
    --enable-iam-database-authentication
```

If you are restoring a DB instance, use one of the following AWS CLI commands:
+ `[restore\-db\-instance\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html)`
+ `[restore\-db\-instance\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)`

The IAM database authentication setting defaults to that of the source snapshot\. To change this setting, set the `--enable-iam-database-authentication` or `--no-enable-iam-database-authentication` option, as appropriate\.

## RDS API<a name="UsingWithRDS.IAMDBAuth.Enabling.API"></a>

To create a new DB instance with IAM authentication by using the API, use the API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)\. Set the `EnableIAMDatabaseAuthentication` parameter to `true`\.

To update an existing DB instance to have IAM authentication, use the API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)\. Set the `EnableIAMDatabaseAuthentication` parameter to `true` to enable IAM authentication, or `false` to disable it\.

**Note**  
 Make sure that the DB instance is compatible with IAM authentication\. Check the compatibility requirements in [Region and version availability](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability)\.

If you are restoring a DB instance, use one of the following API operations:
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html)
+  [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html)

The IAM database authentication setting defaults to that of the source snapshot\. To change this setting, set the `EnableIAMDatabaseAuthentication` parameter to `true` to enable IAM authentication, or `false` to disable it\.