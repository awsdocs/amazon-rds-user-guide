# Step 1: Create an RDS DB Instance<a name="CHAP_Tutorials.WebServerDB.CreateDBInstance"></a>

In this step you create an Amazon RDS MySQL DB instance that maintains the data used by a web application\. 

**Important**  
Before you begin this step, you must have a VPC with both public and private subnets, and corresponding security groups\. If you don't have these, see [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. Complete the steps in [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets), [Create Additional Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.AdditionalSubnets), [ Create a VPC Security Group for a Public Web Server](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupEC2), and [ Create a VPC Security Group for a Private Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)\. 

**To launch a MySQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top\-right corner of the AWS Management Console, choose the region in which you want to create the DB instance\. This example uses the US West \(Oregon\) region\.

1. Choose **Instances**\.

1. Choose **Launch DB Instance**\.

1. On the **Select Engine** page, shown following, choose the MySQL DB engine, and then choose **Select**\.   
![\[Select Engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch01.png)

1. On the **Production** page, below **Dev/Test**, choose **MySQL This instance is intended for use outside of production**, and then choose **Next Step**\.

1. On the **Specify DB Details** page, shown following, set these values:

   + **DB Engine Version:** Use the default value\.

   + **DB Instance Class:** `db.t2.micro`

   + **Multi\-AZ Deployment:** `No`

   + **Storage Type:** `General Purpose (SSD)`

   + **Allocated Storage:** `50 GB`

   + **DB Instance Identifier:** `tutorial-db-instance`

   + **Master Username:** `tutorial_user`

   + **Master Password:** Choose a password\.

   + **Confirm Password:** Retype the password\.  
![\[Specify DB Details Panel\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_08.png)

1. Choose **Next Step** and set the following values in the **Configure Advanced Settings** page, shown following:

   + **VPC:** Choose an existing VPC with both public and private subnets, such as the `tutorial-vpc` \(vpc\-*identifier*\) created in [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets)
**Note**  
The VPC must have subnets in different availability zones\.

   + **Subnet group:** `Create a new DB Subnet Group`

   + **Publicly Accessible:** `No`

   + **Availability Zone:** `No Preference`

   + **VPC Security Group\(s\):** Choose an existing security group that is configured for private access, such as the `tutorial-db-securitygroup` created in [ Create a VPC Security Group for a Private Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)

   + **Database Name:** `sample`  
![\[Configure Advanced Settings Panel\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_09.png)

1. To create your Amazon RDS MySQL DB instance, choose **Launch DB Instance**\.

1. On the next page, choose **View Your DB Instances** to view your RDS MySQL DB instance\.

1. Wait for the status of your new DB instance to show as `available`\. Then choose the selection box to the left of your DB instance to display the DB instance details, shown following\.   
![\[DB Instance Details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_10.png)

   Make note of the endpoint for your DB instance\. This endpoint shows the server name and port that you use to connect your web server to your RDS DB instance\.

To make sure your RDS MySQL DB instance is as secure as possible, verify that sources outside of the VPC cannot connect to your RDS MySQL DB instance\. 

## Next Step<a name="w3ab1b9c37c17c11"></a>

[Step 2: Create an EC2 Instance and Install a Web Server](CHAP_Tutorials.WebServerDB.CreateWebServer.md)