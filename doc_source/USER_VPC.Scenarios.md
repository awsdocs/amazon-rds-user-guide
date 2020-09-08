# Scenarios for Accessing a DB Instance in a VPC<a name="USER_VPC.Scenarios"></a>

Amazon RDS supports the following scenarios for accessing a DB instance:


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.Scenarios.html)

## A DB Instance in a VPC Accessed by an EC2 Instance in the Same VPC<a name="USER_VPC.Scenario1"></a>

A common use of a DB instance in a VPC is to share data with an application server that is running in an EC2 instance in the same VPC\. This is the user scenario created if you use AWS Elastic Beanstalk to create an EC2 instance and a DB instance in the same VPC\. 

The following diagram shows this scenario\.

![\[One VPC Scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/con-VPC-sec-grp.png)

The simplest way to manage access between EC2 instances and DB instances in the same VPC is to do the following:
+ Create a VPC security group for your DB instances to be in\. This security group can be used to restrict access to the DB instances\. For example, you can create a custom rule for this security group that allows TCP access using the port you assigned to the DB instance when you created it and an IP address you use to access the DB instance for development or other purposes\.
+ Create a VPC security group for your EC2 instances \(web servers and clients\) to be in\. This security group can, if needed, allow access to the EC2 instance from the internet by using the VPC's routing table\. For example, you can set rules on this security group to allow TCP access to the EC2 instance over port 22\.
+ Create custom rules in the security group for your DB instances that allow connections from the security group you created for your EC2 instances\. This would allow any member of the security group to access the DB instances\.

For a tutorial that shows you how to create a VPC with both public and private subnets for this scenario, see [Tutorial: Create an Amazon VPC for Use with a DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. 

**To create a rule in a VPC security group that allows connections from another security group, do the following:**

1.  Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\. 

1.  In the navigation pane, choose **Security Groups**\. 

1. Choose or create a security group for which you want to allow access to members of another security group\. In the scenario preceding, this is the security group that you use for your DB instances\. Choose the **Inbound Rules** tab, and then choose **Edit rule**\.

1. On the **Edit inbound rules** page, choose **Add Rule**\.

1. From **Type**, choose the entry that corresponds to the port you used when you created your DB instance, such as **MYSQL/Aurora**\.

1. In the **Source** box, start typing the ID of the security group, which lists the matching security groups\. Choose the security group with members that you want to have access to the resources protected by this security group\. In the scenario preceding, this is the security group that you use for your EC2 instance\.

1. If required, repeat the steps for the TCP protocol by creating a rule with **All TCP** as the **Type** and your security group in the **Source** box\. If you intend to use the UDP protocol, create a rule with **All UDP** as the **Type** and your security group in the **Source** box\.

1. Choose **Save** when you are done\.

The following screen shows several inbound rules\.

![\[adding a security group to another security group's rules\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/con-vpc-add-sg-rule.png)

## A DB Instance in a VPC Accessed by an EC2 Instance in a Different VPC<a name="USER_VPC.Scenario3"></a>

 When your DB instance is in a different VPC from the EC2 instance you are using to access it, you can use VPC peering to access the DB instance\.

The following diagram shows this scenario\. 

![\[A DB Instance in a VPC Accessed by an EC2 Instance in a Different VPC\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/RDSVPC2EC2VPC.png)

A VPC peering connection is a networking connection between two VPCs that enables you to route traffic between them using private IP addresses\. Instances in either VPC can communicate with each other as if they are within the same network\. You can create a VPC peering connection between your own VPCs, with a VPC in another AWS account, or with a VPC in a different AWS Region\. To learn more about VPC peering, see [VPC Peering](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-peering.html) in the *Amazon Virtual Private Cloud User Guide*\. 

## A DB Instance in a VPC Accessed by an EC2 Instance Not in a VPC<a name="USER_VPC.ClassicLink"></a>

You can communicate between an Amazon RDS DB instance that is in a VPC and an EC2 instance that is not in an Amazon VPC by using *ClassicLink*\. When you use Classic Link, an application on the EC2 instance can connect to the DB instance by using the endpoint for the DB instance\. ClassicLink is available at no charge\. 

The following diagram shows this scenario\. 

![\[A DB Instance in a VPC Accessed by an EC2 Instance Not in a VPC\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ClassicLink.png)

Using ClassicLink, you can connect an EC2 instance to a logically isolated database where you define the IP address range and control the access control lists \(ACLs\) to manage network traffic\. You don't have to use public IP addresses or tunneling to communicate with the DB instance in the VPC\. This arrangement provides you with higher throughput and lower latency connectivity for inter\-instance communications\. 

**To enable ClassicLink between a DB instance in a VPC and an EC2 instance not in a VPC**

1.  Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\. 

1.  In the navigation pane, choose **Your VPCs**\. 

1.  Choose the VPC used by the DB instance\. 

1.  In **Actions**, choose **Enable ClassicLink**\. In the confirmation dialog box, choose **Yes, Enable**\. 

1.  On the EC2 console, choose the EC2 instance you want to connect to the DB instance in the VPC\. 

1.  In **Actions**, choose **ClassicLink**, and then choose **Link to VPC**\. 

1.  On the **Link to VPC** page, choose the security group you want to use, and then choose **Link to VPC**\. 

**Note**  
 The ClassicLink features are only visible in the consoles for accounts and regions that support EC2\-Classic\. For more information, see [ ClassicLink](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html) in the *Amazon EC2 User Guide for Linux Instances\.* 

## A DB Instance in a VPC Accessed by a Client Application Through the Internet<a name="USER_VPC.Scenario4"></a>

To access a DB instance in a VPC from a client application through the internet, you configure a VPC with a single public subnet, and an internet gateway to enable communication over the internet\. 

The following diagram shows this scenario\. 

![\[A DB Instance in a VPC Accessed by a Client Application Through the internet\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/GS-VPC-network.png)

We recommend the following configuration:
+ A VPC of size /16 \(for example CIDR: 10\.0\.0\.0/16\)\. This size provides 65,536 private IP addresses\.
+ A subnet of size /24 \(for example CIDR: 10\.0\.0\.0/24\)\. This size provides 256 private IP addresses\.
+ An Amazon RDS DB instance that is associated with the VPC and the subnet\. Amazon RDS assigns an IP address within the subnet to your DB instance\.
+ An internet gateway which connects the VPC to the internet and to other AWS products\.
+ A security group associated with the DB instance\. The security group's inbound rules allow your client application to access to your DB instance\.

For information about creating a DB instance in a VPC, see [Creating a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.InstanceInVPC)\.

## A DB Instance Not in a VPC Accessed by an EC2 Instance in a VPC<a name="USER_VPC.Scenario5"></a>

In the case where you have an EC2 instance in a VPC and an RDS DB instance not in a VPC, you can connect them over the public internet\. 

The following diagram shows this scenario\. 

![\[A DB Instance not in a VPC Accessed by an EC2 Instance in a VPC\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/legacyRDS2EC2VPC.png)

**Note**  
*ClassicLink*, as described in [A DB Instance in a VPC Accessed by an EC2 Instance Not in a VPC](#USER_VPC.ClassicLink), is not available for this scenario\. 

To connect your DB instance and your EC2 instance over the public internet, do the following:
+ Ensure that the EC2 instance is in a public subnet in the VPC\.
+ Ensure that the RDS DB instance was marked as publicly accessible\.
+ A note about network ACLs here\. A network ACL is like a firewall for your entire subnet\. Therefore, all instances in that subnet are subject to network ACL rules\. By default, network ACLs allow all traffic and you generally don't need to worry about them, unless you particularly want to add rules as an extra layer of security\. A security group, on the other hand, is associated with individual instances, and you do need to worry about security group rules\.
+ Add the necessary ingress rules to the DB security group for the RDS DB instance\.

  An ingress rule specifies a network port and a CIDR/IP range\. For example, you can add an ingress rule that allows port 3306 to connect to a MySQL RDS DB instance, and a CIDR/IP range of `203.0.113.25/32`\. For more information, see [Authorizing Network Access to a DB Security Group from an IP Range](USER_WorkingWithSecurityGroups.md#USER_WorkingWithSecurityGroups.Authorizing)\.

**Note**  
If you are interested in moving an existing DB instance into a VPC, you can use the AWS Management Console to do it easily\. For more information\. see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\. 

## A DB Instance Not in a VPC Accessed by an EC2 Instance Not in a VPC<a name="USER_VPC.Scenario7"></a>

When neither your DB instance nor an application on an EC2 instance are in a VPC, you can access the DB instance by using its endpoint and port\. 

The following diagram shows this scenario\. 

![\[A DB Instance Not in a VPC Accessed by an EC2 Instance Not in a VPC\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/legacyRDS2legacyec2.png)

You must create a security group for the DB instance that permits access from the port you specified when creating the DB instance\. For example, you could use a connection string similar to this connection string used with *sqlplus* to access an Oracle DB instance:

```
PROMPT>sqlplus 'mydbusr@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<endpoint>)
    (PORT=<port number>))(CONNECT_DATA=(SID=<database name>)))'
```

For more information, see the following documentation\. 


****  

| Database Engine | Relevant Documentation | 
| --- | --- | 
| MariaDB | [Connecting to a DB Instance Running the MariaDB Database Engine](USER_ConnectToMariaDBInstance.md) | 
| Microsoft SQL Server | [Connecting to a DB Instance Running the Microsoft SQL Server Database Engine](USER_ConnectToMicrosoftSQLServerInstance.md) | 
| MySQL | [Connecting to a DB Instance Running the MySQL Database Engine](USER_ConnectToInstance.md) | 
| Oracle | [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md) | 
| PostgreSQL | [Connecting to a DB Instance Running the PostgreSQL Database Engine](USER_ConnectToPostgreSQLInstance.md) | 

**Note**  
If you are interested in moving an existing DB instance into a VPC, you can use the AWS Management Console to do it easily\. For more information\. see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\. 

## A DB Instance Not in a VPC Accessed by a Client Application Through the Internet<a name="USER_VPC.Scenario6"></a>

New Amazon RDS customers can only create a DB instance in a VPC\. However, you might need to connect to an existing Amazon RDS DB instance that is not in a VPC from a client application through the internet\. 

The following diagram shows this scenario\. 

![\[A DB Instance not in a VPC Accessed by a Client Application via the Internet\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/legacyRDS2client.png)

In this scenario, you must ensure that the DB security group for the RDS DB instance includes the necessary ingress rules for your client application to connect\. An ingress rule specifies a network port and a CIDR/IP range\. For example, you can add an ingress rule that allows port 3306 to connect to a MySQL RDS DB instance, and a CIDR/IP range of `203.0.113.25/32`\. For more information, see [Authorizing Network Access to a DB Security Group from an IP Range](USER_WorkingWithSecurityGroups.md#USER_WorkingWithSecurityGroups.Authorizing)\.

**Warning**  
If you intend to access a DB instance behind a firewall, talk with your network administrator to determine the IP addresses you should use\.

**Note**  
If you are interested in moving an existing DB instance into a VPC, you can use the AWS Management Console to do it easily\. For more information\. see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\. 