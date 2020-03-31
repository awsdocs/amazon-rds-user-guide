# Amazon RDS on AWS Outposts \(Preview\)<a name="rds-on-outposts"></a>


|  | 
| --- |
| This is prerelease documentation for a service in preview release\. It is subject to change\. | 

By using AWS Outposts, you can work with the same AWS hardware infrastructure, services, APIs, and tools to build and run your applications on premises as in the cloud\. AWS Outposts is ideal for workloads that need low latency access to on\-premises applications or systems and for workloads that need to store and process data locally\. For more information about AWS Outposts, see [AWS Outposts](https://aws.amazon.com/outposts/)\.

With Amazon RDS on AWS Outposts, you can create AWS\-managed relational databases in your on\-premises data centers\. RDS on Outposts enables you to run RDS databases on AWS Outposts\. You manage RDS on Outposts databases using the same AWS Management Console, AWS CLI, and RDS API that you use for Amazon RDS databases in the cloud\.

**Important**  
Currently, RDS on Outposts is in preview\. Don't use RDS on Outposts for databases running production workloads\. Also, we strongly recommend not putting sensitive data in databases that you use with the RDS on Outposts preview\. You might encounter issues during the preview that cause incorrect results, corrupted data, or both\. Over the duration of the preview, we might introduce breaking changes without advance notice\.

RDS on Outposts supports automated backups of DB instances\. Automated backups enable point\-in\-time recovery\. You can also manually create DB snapshots\. Network connectivity between your Outpost and your AWS Region is required to back up and restore DB instances\. All DB snapshots and transaction logs from an Outpost are stored in the AWS Region\. From the AWS Region, you can restore a DB instance from a DB snapshot to a different Outpost\. For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.

RDS on Outposts supports automated maintenance and upgrades of DB instances\. For more information, see [Maintaining a DB Instance](USER_UpgradeDBInstance.Maintenance.md)\.

RDS on Outposts uses encryption at rest for DB instances and DB snapshots using your AWS KMS key\. For more information about encryption at rest, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.

If network connectivity between your Outpost and its AWS Region is lost, your databases continue to run\. You can't create new databases or take new actions on existing databases\. If an Outpost's capacity becomes unhealthy while you are disconnected, the database isn't automatically replaced\. Additionally, automatic backups don't run if network connectivity is down during the backup window\. If there is a hardware failure in disconnected mode, all reads and writes to your databases are disabled\. They're disabled because the underlying Amazon EBS volumes stop accepting reads and writes to avoid data loss\.

**Note**  
If network connectivity is lost, we recommend that you restore network connectivity as soon as possible for the best RDS on Outposts experience\.

**Topics**
+ [Prerequisites for Amazon RDS on AWS Outposts](#rds-on-outposts.prerequisites)
+ [Limitations for Amazon RDS on AWS Outposts](#rds-on-outposts.limitations)
+ [Supported DB Instance Classes for Amazon RDS on AWS Outposts](#rds-on-outposts.db-instance-classes)
+ [Creating DB Instances for Amazon RDS on AWS Outposts](#rds-on-outposts.creating)

## Prerequisites for Amazon RDS on AWS Outposts<a name="rds-on-outposts.prerequisites"></a>

The following are prerequisites for using Amazon RDS on AWS Outposts:
+ You must have installed and configured AWS Outposts in your on\-premises data center\. For more information about AWS Outposts, see [AWS Outposts](https://aws.amazon.com/outposts/)\.
+ You must have at least one subnet available for RDS on Outposts\. You can use the same subnet for other workloads\.
+ You must have a reliable network connection between your Outpost and an AWS Region\.

## Limitations for Amazon RDS on AWS Outposts<a name="rds-on-outposts.limitations"></a>

The following are limitations for Amazon RDS on AWS Outposts:
+ You can only create DB instances for RDS for MySQL and RDS for PostgreSQL DB instances\. Currently, other DB engines aren't supported\.
+ RDS on Outposts doesn't support use cases that require all data to remain in your data center\.

  RDS on Outposts stores database backups and logs in your AWS Region and sends database metrics to Amazon CloudWatch\.
+ Some Amazon RDS features aren't supported\.

## Supported DB Instance Classes for Amazon RDS on AWS Outposts<a name="rds-on-outposts.db-instance-classes"></a>

Amazon RDS on AWS Outposts supports the following DB instance classes:
+ General Purpose DB instance classes
  + db\.m5\.24xlarge
  + db\.m5\.12xlarge
  + db\.m5\.4xlarge
  + db\.m5\.2xlarge
  + db\.m5\.xlarge
  + db\.m5\.large
+ Memory Optimized DB instance classes
  + db\.r5\.24xlarge
  + db\.r5\.12xlarge
  + db\.r5\.4xlarge
  + db\.r5\.2xlarge
  + db\.r5\.xlarge
  + db\.r5\.large

Only General Purpose SSD storage is supported for RDS on Outposts DB instances\. For more information about DB instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.

## Creating DB Instances for Amazon RDS on AWS Outposts<a name="rds-on-outposts.creating"></a>

Creating an Amazon RDS on AWS Outposts DB instance is similar to creating an Amazon RDS DB instance in the AWS Cloud\. However, you must specify a DB subnet group that is associated with your Outpost\.

An Amazon VPC can span all of the Availability Zones in an AWS Region\. In AWS Outposts, Outposts are extensions of Availability Zones, and you can extend an Amazon VPC in an account to span multiple Availability Zones and associated Outpost locations\. When you configure your Outpost, you associate a subnet group with it to extend your regional Amazon VPC environment to your on\-premises facility\. Outpost instances and related services appear as part of your regional Amazon VPC, similar to an Availability Zone with associated subnets\.

Before you create an RDS on Outposts DB instance, you can create a DB subnet group that includes one subnet that is associated with your Outpost\. When you create an RDS on Outposts DB instance, specify this DB subnet group\.

For information about configuring AWS Outposts, see the [AWS Outposts User Guide](https://docs.aws.amazon.com/outposts/latest/userguide/)\.

**Note**  
The RDS console is the only interface supported for the RDS on Outposts preview\. The AWS CLI and RDS API aren't supported\.

**To create an RDS on Outposts DB instance using the console**

1. Create a DB subnet group with one subnet that is associated with your Outpost\.
**Note**  
To create a DB subnet group for the AWS Cloud, you specify at least two subnets\. However, for an Outpost DB subnet group, you can specify only one subnet\.

   1. Sign in to the AWS Management Console for the preview, and open the Amazon RDS console\.

   1. Choose **Subnet groups**, and then choose **Create DB Subnet Group**\.

      The **Create DB subnet group** page appears\.  
![\[Create DB subnet group page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/create-db-subnet-group.png)

   1. Set the following values for your new DB subnet group:
      + ****Name** –** The name of the DB subnet group
      + ****Description** –** A description for the DB subnet group
      + ****VPC** –** The VPC for which you're creating the DB subnet group

   1. For **Availability Zone**, choose the Availability Zone for your Outpost\.

   1. For **Subnet**, choose the subnet for use by RDS on Outposts\.

   1. Choose **Add subnet**\.

      Your DB subnet group must have only one subnet\.

   1. Choose **Create** to create the DB subnet group\.

1. Choose the Outpost for your DB instance\. 

   The AWS Management Console detects available Outposts that you have configured\. Choose the Outpost with the DB subnet group that you created previously\.  
![\[Creating an RDS on Outposts DB instance page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/create-outpost-db-instance.png)

   Choose the following settings:
   + ****Database location** –** **On\-premises**
   + ****On\-premises creation method** –** **RDS on Outposts**
   + ****Outpost** –** The Outpost that uses the Amazon VPC with the DB subnet group for the DB instance
   + ****Virtual Private Cloud \(VPC\)** –** The Amazon VPC that contains the DB subnet group for the DB instance
   + ****Subnet group** –** The DB subnet group for the DB instance

     You can choose an existing DB subnet group that is associated with the Outpost\. If you didn't create a DB subnet group, you can create a new DB subnet group for the Outpost\. Only one subnet is allowed in the DB subnet group\.

1. For the remaining sections, specify your DB instance settings\.

   For information about each setting when creating a DB instance, see [Settings for DB Instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.

1. Choose **Create database**\. 

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB instance, choose **View credential details**\.  
![\[Master user credentials after automatic password generation.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-credentials.png)

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. To change the master user password after the DB instance is available, modify the DB instance\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. For **Databases**, choose the name of the new DB instance\.

   On the RDS console, the details for the new DB instance appear\. The DB instance has a status of **Creating** until the DB instance is created and ready for use\. When the state changes to **Available**, you can connect to the DB instance\. Depending on the DB instance class and storage allocated, it can take several minutes for the new DB instance to be available\.   
![\[My DB instances details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/create-outpost-launch.png)

   After the DB instance is available, you can manage it the same way that you manage RDS DB instances in the cloud\.