# Creating a Microsoft SQL Server DB instance and connecting to it<a name="CHAP_GettingStarted.CreatingConnecting.SQLServer"></a>

The basic building block of Amazon RDS is the DB instance\. Your Amazon RDS DB instance is similar to your on\-premises Microsoft SQL Server\. After you create your SQL Server DB instance, you can add one or more custom databases to it\. 

**Important**  
Before you can create or connect to a DB instance, make sure to complete the tasks in [Setting up for Amazon RDS](CHAP_SettingUp.md)\.

In this topic, you create a sample SQL Server DB instance\. You then connect to the DB instance and run a simple query\. Finally, you delete the sample DB instance\.

## Creating a sample SQL Server DB instance<a name="CHAP_GettingStarted.Creating.SQLServer"></a>

You can use **Easy create** to create a DB instance running Microsoft SQL Server with the AWS Management Console\. With **Easy create**, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy create** uses the default settings for the other configuration options\. When you use **Standard create** instead of **Easy create**, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

In this example, you use **Easy create** to create a DB instance running the Microsoft SQL Server database engine with a db\.t2\.micro DB instance class\.

**Note**  
For information about creating DB instances with **Standard create**, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

**To create a Microsoft SQL Server DB instance with Easy create**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and make sure that **Easy create** is chosen\.   
![\[Easy create option\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-option.png)

1. From **Engine type**, choose **Microsoft SQL Server**\.

1. For **DB instance size**, choose **Free tier**\.

1. For **DB instance identifier**, enter **sample\-instance**, or leave the default name\.

1. For **Master username**, enter a name for the master user, or leave the default name\.

1. To use an automatically generated master password for the DB instance, select the **Auto generate a password** box\.

   To enter your master password, clear the **Auto generate a password** box, and then enter the same password in **Master password** and **Confirm password**\.

   The **Create database** page should look similar to the following image\.  
![\[Engine options\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-sqlserver.png)

1. \(Optional\) Expand **View default settings for Easy create**\.  
![\[Easy create default settings\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-view-default-sqlserver.png)

   You can examine the default settings used with **Easy create**\. The **Editable after database is created** column shows which options you can change after database creation\.
   + To change settings with **No** in that column, use **Standard create**\. 
   + To change settings with **Yes** in that column, either use **Standard create**, or modify the DB instance after it is created to change the settings\.

   The following are important considerations for changing the default settings:
   + In some cases, you might want your DB instance to use a specific virtual private cloud \(VPC\) based on the Amazon VPC service\. Or you might require a specific subnet group or security group\. If so, use **Standard create** to specify these resources\. You might have created these resources when you set up for Amazon RDS\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.
   + If you want to be able to access the DB instance from a client outside of its VPC;, use **Standard create** to set **Public access** to **Yes**\.

     If the DB instance should be private, leave **Public access** set to **No**\.

1. Choose **Create database**\.

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB instance, choose **View credential details**\.

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\.   
If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

1. For **Databases**, choose the name of the new Microsoft SQL Server DB instance\.

   On the RDS console, the details for new DB instance appear\. The DB instance has a status of **Creating** until the DB instance is ready to use\. When the state changes to **Available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[Screen capture of the DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-sqlserver-launch.png)

## Connecting an EC2 instance and an SQL Server DB instance automatically<a name="CHAP_GettingStarted.Connecting.EC2.SQLServer"></a>

You can automatically connect an existing EC2 instance to the DB instance from the RDS console\. The RDS console simplifies setting up the connection between an EC2 instance and your SQL Server DB instance\.

Before setting up a connection between an EC2 instance and an RDS database, make sure you meet the requirements described in [Overview of automatic connectivity with an EC2 instance](ec2-rds-connect.md#ec2-rds-connect-overview)\. If you make changes to required security groups after you configure connectivity, the changes might affect the connection between the EC2 instance and the RDS database\.

**Note**  
You can only set up a connection between an EC2 instance and an RDS database automatically by using the AWS Management Console\. You can't set up a connection automatically with the AWS CLI or RDS API\.

**To connect an EC2 instance and an RDS database automatically**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the RDS database\.

1. For **Actions**, choose **Set up EC2 connection**\.

   The **Set up EC2 connection** page appears\.  
![\[Set up EC2 connection page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/auto-connect-rds-ec2-set-up.png)

1. On the **Set up EC2 connection** page, choose the EC2 instance\.

   If no EC2 instances exist in the same VPC, choose **Create EC2 instance** to create one\. In this case, make sure the new EC2 instance is in the same VPC as the RDS database\.

1. Choose **Continue**\.

   The **Review and confirm** page appears\.  
![\[EC2 connection review and confirmation page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/auto-connect-rds-ec2-confirm.png)

1. On the **Review and confirm** page, review the changes that RDS will make to set up a connection with the EC2 instance\. Do one of the following:
   + If the changes are correct, choose **Set up connection**\.
   + If the changes aren't correct, choose **Previous** or **Cancel**\.

1. To verify that the connection is made, choose **Connectivity and security** in the console and find the EC2 resource identifier under **Connected compute resource**\.

## Connecting to your sample SQL Server DB instance<a name="CHAP_GettingStarted.Connecting.SQLServer"></a>

In the following procedure, you connect to your sample DB instance by using Microsoft SQL Server Management Studio \(SSMS\)\.

Before you begin, your database should have a status of **Available**\. If it has a status of **Creating** or **Backing\-up**, wait until it shows **Available**\. The status updates without requiring you to refresh the page\. This process can take up to 20 minutes\.

Also, make sure that you have SSMS installed\. You can also connect to RDS for SQL Server by using a different tool, such as an add\-in for your development environment or some other database tool\. However, this tutorial only covers using SSMS\. To download a standalone version of this SSMS, see [Download SQL Server Management Studio \(SSMS\)](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms) in the Microsoft documentation\.

**To connect to a DB instance using SSMS**

1. Make sure that your DB instance is associated with a security group that provides access to it\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

   If you didn't specify the appropriate security group when you created the DB instance, you can modify the DB instance to change its security group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

   If your DB instance is publicly accessible, make sure its associated security group has inbound rules for the IP addresses that you want to access it\. If your DB instance is private, make sure its associated security group has inbound rules for the security group of each resource to access it\. An example is the security group for an Amazon EC2 instance\.

1. Find the DNS name and port number for your DB instance\.

   1. Open the RDS console, and then choose **Databases** to display a list of your DB instances\.

   1. Hover your mouse cursor over the name **sample\-instance**, which is blue\. When you do this, the mouse cursor changes into a selection icon \(for example, a pointing hand\)\. Also, the DB instance name becomes underlined\. 

      Click on the DB instance name to choose it\.  The screen changes to display the information for the DB instance you choose\. 

   1. On the **Connectivity** tab, which opens by default, copy the endpoint\. The **Endpoint** looks something like this: `sample-instance.123456789012.us-east-2.rds.amazonaws.com`\. Also, note the port number\. The default port for SQL Server is 1433\. If yours is different, write it down\.  
![\[Connect to a Microsoft SQL Server DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLServerConnect1.png)

1. Start SQL Server Management Studio\. 

   The **Connect to Server** dialog box appears\. 

1. Provide the following information for your sample DB instance: 

   1. For **Server type**, choose **Database Engine**\. 

   1. For **Server name**, enter the DNS name, followed by a comma and the port number \(the default port is 1433\)\. For example, your server name should look like the following\.

      ```
      sample-instance.abc2defghije.us-west-2.rds.amazonaws.com,1433
      ```

   1. For **Authentication**, choose **SQL Server Authentication**\. 

   1. For **Login**, enter the user name that you chose to use for your sample DB instance\. This is also known as the master user name\.

   1. For **Password**, enter the password that you chose earlier for your sample DB instance\. This is also known as the master user password\.

1. Choose **Connect**\. 

   After a few moments, SSMS connects to your DB instance\.

For more information about connecting to a Microsoft SQL Server DB instance, see [Connecting to a DB instance running the Microsoft SQL Server database engine](USER_ConnectToMicrosoftSQLServerInstance.md)\. For information on connection issues, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

## Exploring your sample SQL Server DB instance<a name="CHAP_GettingStarted.SQLServer.Exploring"></a>

In this procedure, you continue the previous procedure and explore your sample DB instance by using Microsoft SQL Server Management Studio \(SSMS\)\.

**To explore a DB instance using SSMS**

1. Your SQL Server DB instance comes with SQL Server's standard built\-in system databases \(master, model, msdb, and tempdb\)\. To explore the system databases, do the following: 

   1. In SSMS, on the **View** menu, choose **Object Explorer**\.

   1. Expand your DB instance, expand **Databases**, and then expand **System Databases** as shown\.   
![\[Object Explorer displaying the system databases\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-SSMS-SystemDBs.png)

   Your SQL Server DB instance also comes with a database named `rdsadmin`\. Amazon RDS uses this database to store the objects that it uses to manage your database\. The `rdsadmin` database also includes stored procedures that you can run to perform advanced tasks\. 

1. Start creating your own databases and running queries against your DB instance and databases as usual\. To run a test query against your sample DB instance, do the following: 

   1. In SSMS, on the **File** menu point to **New** and then choose **Query with Current Connection**\. 

   1. Enter the following SQL query\.

      ```
      select @@VERSION
      ```

   1. Run the query\. SSMS returns the SQL Server version of your Amazon RDS DB instance\.   
![\[SQL Query Window\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Query.png)

## Deleting your sample DB instance<a name="CHAP_GettingStarted.Deleting.SQLServer"></a>

After you're done exploring your sample DB instance, delete it so that you're no longer charged for it\. 

**To delete a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose the button next to **sample\-instance**, or whatever you named your sample DB instance\. 

1. From **Actions**, choose **Delete**\.

1. If you see a message that says **This database has deletion protection option enabled**, follow these steps:

   1. Choose **Modify**\.

   1. On the **Deletion protection** card \(near the bottom of the page\), clear the box next to **Enable deletion protection**\. Then choose **Continue**\.

   1. On the **Scheduling of modifications** card, choose **Apply immediately\.** Then choose **Modify DB instance**\.

   1. Try again to delete the instance by choosing **Delete** from the **Actions** menu\.

1. Clear the box for **Create final snapshot**\. Because this isn't a production database, you don't need to save a copy of it\.

1. Verify that you selected the correct database to delete\. The name "sample\-instance" displays in the title of the screen: **Delete sample\-instance instance?** 

   If you don't recognize the name of your sample instance in the title, choose **Cancel** and start over\.

1. To confirm that you want to permanently delete the database that is displayed in the title of this screen, do the following:
   + Select the box to confirm: **I acknowledge that upon instance deletion, automated backups, including system snapshots and point\-in\-time recovery, will no longer be available**\.
   + Enter "**delete me**" into the box **To confirm deletion, type *delete me* into the field**\.
   + Choose **Delete**\. This action can't be undone\.

   The database shows a status of **Deleting** until deletion is complete\.