# Enabling and Disabling IAM Database Authentication<a name="UsingWithRDS.IAMDBAuth.Enabling"></a>

By default, IAM database authentication is disabled on DB instances and DB clusters\. You can enable IAM database authentication \(or disable it again\) using the AWS Management Console, AWS CLI, or the Amazon RDS API\.


+ [AWS Management Console](#UsingWithRDS.IAMDBAuth.Enabling.Console)
+ [AWS CLI](#UsingWithRDS.IAMDBAuth.Enabling.CLI)
+ [Amazon RDS API](#UsingWithRDS.IAMDBAuth.Enabling.API)

## AWS Management Console<a name="UsingWithRDS.IAMDBAuth.Enabling.Console"></a>

To create a new DB instance or DB cluster with IAM authentication by using the console, see the following workflows: 

+ For Amazon RDS for MySQL, see [Creating a DB Instance Running the MySQL Database Engine](USER_CreateInstance.md)\.

+ For Aurora MySQL, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

Each of these creation workflows has a **Configure Advanced Settings** page, where you can enable IAM DB authentication\. In that page's **Database Options** section, choose **Yes** for **Enable IAM DB Authentication**\.

**To enable or disable IAM authentication for an existing DB instance or cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. From the dashboard, choose either **Instances** or **Clusters**\.

1. Choose the DB instance or DB cluster that you want to modify, and then choose **Instance Actions**, **Modify** or **Modify Cluster** as appropriate\.

1. In the **Database Options** section, for **Enable IAM DB Authentication** choose **Yes** \(to enable\) or **No** \(to disable\), and then choose **Continue**\.

1. Choose **Modify DB Instance** or **Modify Cluster** as appropriate\.

**To restore a DB instance or cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. From the dashboard, choose **Snapshots**\.

1. Choose the snapshot you want to restore, and then choose **Snapshot Actions**, **Restore Snapshot**\.

1. In the **Database Options** section, go to **Enable IAM DB Authentication** and choose **Yes** \(to enable\) or **No** \(to disable\)\.

1. Choose **Restore DB Instance**\.

## AWS CLI<a name="UsingWithRDS.IAMDBAuth.Enabling.CLI"></a>

To create a new DB instance or DB cluster with IAM authentication by using the AWS CLI, use one of the following commands:

+ [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) for Amazon RDS MySQL

+ [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) for Aurora MySQL

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

For an existing DB instance or DB cluster, use one of the following AWS CLI commands:

+ [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) for Amazon RDS MySQL

+ [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) for Aurora MySQL

Specify either the `--enable-iam-database-authentication` or `--no-enable-iam-database-authentication` option, as appropriate\. 

By default, Amazon RDS modifies the DB instance during the next maintenance window\. If you want to override this and enable IAM DB authentication as soon as possible, use the `--apply-immediately` parameter\. 

The following example shows how to immediately enable IAM authentication for an existing DB instance\.

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --apply-immediately \
    --enable-iam-database-authentication
```

If you are restoring a DB instance or DB cluster, use one of the following AWS CLI commands:

+ `aws rds [restore\-db\-instance\-to\-point\-in\-time](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html)`

+ `aws rds [restore\-db\-instance\-from\-db\-snapshot](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)`

The IAM database authentication setting defaults to that of the source snapshot\. To change this setting, set the `--enable-iam-database-authentication` or `--no-enable-iam-database-authentication` option, as appropriate\.

## Amazon RDS API<a name="UsingWithRDS.IAMDBAuth.Enabling.API"></a>

For a new DB instance or DB cluster, use one of the following API actions:

+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) for Amazon RDS MySQL

+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) for Aurora MySQL

Set the `EnableIAMDatabaseAuthentication` parameter to `true`\.

For an existing DB instance or DB cluster, use one of the following API actions:

+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) for Amazon RDS MySQL

+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) for Aurora MySQL

Set the `EnableIAMDatabaseAuthentication` to `true` to enable IAM authentication, or `false` to disable it\.

If you are restoring a DB instance or DB cluster, use one of the following API actions:

+  [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html)

+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html)

The IAM database authentication setting defaults to that of the source snapshot\. To change this setting, set the `EnableIAMDatabaseAuthentication` to `true` to enable IAM authentication, or `false` to disable it\.