# Creating a Microsoft SQL Server DB Instance and Connecting to a DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.SQLServer"></a>

The basic building block of Amazon RDS is the DB instance\. Your Amazon RDS DB instance is similar to your on\-premises Microsoft SQL Server\. After you create your SQL Server DB instance, you can add one or more custom databases to it\. 

**Important**  
You must have an AWS account before you can create a DB instance\. If you don't have an AWS account, open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\. 

In this topic, you create a sample SQL Server DB instance\. You then connect to the DB instance and run a simple query\. Finally, you delete the sample DB instance\.

## Creating a Sample SQL Server DB Instance<a name="CHAP_GettingStarted.Creating.SQLServer"></a>

The DB instance is where you run your Microsoft SQL Server databases\.

### Console<a name="CHAP_GettingStarted.Creating.SQLServer.Console"></a>

You can create a DB instance running Microsoft SQL Server with the AWS Management Console with **Easy Create** enabled or not enabled\. With **Easy Create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy Create** uses the default setting for other configuration options\. With **Easy Create** not enabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

For this example, you use **Easy Create** to create a DB instance running the Microsoft SQL Server database engine with a db\.t2\.micro DB instance class\.

**Note**  
For information about creating DB instances with **Easy Create** not enabled, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

**To create a Microsoft SQL Server DB instance with Easy Create enabled**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and make sure that **Easy Create** is chosen\.   
![\[Easy Create option\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-option.png)

1. In **Configuration**, choose **Microsoft SQL Server**\.

1. For **DB instance size**, choose **Free tier**\.

1. For **DB instance identifier**, enter a name for the DB instance, or leave the default name\.

1. For **Master username**, enter a name for the master user, or leave the default name\.

   The **Create database** page should look similar to the following image\.  
![\[Engine options\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-sql-server.png)

1. To use an automatically generated master password for the DB instance, make sure that the **Auto generate a password** check box is chosen\.

   To enter your master password, clear the **Auto generate a password** check box, and then enter the same password in **Master password** and **Confirm password**\.

1. \(Optional\) Open **View default settings for Easy create**\.  
![\[Easy Create default settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-view-default-settings.png)

   You can examine the default settings used when **Easy Create** is enabled\. If you want to change one or more settings during database creation, choose **Standard Create** to set them\. The **Editable after database creation** column shows which options you can change after database creation\. To change a setting with **No** in that column, use **Standard Create**\. For settings with **Yes** in that column, you can either use **Standard Create** or modify the DB instance after it's created to change the setting\.

1. Choose **Create database**\.

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB instance, choose **View credential details**\.  
![\[Master user credentials after automatic password generation.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-credentials.png)

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. For **Databases**, choose the name of the new Microsoft SQL Server DB instance\.

   On the RDS console, the details for new DB instance appear\. The DB instance has a status of **creating** until the DB instance is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[Screenshot of the DB instance details.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch05.png)

## Connecting to Your Sample SQL Server DB Instance<a name="CHAP_GettingStarted.Connecting.SQLServer"></a>

In this procedure, you connect to your sample DB instance by using Microsoft SQL Server Management Studio \(SSMS\)\.

Before you begin, your database should have a status of **Available**\. If it has a status of **Creating** or **Backup\-up**, wait until it's **Available**\. The status updates without requiring you to refresh the page\. This process can take up to 20 minutes\. 

Also, make sure you have SSMS installed\. If you can also connect to SQL Server on RDS by using a different tools, such as an add\-in for your development environment or some other database tool\. However, this tutorial only covers using SSMS\. To download a stand\-alone version of this SSMS, see [Download SQL Server Management Studio \(SSMS\)](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms) in the Microsoft documentation\. 

**To connect to a DB instance using SSMS**

1. Find the DNS name and port number for your DB instance\. 

   1. Open the RDS console, and then choose **Databases** to display a list of your DB instances\. 

   1. Hover your mouse cursor over the name **sample\-instance**, which is blue\. When you do this, the mouse cursor changes into a selection icon \(for example, a pointing hand\)\. Also, the DB instance name, becomes underlined\. 

      Click on the DB instance name to choose it\.  The screen changes to display the information for the DB instance you choose\. 

   1. On the **Connectivity** tab, which opens by default, copy the endpoint\. The **Endpoint** looks something like this: `sample-instance.abc2defghije.us-west-2.rds.amazonaws.com`\. Also, take note of the port number\. The default port for SQL Server is 1433\. If yours is different, write it down\.

1. Start SQL Server Management Studio\. 

   The **Connect to Server** dialog box appears\. 

1. Provide the information for your sample DB instance\. 

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

   If you can't connect to your DB instance, see [Troubleshooting Connections to Your SQL Server DB Instance](USER_ConnectToMicrosoftSQLServerInstance.md#USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting)\.

## Exploring Your Sample SQL Server DB Instance<a name="CHAP_GettingStarted.SQLServer.Exploring"></a>

In this procedure, you continue the previous procedure and explore your sample DB instance by using Microsoft SQL Server Management Studio \(SSMS\)\.

**To explore a DB instance using SSMS**

1. Your SQL Server DB instance comes with SQL Server's standard built\-in system databases \(master, model, msdb, and tempdb\)\. To explore the system databases, do the following: 

   1. In SSMS, on the **View** menu, choose **Object Explorer**\.

   1. Expand your DB instance, expand **Databases**, and then expand **System Databases** as shown following\.   
![\[Object Explorer displaying the system databases\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-SSMS-SystemDBs.png)

1. Your SQL Server DB instance also comes with a database named `rdsadmin`\. Amazon RDS uses this database to store the objects that it uses to manage your database\. The `rdsadmin` database also includes stored procedures that you can run to perform advanced tasks\. 

1. You can now start creating your own databases and running queries against your DB instance and databases as usual\. To run a test query against your sample DB instance, do the following: 

   1. In SSMS, on the **File** menu point to **New** and then choose **Query with Current Connection**\. 

   1. Enter the following SQL query\.

      ```
      select @@VERSION
      ```

   1. Run the query\. SSMS returns the SQL Server version of your Amazon RDS DB instance\.   
![\[SQL Query Window\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Query.png)

## Deleting Your Sample DB Instance<a name="CHAP_GettingStarted.Deleting.SQLServer"></a>

After you are done exploring the sample DB instance that you created, you should delete the DB instance so that you are no longer charged for it\. 

**To delete a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose the button next to **sample\-instance**, or whatever you named your sample DB instance\. 

1. Choose **Delete** from the **Actions** above \(it's to the left of the orange **Create database** button\. 

   If you see a message that says **This database has deletion protection option enabled**, follow these steps:
   + Choose **Modify**\.
   + On the **Deletion protection** card \(near the bottom of the page\), clear the box next to **Enable deletion protection**\. Then choose **Continue**\. 
   + On the **Scheduling of modifications** card, choose **Apply immediately\.** Then choose **Modify DB Instance**\.
   + Try again to delete the instance by choosing **Delete** from the **Actions** menu\.

1. Clear the box for **Create final snapshot**\. Because this isn't a production database, you don't need to save a copy of it\. 

1. Verify that you selected the correct database to delete\. The name "sample\-instance" displays in the title of the screen: **Delete sample\-instance instance?** 

   If you don't recognize the name of your sample instance in the title, choose **Cancel** and start over\.

1. To confirm that you want to permanently delete the database that is displayed in the title of this screen, do the following:
   + Check the box to confirm: **I acknowledge that upon instance deletion, automated backups, including system snapshots and point\-in\-time recovery, will no longer be available**\.
   + Type "**delete me**" into the box **To confirm deletion, type *delete me* into the field**\.
   + Choose **Delete**\. This action can't be undone\.

   The database shows a status of **Deleting** until deletion is complete\.