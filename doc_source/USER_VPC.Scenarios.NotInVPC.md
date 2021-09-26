# Scenarios for accessing a DB instance not in a VPC<a name="USER_VPC.Scenarios.NotInVPC"></a>

Amazon RDS supports the following scenarios for accessing a DB instance that is not in a VPC:
+ [An EC2 instance in a VPC](USER_VPC.Scenarios.md#USER_VPC.Scenario1)
+ [A client application through the internet](#USER_VPC.Scenario6)
+ [An EC2 instance not in a VPC](#USER_VPC.Scenario7)

**Important**  
If your DB instance was created after 2013, it is probably in a VPC\. For information about accessing a DB instance in a VPC, see [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)\.

## A DB instance not in a VPC accessed by an EC2 instance in a VPC<a name="USER_VPC.Scenario5"></a>

In the case where you have an EC2 instance in a VPC and an RDS DB instance not in a VPC, you can connect them over the public internet\. 

The following diagram shows this scenario\. 

![\[A DB Instance not in a VPC Accessed by an EC2 Instance in a VPC\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/legacyRDS2EC2VPC.png)

**Note**  
*ClassicLink*, as described in [A DB instance in a VPC accessed by an EC2 instance not in a VPC](USER_VPC.Scenarios.md#USER_VPC.ClassicLink), is not available for this scenario\. 

To connect your DB instance and your EC2 instance over the public internet, do the following:
+ Ensure that the EC2 instance is in a public subnet in the VPC\.
+ Ensure that the RDS DB instance was marked as publicly accessible\.
+ A note about network ACLs here\. A network ACL is like a firewall for your entire subnet\. Therefore, all instances in that subnet are subject to network ACL rules\. By default, network ACLs allow all traffic and you generally don't need to worry about them, unless you particularly want to add rules as an extra layer of security\. A security group, on the other hand, is associated with individual instances, and you do need to worry about security group rules\.
+ Add the necessary ingress rules to the DB security group for the RDS DB instance\.

  An ingress rule specifies a network port and a CIDR/IP range\. For example, you can add an ingress rule that allows port 3306 to connect to a MySQL RDS DB instance, and a CIDR/IP range of `203.0.113.25/32`\. For more information, see [Authorizing network access to a DB security group from an IP range](USER_WorkingWithSecurityGroups.md#USER_WorkingWithSecurityGroups.Authorizing)\.

**Note**  
If you are interested in moving an existing DB instance into a VPC, you can use the AWS Management Console to do it easily\. For more information\. see [Moving a DB instance not in a VPC into a VPC](USER_VPC.Non-VPC2VPC.md)\. 

## A DB instance not in a VPC accessed by a client application through the internet<a name="USER_VPC.Scenario6"></a>

New Amazon RDS customers can only create a DB instance in a VPC\. However, you might need to connect to an existing Amazon RDS DB instance that is not in a VPC from a client application through the internet\. 

The following diagram shows this scenario\. 

![\[A DB Instance not in a VPC Accessed by a Client Application via the Internet\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/legacyRDS2client.png)

In this scenario, you must ensure that the DB security group for the RDS DB instance includes the necessary ingress rules for your client application to connect\. An ingress rule specifies a network port and a CIDR/IP range\. For example, you can add an ingress rule that allows port 3306 to connect to a MySQL RDS DB instance, and a CIDR/IP range of `203.0.113.25/32`\. For more information, see [Authorizing network access to a DB security group from an IP range](USER_WorkingWithSecurityGroups.md#USER_WorkingWithSecurityGroups.Authorizing)\.

**Warning**  
If you intend to access a DB instance behind a firewall, talk with your network administrator to determine the IP addresses you should use\.

**Note**  
If you are interested in moving an existing DB instance into a VPC, you can use the AWS Management Console to do it easily\. For more information\. see [Moving a DB instance not in a VPC into a VPC](USER_VPC.Non-VPC2VPC.md)\. 

## A DB instance not in a VPC accessed by an EC2 instance not in a VPC<a name="USER_VPC.Scenario7"></a>

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

| Database engine | Relevant documentation | 
| --- | --- | 
| MariaDB | [Connecting to a DB instance running the MariaDB database engine](USER_ConnectToMariaDBInstance.md) | 
| Microsoft SQL Server | [Connecting to a DB instance running the Microsoft SQL Server database engine](USER_ConnectToMicrosoftSQLServerInstance.md) | 
| MySQL | [Connecting to a DB instance running the MySQL database engine](USER_ConnectToInstance.md) | 
| Oracle | [Connecting to your Oracle DB instance](USER_ConnectToOracleInstance.md) | 
| PostgreSQL | [Connecting to a DB instance running the PostgreSQL database engine](USER_ConnectToPostgreSQLInstance.md) | 

**Note**  
If you are interested in moving an existing DB instance into a VPC, you can use the AWS Management Console to do it easily\. For more information\. see [Moving a DB instance not in a VPC into a VPC](USER_VPC.Non-VPC2VPC.md)\. 