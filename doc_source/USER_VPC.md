# Amazon Virtual Private Cloud VPCs and Amazon RDS<a name="USER_VPC"></a>

There are two Amazon Elastic Compute Cloud \(EC2\) platforms that host Amazon RDS DB instances, *EC2\-VPC* and *EC2\-Classic*\. Amazon Virtual Private Cloud \(Amazon VPC\) enables you to launch AWS resources, such as Amazon RDS DB instances, into a virtual private cloud \(VPC\)\. 

When you use an Amazon VPC, you have control over your virtual networking environment: you can choose your own IP address range, create subnets, and configure routing and access control lists\. The basic functionality of Amazon RDS is the same whether your DB instance is running in an Amazon VPC or not: Amazon RDS manages backups, software patching, automatic failure detection, and recovery\.  There is no additional cost to run your DB instance in an Amazon VPC\. 

![\[VPC platform\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/GS-VPC-network.png)

Accounts that support only the *EC2\-VPC* platform have a default VPC\. All new DB instances are created in the default VPC unless you specify otherwise\. If you are a new Amazon RDS customer, if you have never created a DB instance before, or if you are creating a DB instance in an AWS Region you have not used before, you are most likely on the *EC2\-VPC* platform and have a default VPC\. 

Some legacy DB instances on the *EC2\-Classic* platform are not in a VPC\. The legacy *EC2\-Classic* platform does not have a default VPC, but as is true for either platform, you can create your own VPC and specify that a DB instance be located in that VPC\. 

**Topics**
+ [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)
+ [Scenarios for Accessing a DB Instance in a VPC](USER_VPC.Scenarios.md)
+ [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)
+ [Updating the VPC for a DB Instance](#USER_VPC.VPC2VPC)
+ [Tutorial: Create an Amazon VPC for Use with a DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)

This documentation only discusses VPC functionality relevant to Amazon RDS DB instances\. For more information about Amazon VPC, see [Amazon VPC Getting Started Guide](https://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/) and [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\. For information about using a network address translation \(NAT\) gateway, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon Virtual Private Cloud User Guide*\. 

## Updating the VPC for a DB Instance<a name="USER_VPC.VPC2VPC"></a>

You can use the AWS Management Console to move your DB instance to a different VPC\. 

For information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. In the **Network & Security** section of the modify page, shown following, enter the new subnet group for **Subnet group**\. The new subnet group must be a subnet group in a new VPC\.  

![\[Modify DB Instance panel Subnet Group section\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EC2-VPC.png)

### Moving a DB Instance Not in a VPC into a VPC<a name="USER_VPC.Non-VPC2VPC"></a>

Some legacy DB instances on the EC2\-Classic platform are not in a VPC\. If your DB instance is not in a VPC, you can use the AWS Management Console to easily move your DB instance into a VPC\. Before you can move a DB instance not in a VPC, into a VPC, you must create the VPC\. 

Follow these steps to create a VPC for your DB instance\. 
+ [Step 1: Create a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreatingVPC)
+ [Step 2: Add Subnets to the VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.AddingSubnets)
+  [Step 3: Create a DB Subnet Group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateDBSubnetGroup)
+  [Step 4: Create a VPC Security Group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)

Each DB subnet group must include at least the Availability Zones in which the DB instance is located\.

After you create the VPC, follow these steps to move your DB instance into the VPC\. 
+ [Updating the VPC for a DB Instance](#USER_VPC.VPC2VPC)

We highly recommend that you create a backup of your DB instance immediately before the migration\. Doing so ensures that you can restore the data if the migration fails\. For more information, see [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)\.

The following are some limitations to moving your DB instance into the VPC\. 
+ **Previous generation DB instance classes** – Previous generation DB instance classes might not be supported on the VPC platform\. When moving a DB instance to a VPC, choose a db\.m3 or db\.r3 DB instance class\. After you move the DB instance to a VPC, you can scale the DB instance to use a later DB instance class\. For a full list of VPC supported instance classes, see [Amazon RDS Instance Types](https://aws.amazon.com/rds/instance-types/)\. 
+ **Multi\-AZ** – Moving a Multi\-AZ DB instance not in a VPC into a VPC is not currently supported\. To move your DB instance to a VPC, first modify the DB instance so that it is a single\-AZ deployment\. Change the **Multi\-AZ deployment** setting to **No**\. After you move the DB instance to a VPC, modify it again to make it a Multi\-AZ deployment\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 
+ **Read replicas** – Moving a DB instance with read replicas not in a VPC into a VPC is not currently supported\. To move your DB instance to a VPC, first delete all of its read replicas\. After you move the DB instance to a VPC, recreate the read replicas\. For more information, see [Working with Read Replicas](USER_ReadRepl.md)\.
+ **Option groups** – If you move your DB instance to a VPC, and the DB instance is using a custom option group, change the option group that is associated with your DB instance\. Option groups are platform\-specific, and moving to a VPC is a change in platform\. To use a custom option group in this case, assign the default VPC option group to the DB instance, assign an option group that is used by other DB instances in the VPC you are moving to, or create a new option group and assign it to the DB instance\. For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.

#### Alternatives for Moving a DB Instance Not in a VPC into a VPC with Minimal Downtime<a name="USER_VPC.Non-VPC2VPC.Minimal-Downtime"></a>

Using the following alternatives, you can move a DB instance not in a VPC into a VPC with minimal downtime\. These alternatives cause minimum disruption to the source DB instance and allow it to serve user traffic during the migration\. However, the time required to migrate to a VPC will vary based on the database size and the live workload characteristics\. 
+ **AWS Database Migration Service \(AWS DMS\)** – AWS DMS enables the live migration of data while keeping the source DB instance fully operational, but it replicates only a limited set of DDL statements\. AWS DMS doesn't propagate items such as indexes, users, privileges, stored procedures, and other database changes not directly related to table data\. In addition, AWS DMS doesn't automatically use RDS snapshots for the initial DB instance creation, which can increase migration time\. For more information, see [AWS Database Migration Service](https://aws.amazon.com/dms/)\. 
+ **DB snapshot restore or point\-in\-time recovery** – You can move a DB instance to a VPC by restoring a snapshot of the DB instance or by restoring a DB instance to a point in time\. For more information, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md) and [Restoring a DB Instance to a Specified Time](USER_PIT.md)\. 