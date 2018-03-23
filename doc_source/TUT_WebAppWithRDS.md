# Tutorial: Create a Web Server and an Amazon RDS Database<a name="TUT_WebAppWithRDS"></a>

This tutorial helps you install an Apache web server with PHP, and create a MySQL database\. The web server runs on an Amazon EC2 instance using Amazon Linux, and the MySQL database is an Amazon RDS MySQL DB instance\. Both the Amazon EC2 instance and the Amazon RDS DB instance run in a VPC based in Amazon Virtual Private Cloud service \(Amazon VPC\)\. 

**Note**  
This tutorial works with Amazon Linux and might not work for other versions of Linux such as Ubuntu\.

Before you begin this tutorial, you must have a VPC with both public and private subnets, and corresponding security groups\. If you don't have these, complete the following tasks in [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md): 
+ [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets)
+ [Create Additional Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.AdditionalSubnets)
+ [ Create a VPC Security Group for a Public Web Server](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupEC2)
+ [ Create a VPC Security Group for a Private Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)

In this tutorial, you perform the following procedures:
+ [Step 1: Create an RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateDBInstance.md)
+ [Step 2: Create an EC2 Instance and Install a Web Server](CHAP_Tutorials.WebServerDB.CreateWebServer.md)