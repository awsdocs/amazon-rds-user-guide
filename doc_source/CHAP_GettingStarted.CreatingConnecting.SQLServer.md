# Creating and connecting to a Microsoft SQL Server DB instance<a name="CHAP_GettingStarted.CreatingConnecting.SQLServer"></a>

This tutorial creates an EC2 instance and an RDS for Microsoft SQL Server DB instance\. The tutorial shows you how to access the DB instance from the EC2 instance using the Microsoft SQL Server Management Studio client\. As a best practice, this tutorial creates a private DB instance in a virtual private cloud \(VPC\)\. In most cases, other resources in the same VPC, such as EC2 instances, can access the DB instance, but resources outside of the VPC can't access it\. 

**Important**  
There's no charge for creating an AWS account\. However, by completing this tutorial, you might incur costs for the AWS resources you use\. You can delete these resources after you complete the tutorial if they are no longer needed\.

The following diagram shows the configuration when the tutorial is complete\.

![\[EC2 instance and Microsoft SQL Server DB instance.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/getting-started-sqlserver.png)

This tutorial uses **Easy create** to create a DB instance running Microsoft SQL Server with the AWS Management Console\. With **Easy create**, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy create** uses the default settings for the other configuration options\. The DB instance created by **Easy create** is private\. 

When you use **Standard create** instead of **Easy create**, you can specify more configuration options when you create a DB instance, including ones for availability, security, backups, and maintenance\. To create a public DB instance, you must use **Standard create**\. For information about creating DB instances with **Standard create**, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

**Topics**
+ [Prerequisites](#CHAP_GettingStarted.Prerequisites.SQLServer)
+ [Step 1: Create a SQL Server DB instance](#CHAP_GettingStarted.Creating.SQLServer)
+ [Step 2: Create an EC2 instance](#CHAP_GettingStarted.Creating.SQLServer.EC2)
+ [Step 3: Connect your EC2 instance and SQL Server DB instance automatically](#CHAP_GettingStarted.Connecting.EC2.SQLServer)
+ [Step 4: Connect to your SQL Server DB instance](#CHAP_GettingStarted.Connecting.SQLServer)
+ [Step 5: Explore your sample SQL Server DB instance](#CHAP_GettingStarted.SQLServer.Exploring)
+ [Step 6: Delete the EC2 instance and DB instance](#CHAP_GettingStarted.Deleting.SQLServer)

## Prerequisites<a name="CHAP_GettingStarted.Prerequisites.SQLServer"></a>

Before you begin, complete the steps in the following sections:
+ [Sign up for an AWS account](CHAP_SettingUp.md#sign-up-for-aws)
+ [Create an administrative user](CHAP_SettingUp.md#create-an-admin)

## Step 1: Create a SQL Server DB instance<a name="CHAP_GettingStarted.Creating.SQLServer"></a>

The basic building block of Amazon RDS is the DB instance\. This environment is where you run your SQL Server databases\.

In this example, you use **Easy create** to create a DB instance running the SQL Server database engine with a db\.t2\.micro DB instance class\.

**To create a Microsoft SQL Server DB instance with Easy create**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and make sure that **Easy create** is chosen\.   
![\[Easy create option.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-option.png)

1. In **Configuration**, choose **Microsoft SQL Server**\.

1. For **Edition**, choose **SQL Server Express Edition**\.

1. For **DB instance size**, choose **Free tier**\.

1. For **DB instance identifier**, enter **database\-test1**\.

   The **Create database** page should look similar to the following image\.  
![\[Engine options\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-sqlserver.png)

1. For **Master username**, enter a name for the master user, or keep the default name\.

1. To use an automatically generated master password for the DB instance, select the **Auto generate a password** box\.

   To enter your master password, clear the **Auto generate a password** box, and then enter the same password in **Master password** and **Confirm password**\.

1. Open **View default settings for Easy create**\.  
![\[Easy create default settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-sqlserver-confirm.png)

   Note the setting for **VPC**\. Your DB instance and EC2 instance must reside in the same VPC to set up connectivity between them automatically in a later step\. If you didn't create a new VPC in the AWS Region, then the default VPC is selected\.

   You can examine the default settings used with **Easy create**\. The **Editable after database is created** column shows which options you can change after you create the database\.
   + If a setting has **No** in that column, and you want a different setting, you can use **Standard create** to create the DB instance\.
   + If a setting has **Yes** in that column, and you want a different setting, you can either use **Standard create** to create the DB instance, or modify the DB instance after you create it to change the setting\.

1. Choose **Create database**\.

   To view the master username and password for the DB instance, choose **View credential details**\.

   You can use the username and password that appears to connect to the DB instance as the master user\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\.   
If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

1. In the **Databases** list, choose the name of the new SQL Server DB instance to show its details\.

   The DB instance has a status of **Creating** until it is ready to use\.

   Wait for the **Region & AZ** value to appear\. When it appears, make a note of the value because you need it later\. In the following image, the **Region & AZ** value is **us\-west\-2b**\.  
![\[Screen capture of the DB instance details.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-sqlserver-launch.png)

   When the status changes to **Available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\. While the DB instance is being created, you can move on to the next step and create an EC2 instance\.

## Step 2: Create an EC2 instance<a name="CHAP_GettingStarted.Creating.SQLServer.EC2"></a>

Create an Amazon EC2 instance that you will use to connect to your database\.

**To create an EC2 instance**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region you used for the database previously\.

1. Choose **EC2 Dashboard**, and then choose **Launch instance**, as shown in the following image\.  
![\[EC2 Dashboard.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_11.png)

   The **Launch an instance** page opens\.

1. Choose the following settings on the **Launch an instance** page\.

   1. Under **Name and tags**, for **Name**, enter **ec2\-database\-connect**\.

   1. Under **Application and OS Images \(Amazon Machine Image\)**, choose **Windows**, and then choose the **Microsoft Windows Server 2022 Base**\. Keep the default selections for the other choices\.  
![\[Choose an Amazon Machine Image.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/tutorial_ec2_sqlserver_create1.png)

   1. Under **Instance type**, choose **t2\.micro**\.

   1. Under **Key pair \(login\)**, choose a **Key pair name** to use an existing key pair\. To create a new key pair for the Amazon EC2 instance, choose **Create new key pair** and then use the **Create key pair** window to create it\.

      For more information about creating a new key pair, see [Create a key pair](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Windows Instances*\.

   1. In **Network settings**, choose **Edit**\.

      1. For **VPC**, choose the VPC that you used for the database\. If you didn't create a new VPC in the AWS Region, choose the default VPC\.

      1. For **Subnet**, choose the subnet that is in the same Availability Zone as the database\. You noted the Availability Zone of the database when you created it previously\. If you don't know the Availability Zone of the database, you can find it in the database details\.

      1. For **Auto\-assign public IP**, make sure **Enable** is selected\.

         If this setting has changed to **Disable**, then there is more than one subnet in the Availability Zone, and **Subnet** is set to a private subnet\. In this case, change the **Subnet** setting to a public subnet in the Availability Zone\.

      1. For **Firewall \(security groups\)**, keep the default values\.

      1. For **Inbound security groups rules**, choose the source of Remote Desktop Protocol \(RDP\) connections to the EC2 instance\.

         For **Source Type**, choose **My IP** if the displayed IP address is correct for RDP connections\.

         Otherwise, choose **Custom** and specify the IP address or IP address range\. To determine your public IP address, open a different browser window or tab, and use the service at [https://checkip\.amazonaws\.com](https://checkip.amazonaws.com)\. An example of an IP address is `192.0.2.1/32`\.

         In many cases, you might connect through an internet service provider \(ISP\) or from behind your firewall without a static IP address\. If so, make sure to determine the range of IP addresses used by client computers\.
**Warning**  
If you use `0.0.0.0/0` for RDP access, you make it possible for all IP addresses to access your public EC2 instances using RDP\. This approach is acceptable for a short time in a test environment, but it's unsafe for production environments\. In production, authorize only a specific IP address or range of addresses to access your EC2 instances using RDP\.

         The following image shows an example of the **Inbound security groups rules** section\.  
![\[Inbound security group rules for an EC2 instance.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EC2_RDS_Connect_NtwkSettings_sqlserver.png)

   1. Keep the default values for the remaining sections\.

   1. Review a summary of your EC2 instance configuration in the **Summary** panel, and when you're ready, choose **Launch instance**\.

1. On the **Launch Status** page, note the identifier for your new EC2 instance, for example: `i-0b3434748fd2a6bb4`\.  
![\[EC2 instance identifier on Launch Status page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/getting-started-ec2-id_sqlserver.png)

1. Choose the EC2 instance identifier to open the list of EC2 instances\. 

1. Wait until the **Instance state** for your EC2 instance has a status of **Running** before continuing\.

## Step 3: Connect your EC2 instance and SQL Server DB instance automatically<a name="CHAP_GettingStarted.Connecting.EC2.SQLServer"></a>

You can automatically connect an existing EC2 instance to a DB instance using the RDS console\. The RDS console simplifies setting up the connection between an EC2 instance and your SQL Server DB instance\. For this tutorial, set up a connection between the EC2 instance and the SQL Server DB instance that you created previously\.

Before setting up a connection between an EC2 instance and an RDS database, make sure you meet the requirements described in [Overview of automatic connectivity with an EC2 instance](ec2-rds-connect.md#ec2-rds-connect-overview)\.

If you change these security groups after you configure connectivity, the changes might affect the connection between the EC2 instance and the RDS database\.

**Note**  
You can only set up a connection between an EC2 instance and an RDS database automatically by using the AWS Management Console\. You can't set up a connection automatically with the AWS CLI or RDS API\.

**To connect an EC2 instance and an RDS database automatically**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the RDS database\.

1. For **Actions**, choose **Set up EC2 connection**\.

   The **Set up EC2 connection** page appears\.

1. On the **Set up EC2 connection** page, choose the EC2 instance\.  
![\[Set up EC2 connection page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/auto-connect-rds-ec2-set-up-sqlserver.png)

   If no EC2 instances exist in the same VPC, choose **Create EC2 instance** to create one\. In this case, make sure the new EC2 instance is in the same VPC as the RDS database\.

1. Choose **Continue**\.

   The **Review and confirm** page appears\.  
![\[EC2 connection review and confirmation page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/auto-connect-rds-ec2-confirm-sqlserver.png)

1. On the **Review and confirm** page, review the changes that RDS will make to set up connectivity with the EC2 instance\.

   If the changes are correct, choose **Confirm and set up**\.

   If the changes aren't correct, choose **Previous** or **Cancel**\.

To set up connectivity, RDS adds a security group to the EC2 instance and a security group to the DB instance\.

## Step 4: Connect to your SQL Server DB instance<a name="CHAP_GettingStarted.Connecting.SQLServer"></a>

In the following procedure, you connect to your DB instance by using Microsoft SQL Server Management Studio \(SSMS\)\.

**To connect to an RDS for SQL Server DB instance using SSMS**

1. Find the endpoint \(DNS name\) and port number for your DB instance\. 

   1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region for the DB instance\.

   1. In the navigation pane, choose **Databases**\.

   1. Choose the SQL Server DB instance name to display its details\. 

   1. On the **Connectivity** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.  
![\[Connect to a Microsoft SQL Server DB instance.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLServerConnect2.png)

1. Connect to the EC2 instance that you created earlier by following the steps in [Connect to your Microsoft Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2_GetStarted.html#ec2-connect-to-instance-windows) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Install the SQL Server Management Studio \(SSMS\) client from Microsoft\.

   To download a standalone version of SSMS to your EC2 instance, see [Download SQL Server Management Studio \(SSMS\)](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms) in the Microsoft documentation\.

   1. Use the Start menu to open Internet Explorer\.

   1. Use Internet Explorer to download and install a standalone version of SSMS\. If you are prompted that the site isn't trusted, add the site to the list of trusted sites\.

1. Start SQL Server Management Studio \(SSMS\)\. 

   The **Connect to Server** dialog box appears\. 

1. Provide the following information for your sample DB instance: 

   1. For **Server type**, choose **Database Engine**\. 

   1. For **Server name**, enter the DNS name, followed by a comma and the port number \(the default port is 1433\)\. For example, your server name should look as follows:

      ```
      database-test1.0123456789012.us-west-2.rds.amazonaws.com,1433
      ```

   1. For **Authentication**, choose **SQL Server Authentication**\. 

   1. For **Login**, enter the username that you chose to use for your sample DB instance\. This is also known as the master username\.

   1. For **Password**, enter the password that you chose earlier for your sample DB instance\. This is also known as the master user password\.

1. Choose **Connect**\. 

   After a few moments, SSMS connects to your DB instance\. For security, it is a best practice to use encrypted connections\. Only use an unencrypted SQL Server connection when the client and server are in the same VPC and the network is trusted\. For information about using encrypted connections, see [Using SSL with a Microsoft SQL Server DB instance](SQLServer.Concepts.General.SSL.Using.md)

For more information about connecting to a Microsoft SQL Server DB instance, see [Connecting to a DB instance running the Microsoft SQL Server database engine](USER_ConnectToMicrosoftSQLServerInstance.md)\.

For information about connection issues, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

## Step 5: Explore your sample SQL Server DB instance<a name="CHAP_GettingStarted.SQLServer.Exploring"></a>

You can explore your sample DB instance by using Microsoft SQL Server Management Studio \(SSMS\)\.

**To explore a DB instance using SSMS**

1. Your SQL Server DB instance comes with SQL Server's standard built\-in system databases \(master, model, msdb, and tempdb\)\. To explore the system databases, do the following: 

   1. In SSMS, on the **View** menu, choose **Object Explorer**\.

   1. Expand your DB instance, expand **Databases**, and then expand **System Databases** as shown\.   
![\[Object Explorer displaying the system databases.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-SSMS-SystemDBs.png)

   Your SQL Server DB instance also comes with a database named `rdsadmin`\. Amazon RDS uses this database to store the objects that it uses to manage your database\. The `rdsadmin` database also includes stored procedures that you can run to perform advanced tasks\. 

1. Start creating your own databases and running queries against your DB instance and databases as usual\. To run a test query against your sample DB instance, do the following: 

   1. In SSMS, on the **File** menu, point to **New** and then choose **Query with Current Connection**\. 

   1. Enter the following SQL query:

      ```
      select @@VERSION
      ```

   1. Run the query\. SSMS returns the SQL Server version of your Amazon RDS DB instance\.   
![\[SQL Query Window.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Query.png)

## Step 6: Delete the EC2 instance and DB instance<a name="CHAP_GettingStarted.Deleting.SQLServer"></a>

After you connect to and explore the sample EC2 instance and DB instance that you created, delete them so you're no longer charged for them\.

**To delete the EC2 instance**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the EC2 instance, and choose **Instance state, Terminate instance**\.

1. Choose **Terminate** when prompted for confirmation\.

For more information about deleting an EC2 instance, see [Terminate your instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/terminating-instances.html) in the *User Guide for Windows Instances*\.

**To delete the DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. Clear **Create final snapshot?** and **Retain automated backups**\.

1. Complete the acknowledgement and choose **Delete**\. 