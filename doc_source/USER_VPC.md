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

After you create the VPC, follow these steps to move your DB instance into the VPC\. 
+ [Updating the VPC for a DB Instance](#USER_VPC.VPC2VPC)

The following are some limitations to moving your DB instance into the VPC\. 
+ Moving a Multi\-AZ DB instance not in a VPC into a VPC is not currently supported\.
+ Moving a DB instance with read replicas not in a VPC into a VPC is not currently supported\.

If you move your DB instance into a VPC, and you are using a custom option group with your DB instance, then you need to change the option group that is associated with your DB instance\. Option groups are platform\-specific, and moving to a VPC is a change in platform\. To use a custom option group in this case, assign the default VPC option group to the DB instance, assign an option group that is used by other DB instances in the VPC you are moving to, or create a new option group and assign it to the DB instance\. For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\. 