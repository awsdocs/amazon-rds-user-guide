# Moving a DB instance not in a VPC into a VPC<a name="USER_VPC.Non-VPC2VPC"></a>

Some legacy DB instances on the EC2\-Classic platform are not in a VPC\. If your DB instance is not in a VPC, you can use the AWS Management Console to easily move your DB instance into a VPC\. Before you can move a DB instance not in a VPC, into a VPC, you must create the VPC\. 


|  | 
| --- |
| EC2\-Classic was retired on August 15, 2022\. If you haven't migrated from EC2\-Classic to a VPC, we recommend that you migrate as soon as possible\. For more information, see [ Migrate from EC2\-Classic to a VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-migrate.html) in the Amazon EC2 User Guide and the blog [EC2\-Classic Networking is Retiring – Here’s How to Prepare](http://aws.amazon.com/blogs/aws/ec2-classic-is-retiring-heres-how-to-prepare/)\. | 

**Important**  
If you are a new Amazon RDS customer, if you have never created a DB instance before, or if you are creating a DB instance in an AWS Region you have not used before, in almost all cases you are on the *EC2\-VPC* platform and have a default VPC\. For information about working with DB instances in a VPC, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\.

Follow these steps to create a VPC for your DB instance\. 
+ [Step 1: Create a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreatingVPC)
+  [Step 2: Create a DB subnet group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateDBSubnetGroup)
+  [Step 3: Create a VPC security group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)

After you create the VPC, follow these steps to move your DB instance into the VPC\. 
+ [Updating the VPC for a DB instance](USER_VPC.VPC2VPC.md)

We highly recommend that you create a backup of your DB instance immediately before the migration\. Doing so ensures that you can restore the data if the migration fails\. For more information, see [Backing up and restoring an Amazon RDS DB instance](CHAP_CommonTasks.BackupRestore.md)\.

The following are some limitations to moving your DB instance into the VPC\. 
+ **Previous generation DB instance classes** – Previous generation DB instance classes might not be supported on the VPC platform\. When moving a DB instance to a VPC, choose a db\.m3 or db\.r3 DB instance class\. After you move the DB instance to a VPC, you can scale the DB instance to use a later DB instance class\. For a full list of VPC supported instance classes, see [Amazon RDS instance types](http://aws.amazon.com/rds/instance-types/)\. 
+ **Multi\-AZ** – Moving a Multi\-AZ DB instance not in a VPC into a VPC is not currently supported\. To move your DB instance to a VPC, first modify the DB instance so that it is a single\-AZ deployment\. Change the **Multi\-AZ deployment** setting to **No**\. After you move the DB instance to a VPC, modify it again to make it a Multi\-AZ deployment\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 
+ **Read replicas** – Moving a DB instance with read replicas not in a VPC into a VPC is not currently supported\. To move your DB instance to a VPC, first delete all of its read replicas\. After you move the DB instance to a VPC, recreate the read replicas\. For more information, see [Working with read replicas](USER_ReadRepl.md)\.
+ **Option groups** – If you move your DB instance to a VPC, and the DB instance is using a custom option group, change the option group that is associated with your DB instance\. Option groups are platform\-specific, and moving to a VPC is a change in platform\. To use a custom option group in this case, assign the default VPC option group to the DB instance, assign an option group that is used by other DB instances in the VPC you are moving to, or create a new option group and assign it to the DB instance\. For more information, see [Working with option groups](USER_WorkingWithOptionGroups.md)\.

## Alternatives for moving a DB instance not in a VPC into a VPC with minimal downtime<a name="USER_VPC.Non-VPC2VPC.Minimal-Downtime"></a>

Using the following alternatives, you can move a DB instance not in a VPC into a VPC with minimal downtime\. These alternatives cause minimum disruption to the source DB instance and allow it to serve user traffic during the migration\. However, the time required to migrate to a VPC will vary based on the database size and the live workload characteristics\. 
+ **AWS Database Migration Service \(AWS DMS\)** – AWS DMS enables the live migration of data while keeping the source DB instance fully operational, but it replicates only a limited set of DDL statements\. AWS DMS doesn't propagate items such as indexes, users, privileges, stored procedures, and other database changes not directly related to table data\. In addition, AWS DMS doesn't automatically use RDS snapshots for the initial DB instance creation, which can increase migration time\. For more information, see [AWS Database Migration Service](http://aws.amazon.com/dms/)\. 
+ **DB snapshot restore or point\-in\-time recovery** – You can move a DB instance to a VPC by restoring a snapshot of the DB instance or by restoring a DB instance to a point in time\. For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md) and [Restoring a DB instance to a specified time](USER_PIT.md)\. 