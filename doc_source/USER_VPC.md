# Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS<a name="USER_VPC"></a>

There are two Amazon Elastic Compute Cloud \(EC2\) platforms that host Amazon RDS DB instances, *EC2\-VPC* and *EC2\-Classic*\. Amazon Virtual Private Cloud \(Amazon VPC\) lets you launch AWS resources, such as Amazon RDS DB instances, into a virtual private cloud \(VPC\)\. 

When you use an Amazon VPC, you have control over your virtual networking environment: you can select your own IP address range, create subnets, and configure routing and access control lists\. The basic functionality of Amazon RDS is the same whether your DB instance is running in an Amazon VPC or not: Amazon RDS manages backups, software patching, automatic failure detection, and recovery\. There is no additional cost to run your DB instance in Amazon VPC\. 

![\[VPC platform\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/GS-VPC network.png)

Accounts that support only the *EC2\-VPC* platform have a default VPC\. All new DB instances are created in the default VPC unless you specify otherwise\. If you are a new Amazon RDS customer, if you have never created a DB instance before, or if you are creating a DB instance in a region you have not used before, you are most likely on the *EC2\-VPC* platform and have a default VPC\. 

Some legacy DB instances on the *EC2\-Classic* platform are not in a VPC\. The legacy *EC2\-Classic* platform does not have a default VPC, but as is true for either platform, you can create your own VPC and specify that a DB instance be located in that VPC\. 

**Topics**
+ [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)
+ [Scenarios for Accessing a DB Instance in a VPC](USER_VPC.Scenarios.md)
+ [Working with an Amazon RDS DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)
+ [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)

This documentation only discusses VPC functionality relevant to Amazon RDS DB instances\. For more information about Amazon VPC, see [Amazon VPC Getting Started Guide](http://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/) and [Amazon VPC User Guide](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/)\. 