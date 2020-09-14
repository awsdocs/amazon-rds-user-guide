# Create a DB Instance<a name="CHAP_Tutorials.WebServerDB.CreateDBInstance"></a>

In this step, you create an Amazon RDS for MySQL DB instance that maintains the data used by a web application\. 

**Important**  
Before you begin this step, make sure that you have a VPC with both public and private subnets, and corresponding security groups\. If you don't have these, see [Tutorial: Create an Amazon VPC for Use with a DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. Complete the steps in [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets), [Create Additional Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.AdditionalSubnets), [ Create a VPC Security Group for a Public Web Server](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupEC2), and [ Create a VPC Security Group for a Private DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)\. 

**Note**  
A new console interface is available for database creation\. Choose either the **New Console** or the **Original Console** instructions based on the console that you are using\. The **New Console** instructions are open by default\.

## New Console<a name="CHAP_Tutorials.WebServerDB.CreateDBInstance.Console"></a>

**To launch a MySQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region where you want to create the DB instance\. This example uses the US West \(Oregon\) Region\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. On the **Create database** page, shown following, make sure that the **Standard Create** option is chosen, and then choose **MySQL**\.   
![\[Select engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch01.png)

1. In the **Templates** section, choose **Dev/Test**\.

1. In the **Settings** section, set these values:
   + **DB instance identifier** – **tutorial\-db\-instance**
   + **Master username** – **tutorial\_user**
   + **Auto generate a password** – Disable the option
   + **Master password** – Choose a password\.
   + **Confirm password** – Retype the password\.  
![\[Settings sections\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_Settings.png)

1. In the **DB instance size** section, set these values:
   + **Burstable classes \(includes t classes\)**
   + **db\.t2\.small**  
![\[DB instance size section\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_DB_instance_size.png)

1. In the **Storage** and **Availability & durability** sections, use the default values\.

1. In the **Connectivity** section, open **Additional connectivity configuration** and set these values:
   + **Virtual private cloud \(VPC\)** – Choose an existing VPC with both public and private subnets, such as the `tutorial-vpc` \(vpc\-*identifier*\) created in [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets)
**Note**  
The VPC must have subnets in different Availability Zones\.
   + **Subnet group** – The DB subnet group for the VPC, such as the `tutorial-db-subnet-group` created in [Create a DB Subnet Group](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.DBSubnetGroup)
   + **Public access** – **No**
   + **Existing VPC security groups** – Choose an existing VPC security group that is configured for private access, such as the `tutorial-db-securitygroup` created in [ Create a VPC Security Group for a Private DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)\.

     Remove other security groups, such as the default security group, by choosing the **X** associated with each\.
   + **Database port** – **3306**  
![\[Connectivity section\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_Connectivity.png)

1. Open the **Additional configuration** section, and enter **sample** for **Initial database name**\. Keep the default settings for the other options\.

1. Choose **Create database** to create your RDS MySQL DB instance\.

   Your new DB instance appears in the **Databases** list with the status **Creating**\.

1. Wait for the **Status** of your new DB instance to show as **Available**\. Then choose the DB instance name to show its details\.

1. In the **Connectivity & security** section, view the **Endpoint** and **Port** of the DB instance\.  
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_Endpoint_Port.png)

   Note the endpoint and port for your DB instance\. You use this information to connect your web server to your DB instance\.

   To make sure that your DB instance is as secure as possible, verify that sources outside of the VPC can't connect to your DB instance\. 

1. Complete [Create an EC2 Instance and Install a Web Server](CHAP_Tutorials.WebServerDB.CreateWebServer.md)\.

## Original Console<a name="CHAP_Tutorials.WebServerDB.CreateDBInstance.CurrentConsole"></a>

**To launch a MySQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top\-right corner of the AWS Management Console, choose the AWS Region where you want to create the DB instance\. This example uses the US West \(Oregon\) Region\.

1. In the navigation pane, choose **Databases**\.

   If the navigation pane is closed, choose the menu icon at the top left to open it\.

1. Choose **Create database** to open the **Select engine** page\.

1. On the **Select engine** page, shown following, choose **MySQL**, and then choose **Next**\.   
![\[Select engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-MySQL-Launch01.png)

1. On the **Choose use case** page, choose **Dev/Test – MySQL**, and then choose **Next**\.

1. On the **Specify DB details** page, shown following, set these values:
   + **License model:** Use the default value\.
   + **DB engine version:** Use the default value\.
   + **DB instance class:** `db.t2.small`
   + **Multi\-AZ deployment:** `No`
   + **Storage type:** `General Purpose (SSD)`
   + **Allocated storage:** `20 GiB`
   + **DB instance identifier:** `tutorial-db-instance`
   + **Master username:** `tutorial_user`
   + **Master password:** Choose a password\.
   + **Confirm password:** Retype the password\.  
![\[Specify DB details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-Tutorial_WebServer_08.png)

1. Choose **Next** and set the following values in the **Configure advanced settings** page:
   + **Virtual Private Cloud \(VPC\):** Choose an existing VPC with both public and private subnets, such as the `tutorial-vpc` \(vpc\-*identifier*\) created in [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets)
**Note**  
The VPC must have subnets in different Availability Zones\.
   + **Subnet group:** The DB subnet group for the VPC, such as the `tutorial-db-subnet-group` created in [Create a DB Subnet Group](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.DBSubnetGroup)
   + **Public accessibility:** **No**
   + **Availability zone:** **No Preference**
   + **VPC security groups:** Choose an existing VPC security group that is configured for private access, such as the `tutorial-db-securitygroup` created in [ Create a VPC Security Group for a Private DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)\.

     Remove other security groups, such as the default security group, by choosing the **X** associated with each\.
   + **Database name:** `sample`

   Keep the default settings for the other options\.  
![\[Configure Advanced Settings Panel\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-Tutorial_WebServer_09.png)

1. Choose **Create database** to create your RDS MySQL DB instance\.

1. On the next page, choose **View DB instances details** to view your DB instance\.

1. Wait for the **DB instance status** of your new DB instance to show as **available**\. Then scroll to the **Connect** section, shown following\.  
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-Tutorial_WebServer_10.png)

   Note the endpoint and port for your DB instance\. You use this information to connect your web server to your DB instance\.

   To make sure that your DB instance is as secure as possible, verify that sources outside of the VPC can't connect to your DB instance\. 

1. Complete [Create an EC2 Instance and Install a Web Server](CHAP_Tutorials.WebServerDB.CreateWebServer.md)\.