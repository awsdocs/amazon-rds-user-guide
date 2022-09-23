# Create a DB instance<a name="CHAP_Tutorials.WebServerDB.CreateDBInstance"></a>

Create an Amazon RDS for MySQL DB instance that maintains the data used by a web application\. 

**To create a MySQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, check the AWS Region\. It should be the same as the one where you created your EC2 instance\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. On the **Create database** page, shown following, make sure that the **Standard create** option is chosen, and then choose **MySQL**\.  
![\[Select engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch01.png)

1. In the **Templates** section, choose **Free tier**\.

1. In the **Availability and durability** section, keep the defaults\.

1. In the **Settings** section, set these values:
   + **DB instance identifier** – Type **tutorial\-db\-instance**\.
   + **Master username** – Type **tutorial\_user**\.
   + **Auto generate a password** – Leave the option turned off\.
   + **Master password** – Type a password\.
   + **Confirm password** – Retype the password\.  
![\[Settings sections\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_Settings.png)

1. In the **Instance configuration** section, set these values:
   + **Burstable classes \(includes t classes\)**
   + **db\.t3\.micro**  
![\[Instance configuration section\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_DB_instance_micro.png)

1. In the **Storage** section, keep the defaults\.

1. In the **Connectivity** section, set these values and keep the other values as their defaults:
   + For **Compute resource**, choose **Connect to an EC2 compute resource**\.
   + For **EC2 instance**, choose the EC2 instance you created previously, such as **tutorial\-ec2\-instance\-web\-server**\.  
![\[Connectivity section\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_Connectivity.png)

1. In the **Database authentication** section, make sure **Password authentication** is selected\.

1. Open the **Additional configuration** section, and enter **sample** for **Initial database name**\. Keep the default settings for the other options\.

1. To create your MySQL DB instance, choose **Create database**\.

   Your new DB instance appears in the **Databases** list with the status **Creating**\.

1. Wait for the **Status** of your new DB instance to show as **Available**\. Then choose the DB instance name to show its details\.

1. In the **Connectivity & security** section, view the **Endpoint** and **Port** of the DB instance\.  
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_Endpoint_Port.png)

   Note the endpoint and port for your DB instance\. You use this information to connect your web server to your DB instance\.

1. Complete [Install a web server on your EC2 instance](CHAP_Tutorials.WebServerDB.CreateWebServer.md)\.