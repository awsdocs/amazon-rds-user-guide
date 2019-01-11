# Step 1: Create an RDS DB Instance<a name="CHAP_Tutorials.WebServerDB.CreateDBInstance"></a>

In this step you create an Amazon RDS MySQL DB instance that maintains the data used by a web application\. 

**Important**  
Before you begin this step, you must have a VPC with both public and private subnets, and corresponding security groups\. If you don't have these, see [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. Complete the steps in [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets), [Create Additional Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.AdditionalSubnets), [ Create a VPC Security Group for a Public Web Server](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupEC2), and [ Create a VPC Security Group for a Private Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)\. 

**To launch a MySQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top\-right corner of the AWS Management Console, choose the AWS Region in which you want to create the DB instance\. This example uses the US West \(Oregon\) region\.

1. In the navigation pane, choose **Databases**\.

   If the navigation pane is closed, choose the menu icon at the top left to open it\.

1. Choose **Create database** to open the **Select engine** page\.

1. On the **Select engine** page, shown following, choose **MySQL**, and then choose **Next**\.   
![\[Select engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch01.png)

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
![\[Specify DB details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_08.png)

1. Choose **Next** and set the following values in the **Configure advanced settings** page:
   + **Virtual Private Cloud \(VPC\):** Choose an existing VPC with both public and private subnets, such as the `tutorial-vpc` \(vpc\-*identifier*\) created in [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets)
**Note**  
The VPC must have subnets in different Availability Zones\.
   + **Subnet group:** The DB subnet group for the VPC, such as the `tutorial-db-subnet-group` created in [Create a DB Subnet Group](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.DBSubnetGroup)
   + **Public accessibility:** **No**
   + **Availability zone:** **No Preference**
   + **VPC security groups:** Choose an existing VPC security group that is configured for private access, such as the `tutorial-db-securitygroup` created in [ Create a VPC Security Group for a Private Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)\.

     Remove other security groups, such as the default security group, by choosing the **X** associated with each\.
   + **Database name:** `sample`

   Leave the default settings for the other options\.  
![\[Configure Advanced Settings Panel\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_09.png)

1. To create your Amazon RDS MySQL DB instance, choose **Create database**\.

1. On the next page, choose **View DB instances details** to view your RDS MySQL DB instance\.

1. Wait for the **DB instance status** of your new DB instance to show as **available**\. Then scroll to the **Connect** section, shown following\.  
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_10.png)

   Make note of the endpoint and port for your DB instance\. You will use this information to connect your web server to your RDS DB instance\.

To make sure your RDS MySQL DB instance is as secure as possible, verify that sources outside of the VPC cannot connect to your RDS MySQL DB instance\. 

## Next Step<a name="w4aab9c33c23c11"></a>

[Step 2: Create an EC2 Instance and Install a Web Server](CHAP_Tutorials.WebServerDB.CreateWebServer.md)