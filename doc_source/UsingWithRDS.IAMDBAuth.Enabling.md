# Enabling and Disabling IAM Database Authentication<a name="UsingWithRDS.IAMDBAuth.Enabling"></a>

By default, IAM database authentication is disabled on DB instances\. You can enable IAM database authentication \(or disable it again\) using the AWS Management Console, AWS CLI, or the Amazon RDS API\.

**Topics**
+ [AWS Management Console](#UsingWithRDS.IAMDBAuth.Enabling.Console)
+ [AWS CLI](#UsingWithRDS.IAMDBAuth.Enabling.CLI)
+ [API](#UsingWithRDS.IAMDBAuth.Enabling.API)

## AWS Management Console<a name="UsingWithRDS.IAMDBAuth.Enabling.Console"></a>

To create a new DB instance with IAM authentication by using the console, see [Creating a DB Instance Running the MySQL Database Engine](USER_CreateInstance.md)\.

Each of these creation workflows has a **Configure Advanced Settings** page, where you can enable IAM DB authentication\. In that page's **Database Options** section, choose **Yes** for **Enable IAM DB Authentication**\.

**To enable or disable IAM authentication for an existing DB instance**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the DB instance that you want to modify\.

1. Choose **Instance actions**, and then choose **Modify**\.

1. In the **Database options** section, for **IAM DB authentication**, choose **Enable IAM DB authentication** or **Disable**, and then choose **Continue**\.

1. To apply the changes immediately, choose **Apply immediately**\.

1. Choose **Modify DB instance** \.

**To restore a DB instance**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the snapshot you want to restore, and then choose **Restore Snapshot** from **Snapshot Actions**\.

1. In the **Settings** section, type an identifier for the DB instance in **DB Instance Identifier**\.

1. In the **Database options** section, for **IAM DB authentication**, choose **Enable IAM DB authentication** or **Disable**\.

1. Choose **Restore DB Instance**\.

## AWS CLI<a name="UsingWithRDS.IAMDBAuth.Enabling.CLI"></a>

To create a new DB instance with IAM authentication by using the AWS CLI, use the following command:
+ [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)

Specify the `--enable-iam-database-authentication` option, as shown in the following example\.

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

For an existing DB instance, use one of the following AWS CLI command:
+ [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)

Specify either the `--enable-iam-database-authentication` or `--no-enable-iam-database-authentication` option, as appropriate\. 

By default, Amazon RDS performs the modification during the next maintenance window\. If you want to override this and enable IAM DB authentication as soon as possible, use the `--apply-immediately` parameter\. 

The following example shows how to immediately enable IAM authentication for an existing DB instance\.

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --apply-immediately \
    --enable-iam-database-authentication
```

If you are restoring a DB instance, use one of the following AWS CLI commands:
+ `[restore\-db\-instance\-to\-point\-in\-time](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html)`
+ `[restore\-db\-instance\-from\-db\-snapshot](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)`

The IAM database authentication setting defaults to that of the source snapshot\. To change this setting, set the `--enable-iam-database-authentication` or `--no-enable-iam-database-authentication` option, as appropriate\.

## API<a name="UsingWithRDS.IAMDBAuth.Enabling.API"></a>

For a new DB instance, use the following API action:
+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)

Set the `EnableIAMDatabaseAuthentication` parameter to `true`\.

For an existing DB instance, use the following API action:
+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)

Set the `EnableIAMDatabaseAuthentication` to `true` to enable IAM authentication, or `false` to disable it\.

If you are restoring a DB instance, use one of the following API actions:
+  [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html)
+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html)

The IAM database authentication setting defaults to that of the source snapshot\. To change this setting, set the `EnableIAMDatabaseAuthentication` to `true` to enable IAM authentication, or `false` to disable it\.