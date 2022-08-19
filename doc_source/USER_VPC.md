# Amazon VPC VPCs and Amazon RDS<a name="USER_VPC"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) makes it possible for you to launch AWS resources, such as Amazon RDS DB instances, into a virtual private cloud \(VPC\)\. 

When you use a VPC, you have control over your virtual networking environment\. You can choose your own IP address range, create subnets, and configure routing and access control lists\. There is no additional cost to run your DB instance in a VPC\. 

Accounts have a default VPC\. All new DB instances are created in the default VPC unless you specify otherwise\.

**Topics**
+ [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)
+ [Updating the VPC for a DB instance](USER_VPC.VPC2VPC.md)
+ [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)
+ [Tutorial: Create a VPC for use with a DB instance \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)
+ [Tutorial: Create a VPC for use with a DB instance \(dual\-stack mode\)](CHAP_Tutorials.CreateVPCDualStack.md)
+ [Moving a DB instance not in a VPC into a VPC](USER_VPC.Non-VPC2VPC.md)

Following, you can find a discussion about VPC functionality relevant to Amazon RDS DB instances\. For more information about Amazon VPC, see [Amazon VPC Getting Started Guide](https://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/) and [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\.