# Creating read replicas for Amazon RDS on AWS Outposts<a name="rds-on-outposts.rr"></a>

Amazon RDS on AWS Outposts uses the MySQL and PostgreSQL DB engines' built\-in replication functionality to create a read replica from a source DB instance\. The source DB instance becomes the primary DB instance\. Updates made to the primary DB instance are asynchronously copied to the read replica\. You can reduce the load on your primary DB instance by routing read queries from your applications to the read replica\. Using read replicas, you can elastically scale out beyond the capacity constraints of a single DB instance for read\-heavy database workloads\.

When you create a read replica from an RDS on Outposts DB instance, the read replica can use a customer\-owned IP address \(CoIP\)\. For more information, see [Customer\-owned IP addresses for Amazon RDS on AWS Outposts](rds-on-outposts.coip.md)\.

Read replicas on RDS on Outposts have the following limitations:
+ You can't create read replicas for RDS for SQL Server on RDS on Outposts DB instances\.
+ Cross\-Region read replicas aren't supported on RDS on Outposts\.
+ Cascading read replicas aren't supported on RDS on Outposts\.
+ The source RDS on Outposts DB instance can't have local backups\. The backup target for the source DB instance must be your AWS Region\.

You can create a read replica from an RDS on Outposts DB instance using the AWS Management Console, AWS CLI, or RDS API\. For more information on read replicas, see [Working with read replicas](USER_ReadRepl.md)\.

## Console<a name="outposts-rr.Console"></a>

**To create a read replica from a source DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to use as the source for a read replica\.

1. For **Actions**, choose **Create read replica**\. 

1. For **DB instance identifier**, enter a name for the read replica\.

1. Specify your settings for **Outposts Connectivity**\. These settings are for the Outpost that uses the virtual private cloud \(VPC\) that has the DB subnet group for your DB instance\. Your VPC must be based on the Amazon VPC service\.

1. Choose your **DB instance class**\. We recommend that you use the same or larger DB instance class and storage type as the source DB instance for the read replica\.

1. For **Multi\-AZ deployment**, choose **Create a standby instance \(recommended for production usage\)** to create a standby DB instance in a different Availability Zone\.

   Creating your read replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\.

1. \(Optional\) Under **Connectivity**, set values for **Subnet Group** and **Availability Zone**\.

   If you specify values for both **Subnet Group** and **Availability Zone**, the read replica is created on an Outpost that is associated with the Availability Zone in the DB subnet group\.

   If you specify a value for **Subnet Group** and **No preference** for **Availability Zone**, the read replica is created on a random Outpost in the DB subnet group\.

1. For **AWS KMS key**, choose the AWS KMS key identifier of the KMS key\.

    The read replica must be encrypted\.

1. Choose other options as needed\.

1. Choose **Create read replica**\.

After the read replica is created, you can see it on the **Databases** page in the RDS console\. It shows **Replica** in the **Role** column\.

## AWS CLI<a name="outposts-rr.CLI"></a>

To create a read replica from a source MySQL or PostgreSQL DB instance, use the AWS CLI command [create\-db\-instance\-read\-replica](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html)\.  

You can control where the read replica is created by specifying the `--db-subnet-group-name` and `--availability-zone` options:
+ If you specify both the `--db-subnet-group-name` and `--availability-zone` options, the read replica is created on an Outpost that is associated with the Availability Zone in the DB subnet group\.
+ If you specify the `--db-subnet-group-name` option and don't specify the `--availability-zone` option, the read replica is created on a random Outpost in the DB subnet group\.
+ If you don't specify either option, the read replica is created on the same Outpost as the source RDS on Outposts DB instance\.

The following example creates a replica and specifies the location of the read replica by including `--db-subnet-group-name` and `--availability-zone` options\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance-read-replica \
    --db-instance-identifier myreadreplica \
    --source-db-instance-identifier mydbinstance \
    --db-subnet-group-name myoutpostdbsubnetgr \
    --availability-zone us-west-2a
```
For Windows:  

```
aws rds create-db-instance-read-replica ^
    --db-instance-identifier myreadreplica ^
    --source-db-instance-identifier mydbinstance ^
    --db-subnet-group-name myoutpostdbsubnetgr ^
    --availability-zone us-west-2a
```

## RDS API<a name="outposts-rr.API"></a>

To create a read replica from a source MySQL or PostgreSQL DB instance, call the Amazon RDS API [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) operation with the following required parameters:
+ `DBInstanceIdentifier`
+ `SourceDBInstanceIdentifier`

You can control where the read replica is created by specifying the `DBSubnetGroupName` and `AvailabilityZone` parameters:
+ If you specify both the `DBSubnetGroupName` and `AvailabilityZone` parameters, the read replica is created on an Outpost that is associated with the Availability Zone in the DB subnet group\.
+ If you specify the `DBSubnetGroupName` parameter and don't specify the `AvailabilityZone` parameter, the read replica is created on a random Outpost in the DB subnet group\.
+ If you don't specify either parameter, the read replica is created on the same Outpost as the source RDS on Outposts DB instance\.