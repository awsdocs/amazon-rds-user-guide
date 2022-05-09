# Amazon Virtual Private Cloud VPCs and Amazon RDS<a name="USER_VPC"></a>

There are two Amazon Elastic Compute Cloud \(EC2\) platforms that host Amazon RDS DB instances, *EC2\-VPC* and *EC2\-Classic*\. Amazon Virtual Private Cloud \(Amazon VPC\) enables you to launch AWS resources, such as Amazon RDS DB instances, into a virtual private cloud \(VPC\)\. 

When you use an Amazon VPC, you have control over your virtual networking environment: you can choose your own IP address range, create subnets, and configure routing and access control lists\. There is no additional cost to run your DB instance in an Amazon VPC\. 

Accounts that support only the *EC2\-VPC* platform have a default VPC\. All new DB instances are created in the default VPC unless you specify otherwise\. If you are a new Amazon RDS customer, if you have never created a DB instance before, or if you are creating a DB instance in an AWS Region you have not used before, you are most likely on the *EC2\-VPC* platform and have a default VPC\. 

Some legacy DB instances on the *EC2\-Classic* platform are not in a VPC\. The legacy *EC2\-Classic* platform does not have a default VPC, but as is true for either platform, you can create your own VPC and specify that a DB instance be located in that VPC\. For more information, see [Working with a DB instance that is not in a VPC](USER_VPC.WorkingWithRDSInstanceNotinaVPC.md)\. 

**Topics**
+ [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)
+ [Updating the VPC for a DB instance](USER_VPC.VPC2VPC.md)
+ [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)
+ [Tutorial: Create an Amazon VPC for use with a DB instance \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)
+ [Tutorial: Create a virtual private cloud \(VPC\) for use with a DB instance \(dual\-stack mode\)](CHAP_Tutorials.CreateVPCDualStack.md)
+ [Working with a DB instance that is not in a VPC](USER_VPC.WorkingWithRDSInstanceNotinaVPC.md)

This documentation only discusses VPC functionality relevant to Amazon RDS DB instances\. For more information about Amazon VPC, see [Amazon VPC Getting Started Guide](https://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/) and [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\. For information about using a network address translation \(NAT\) gateway, see [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon Virtual Private Cloud User Guide*\. 