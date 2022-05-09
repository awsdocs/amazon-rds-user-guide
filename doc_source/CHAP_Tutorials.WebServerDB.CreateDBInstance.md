# Create a DB instance<a name="CHAP_Tutorials.WebServerDB.CreateDBInstance"></a>

In this step, you create an Amazon RDS for MySQL DB instance that maintains the data used by a web application\. 

**Important**  
Before you begin this step, make sure that you have a VPC with both public and private subnets, and corresponding security groups\. If you don't have these, see [Tutorial: Create an Amazon VPC for use with a DB instance \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. Complete the steps in [Create a VPC with private and public subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets), [Create additional subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.AdditionalSubnets), [ Create a VPC security group for a public web server](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupEC2), and [ Create a VPC security group for a private DB instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)\. 

**To create a MySQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region where you want to create the DB instance\. This example uses the US West \(Oregon\) Region\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. On the **Create database** page, shown following, make sure that the **Standard create** option is chosen, and then choose **MySQL**\.   
![\[Select engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch01.png)

1. In the **Templates** section, choose **Free tier**\.

1. In the **Settings** section, set these values:
   + **DB instance identifier** – **tutorial\-db\-instance**
   + **Master username** – **tutorial\_user**
   + **Auto generate a password** – Clear the check box\.
   + **Master password** – Choose a password\.
   + **Confirm password** – Retype the password\.  
![\[Settings sections\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_Settings.png)

1. In the **DB instance class** section, enable **Include previous generation classes**, and set these values:
   + **Burstable classes \(includes t classes\)**
   + **db\.t2\.micro**  
![\[DB instance class section\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_DB_instance_micro.png)

1. In the **Storage** and **Availability & durability** sections, use the default values\.

1. In the **Connectivity** section, set these values:
   + **Virtual private cloud \(VPC\)** – Choose an existing VPC with both public and private subnets, such as the `tutorial-vpc` \(vpc\-*identifier*\) created in [Create a VPC with private and public subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets)
**Note**  
The VPC must have subnets in different Availability Zones\.
   + **Subnet group** – The DB subnet group for the VPC, such as the `tutorial-db-subnet-group` created in [Create a DB subnet group](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.DBSubnetGroup)
   + **Public access** – **No**
   + **VPC security group** – **Choose existing**
   + **Existing VPC security groups** – Choose an existing VPC security group that is configured for private access, such as the `tutorial-db-securitygroup` created in [ Create a VPC security group for a private DB instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)\.

     Remove other security groups, such as the default security group, by choosing the **X** associated with each\.
   + **Availability Zone** – **No preference**
   + Open **Additional configuration**, and make sure **Database port** uses the default value **3306**\.  
![\[Connectivity section\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_Connectivity.png)

1. In the **Database authentication** section, make sure **Password authentication** is selected\.

1. Open the **Additional configuration** section, and enter **sample** for **Initial database name**\. Keep the default settings for the other options\.

1. To create your MySQL DB instance, choose **Create database**\.

   Your new DB instance appears in the **Databases** list with the status **Creating**\.

1. Wait for the **Status** of your new DB instance to show as **Available**\. Then choose the DB instance name to show its details\.

1. In the **Connectivity & security** section, view the **Endpoint** and **Port** of the DB instance\.  
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_Endpoint_Port.png)

   Note the endpoint and port for your DB instance\. You use this information to connect your web server to your DB instance\.

1. Complete [Create an EC2 instance and install a web server](CHAP_Tutorials.WebServerDB.CreateWebServer.md)\.