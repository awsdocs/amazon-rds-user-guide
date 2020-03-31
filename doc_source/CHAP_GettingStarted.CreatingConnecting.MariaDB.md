# Creating a MariaDB DB Instance and Connecting to a Database on a MariaDB DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.MariaDB"></a>

The easiest way to create a MariaDB DB instance is to use the Amazon RDS console\. After you create the DB instance, you can use command line tools such as mysql or standard graphical tools such as HeidiSQL to connect to a database on the DB instance\.

**Important**  
Before you can create or connect to a DB instance, you must complete the tasks in [Setting Up for Amazon RDS](CHAP_SettingUp.md)\.

**Topics**
+ [Creating a MariaDB DB Instance](#CHAP_GettingStarted.Creating.MariaDB)
+ [Connecting to a Database on a DB Instance Running the MariaDB Database Engine](#CHAP_GettingStarted.Connecting.MariaDB)
+ [Deleting a DB Instance](#CHAP_GettingStarted.Deleting.MariaDB)

## Creating a MariaDB DB Instance<a name="CHAP_GettingStarted.Creating.MariaDB"></a>

The basic building block of Amazon RDS is the DB instance\. This environment is where you run your MariaDB databases\.

### Console<a name="CHAP_GettingStarted.Creating.MariaDB.Console"></a>

You can create a DB instance running MariaDB with the AWS Management Console with **Easy Create** enabled or not enabled\. With **Easy Create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy Create** uses the default setting for other configuration options\. With **Easy Create** not enabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

In this example, you use **Easy Create** to create a DB instance running the MariaDB database engine with a db\.t2\.micro DB instance class\.

**Note**  
For information about creating DB instances with **Easy Create** not enabled, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

**To create a MariaDB DB instance with Easy Create enabled**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and make sure that **Easy Create** is chosen\.   
![\[Easy Create option\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-option.png)

1. In **Configuration**, choose **MariaDB**\.

1. For **DB instance size**, choose **Free tier**\.

1. For **DB instance identifier**, enter a name for the DB instance, or leave the default name\.

1. For **Master username**, enter a name for the master user, or leave the default name\.

   The **Create database** page should look similar to the following image\.  
![\[Create database page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-mariadb.png)

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

1. For **Databases**, choose the name of the new Maria DB instance\.

   On the RDS console, the details for new DB instance appear\. The DB instance has a status of **creating** until the DB instance is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[Summary during DB instance creation\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDB-Launch06.png)

## Connecting to a Database on a DB Instance Running the MariaDB Database Engine<a name="CHAP_GettingStarted.Connecting.MariaDB"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to a database on the DB instance\. In this example, you connect to a database on a Maria DB instance using the mysql command\-line tool\. One GUI\-based application you can use to connect is HeidiSQL\. For more information, see the [ Download HeidiSQL](http://www.heidisql.com/download.php) page\. For more information on using MariaDB, see the [MariaDB documentation](https://mariadb.com/kb/en/mariadb/documentation/)\.

 **To connect to a database on a DB instance using the `mysql` command\-line tool** 

1. Find the endpoint \(DNS name\) and port number for your DB instance\. 

   1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

   1. Choose the Maria DB instance name to display its details\. 

   1. On the **Connectivity & security** tab, copy the endpoint\. Also note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Connect to a Maria DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDBConnect1.png)

1. Enter the following command at a command prompt on a client computer to connect to a database on a Maria DB instance\. Substitute the DNS name \(endpoint\) for your DB instance for `<endpoint>`, the master user name you used for *`<mymasteruser>`*, and provide the master password you used when prompted for a password\.

   ```
   PROMPT> mysql -h <endpoint> -P 3306 -u <mymasteruser>  -p
   ```

   After you enter the password for the user, you should see output similar to the following\.

   ```
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 272
   Server version: 5.5.5-10.0.17-MariaDB-log MariaDB Server
   
   Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.
   
   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.
   
   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
   
   mysql >
   ```

For more information about connecting to a MariaDB DB instance, see [Connecting to a DB Instance Running the MariaDB Database Engine](USER_ConnectToMariaDBInstance.md)\. For information on connection issues, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

## Deleting a DB Instance<a name="CHAP_GettingStarted.Deleting.MariaDB"></a>

After you have connected to the sample DB instance that you created, you should delete the DB instance so you are no longer charged for it\.

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance you want to delete\.

1. For **Actions**, choose **Delete**\.

1. For **Create final snapshot?**, choose **No**, and select the acknowledgment\.

1. Choose **Delete**\. 