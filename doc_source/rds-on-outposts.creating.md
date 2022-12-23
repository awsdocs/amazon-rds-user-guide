# Creating DB instances for Amazon RDS on AWS Outposts<a name="rds-on-outposts.creating"></a>

Creating an Amazon RDS on AWS Outposts DB instance is similar to creating an Amazon RDS DB instance in the AWS Cloud\. However, make sure that you specify a DB subnet group that is associated with your Outpost\.

A virtual private cloud \(VPC\) based on the Amazon VPC service can span all of the Availability Zones in an AWS Region\. You can extend any VPC in the AWS Region to your Outpost by adding an Outpost subnet\. To add an Outpost subnet to a VPC, specify the Amazon Resource Name \(ARN\) of the Outpost when you create the subnet\.

Before you create an RDS on Outposts DB instance, you can create a DB subnet group that includes one subnet that is associated with your Outpost\. When you create an RDS on Outposts DB instance, specify this DB subnet group\. You can also choose to create a new DB subnet group when you create your DB instance\.

For information about configuring AWS Outposts, see the [AWS Outposts User Guide](https://docs.aws.amazon.com/outposts/latest/userguide/)\.

## Console<a name="rds-on-outposts.creating.console"></a>

### Creating a DB subnet group<a name="rds-on-outposts.creating.console.subnet"></a>

Create a DB subnet group with one subnet that is associated with your Outpost\.

You can also create a new DB subnet group for the Outpost when you create your DB instance\. If you want to do so, then skip this procedure\.

**Note**  
To create a DB subnet group for the AWS Cloud, specify at least two subnets\.

**To create a DB subnet group for your Outpost**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region where you want to create the DB subnet group\.

1. Choose **Subnet groups**, and then choose **Create DB Subnet Group**\.

   The **Create DB subnet group** page appears\.  
![\[Create DB subnet group page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/create-db-subnet-group.png)

1. For **Name**, choose the name of the DB subnet group\.

1. For **Description**, choose a description for the DB subnet group\.

1. For **VPC**, choose the VPC that you're creating the DB subnet group for\.

1. For **Availability Zones**, choose the Availability Zone for your Outpost\.

1. For **Subnets**, choose the subnet for use by RDS on Outposts\.

1. Choose **Create** to create the DB subnet group\.

### Creating the RDS on Outposts DB instance<a name="rds-on-outposts.creating.console.DB"></a>

Create the DB instance, and choose the Outpost for your DB instance\.

**To create an RDS on Outposts DB instance using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region where the Outpost on which you want to create the DB instance is attached\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

   The AWS Management Console detects available Outposts that you have configured and presents the **On\-premises** option in the **Database location** section\.
**Note**  
If you haven't configured any Outposts, either the **Database location** section doesn't appear or the **RDS on Outposts** option isn't available in the **Choose an on\-premises creation method** section\.

1. For **Database location**, choose **On\-premises**\.

1. For **On\-premises creation method**, choose **RDS on Outposts**\.

1. Specify your settings for **Outposts Connectivity**\. These settings are for the Outpost that uses the VPC that has the DB subnet group for your DB instance\. Your VPC must be based on the Amazon VPC service\.

   1. For **Virtual Private Cloud \(VPC\)**, choose the VPC that contains the DB subnet group for your DB instance\.

   1. For **VPC security group**, choose the Amazon VPC security group for your DB instance\.

   1. For **DB subnet group**, choose the DB subnet group for your DB instance\.

      You can choose an existing DB subnet group that's associated with the Outpost—for example, if you performed the procedure in [Creating a DB subnet group](#rds-on-outposts.creating.console.subnet)\.

      You can also create a new DB subnet group for the Outpost\.

1. For **Multi\-AZ deployment**, choose **Create a standby instance \(recommended for production usage\)** to create a standby DB instance in another Outpost\.
**Note**  
This option isn't available for Microsoft SQL Server\.  
If you choose to create a Multi\-AZ deployment, you can't store backups on your Outpost\.

1. Under **Backup**, do the following:

   1. For **Backup target**, choose one of the following:
      + **AWS Cloud** to store automated backups and manual snapshots in the parent AWS Region\.
      + **Outposts \(on\-premises\)** to create local backups\.
**Note**  
To store backups on your Outpost, your Outpost must have Amazon S3 capability\. For more information, see [Amazon S3 on Outposts](https://aws.amazon.com/s3/outposts/)\.  
Local backups aren't supported for Multi\-AZ deployments or read replicas\.

   1. Choose **Enable automated backups** to create point\-in\-time snapshots of your DB instance\.

      If you turn on automated backups, then you can choose values for **Backup retention period** and **Backup window**, or leave the default values\.

1. Specify other DB instance settings as needed\.

   For information about each setting when creating a DB instance, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.

1. Choose **Create database**\.

   The **Databases** page appears\. A banner tells you that your DB instance is being created, and displays the **View credential details** button\.

### Viewing DB instance details<a name="rds-on-outposts.creating.console.view"></a>

After you create your DB instance, you can view credentials and other details for it\.

**To view DB instance details**

1. To view the master user name and password for the DB instance, choose **View credential details** on the **Databases** page\.

   You can connect to the DB instance as the master user by using these credentials\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. To change the master user password after the DB instance is available, modify the DB instance\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

1. Choose the name of the new DB instance on the **Databases** page\.

   On the RDS console, the details for the new DB instance appear\. The DB instance has a status of **Creating** until the DB instance is created and ready for use\. When the state changes to **Available**, you can connect to the DB instance\. Depending on the DB instance class and storage allocated, it can take several minutes for the new DB instance to be available\.   
![\[My DB instances details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/create-outpost-launch.png)

   After the DB instance is available, you can manage it the same way that you manage RDS DB instances in the AWS Cloud\.

## AWS CLI<a name="rds-on-outposts.creating.cli"></a>

Before you create a new DB instance in an Outpost with the AWS CLI, first create a DB subnet group for use by RDS on Outposts\.

**To create a DB subnet group for your Outpost**
+ Use the [create\-db\-subnet\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-subnet-group.html) command\. For `--subnet-ids`, specify the subnet group in the Outpost for use by RDS on Outposts\.

  For Linux, macOS, or Unix:

  ```
  1. aws rds create-db-subnet-group \
  2.     --db-subnet-group-name myoutpostdbsubnetgr \
  3.     --db-subnet-group-description "DB subnet group for RDS on Outposts" \
  4.     --subnet-ids subnet-abc123
  ```

  For Windows:

  ```
  1. aws rds create-db-subnet-group ^
  2.     --db-subnet-group-name myoutpostdbsubnetgr ^
  3.     --db-subnet-group-description "DB subnet group for RDS on Outposts" ^
  4.     --subnet-ids subnet-abc123
  ```

**To create an RDS on Outposts DB instance using the AWS CLI**
+ Use the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command\. Specify an Availability Zone for the Outpost, an Amazon VPC security group associated with the Outpost, and the DB subnet group you created for the Outpost\. You can include the following options:
  + `--db-instance-identifier`
  + `--db-instance-class`
  + `--engine` – The database engine\. Use one of the following values:
    + MySQL – Specify `mysql`\.
    + PostgreSQL – Specify `postgres`\.
    + Microsoft SQL Server – Specify `sqlserver-ee`, `sqlserver-se`, or `sqlserver-web`\.
  + `--availability-zone`
  + `--vpc-security-group-ids`
  + `--db-subnet-group-name`
  + `--allocated-storage`
  + `--max-allocated-storage`
  + `--master-username`
  + `--master-user-password`
  + `--multi-az | --no-multi-az` – \(Optional\) Whether to create a standby DB instance in a different Availability Zone\. The default is `--no-multi-az`\.

    The `--multi-az` option isn't available for SQL Server\.
  + `--backup-retention-period`
  + `--backup-target` – \(Optional\) Where to store automated backups and manual snapshots\. Use one of the following values:
    + `outposts` – Store them locally on your Outpost\.
    + `region` – Store them in the parent AWS Region\. This is the default value\.

    If you use the `--multi-az` option, you can't use `outposts` for `--backup-target`\. In addition, the DB instance can't have read replicas if you use `outposts` for `--backup-target`\.
  + `--storage-encrypted`
  + `--kms-key-id`

**Example**  
The following example creates a MySQL DB instance named `myoutpostdbinstance` with backups stored on your Outpost\.  
For Linux, macOS, or Unix:  

```
 1. aws rds create-db-instance \
 2.     --db-instance-identifier myoutpostdbinstance \
 3.     --engine-version 8.0.17 \
 4.     --db-instance-class db.m5.large \
 5.     --engine mysql \
 6.     --availability-zone us-east-1d \
 7.     --vpc-security-group-ids outpost-sg \
 8.     --db-subnet-group-name myoutpostdbsubnetgr \
 9.     --allocated-storage 100 \
10.     --max-allocated-storage 1000 \
11.     --master-username masterawsuser \
12.     --master-user-password masteruserpassword \
13.     --backup-retention-period 3 \
14.     --backup-target outposts \
15.     --storage-encrypted \
16.     --kms-key-id mykey
```
For Windows:  

```
 1. aws rds create-db-instance ^
 2.     --db-instance-identifier myoutpostdbinstance ^
 3.     --engine-version 8.0.17 ^
 4.     --db-instance-class db.m5.large ^
 5.     --engine mysql ^
 6.     --availability-zone us-east-1d ^
 7.     --vpc-security-group-ids outpost-sg ^
 8.     --db-subnet-group-name myoutpostdbsubnetgr ^
 9.     --allocated-storage 100 ^
10.     --max-allocated-storage 1000 ^
11.     --master-username masterawsuser ^
12.     --master-user-password masteruserpassword ^
13.     --backup-retention-period 3 ^
14.     --backup-target outposts ^
15.     --storage-encrypted ^
16.     --kms-key-id mykey
```

For information about each setting when creating a DB instance, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.

## RDS API<a name="rds-on-outposts.creating.api"></a>

To create a new DB instance in an Outpost with the RDS API, first create a DB subnet group for use by RDS on Outposts by calling the [CreateDBSubnetGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBSubnetGroup.html) operation\. For `SubnetIds`, specify the subnet group in the Outpost for use by RDS on Outposts\. 

Next, call the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) operation with the following parameters\. Specify an Availability Zone for the Outpost, an Amazon VPC security group associated with the Outpost, and the DB subnet group you created for the Outpost\. 
+ `AllocatedStorage`
+ `AvailabilityZone`
+ `BackupRetentionPeriod`
+ `BackupTarget`

  If you are creating a Multi\-AZ DB instance deployment, you can't use `outposts` for `BackupTarget`\. In addition, the DB instance can't have read replicas if you use `outposts` for `BackupTarget`\.
+ `DBInstanceClass`
+ `DBInstanceIdentifier`
+ `VpcSecurityGroupIds`
+ `DBSubnetGroupName`
+ `Engine`
+ `EngineVersion`
+ `MasterUsername`
+ `MasterUserPassword`
+ `MaxAllocatedStorage` \(optional\)
+ `MultiAZ` \(optional\)
+ `StorageEncrypted`
+ `KmsKeyID`

For information about each setting when creating a DB instance, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.