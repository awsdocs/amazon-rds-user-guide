# Creating a MySQL DB Instance and Connecting to a Database on a MySQL DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.MySQL"></a>

The easiest way to create a DB instance is to use the AWS Management Console\. After you have created the DB instance, you can use standard MySQL utilities such as MySQL Workbench to connect to a database on the DB instance\.

**Important**  
Before you can create or connect to a DB instance, you must complete the tasks in [Setting Up for Amazon RDS](CHAP_SettingUp.md)\.

**Topics**
+ [Creating a MySQL DB Instance](#CHAP_GettingStarted.Creating.MySQL)
+ [Connecting to a Database on a DB Instance Running the MySQL Database Engine](#CHAP_GettingStarted.Connecting.MySQL)
+ [Deleting a DB Instance](#CHAP_GettingStarted.Deleting.MySQL)

## Creating a MySQL DB Instance<a name="CHAP_GettingStarted.Creating.MySQL"></a>

The basic building block of Amazon RDS is the DB instance\. This environment is where you run your MySQL databases\.

### Console<a name="CHAP_GettingStarted.Creating.MySQL.Console"></a>

You can create a DB instance running MySQL with the AWS Management Console with **Easy Create** enabled or disabled\. With **Easy Create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy Create** uses the default setting for other configuration options\. With **Easy Create** not enabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

In this example, you use **Easy Create** to create a DB instance running the MySQL database engine with a db\.t2\.micro DB instance class\.

**Note**  
For information about creating DB instances with **Easy Create** not enabled, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

**To create a MySQL DB instance with Easy Create enabled**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and make sure that **Easy Create** is chosen\.   
![\[Easy Create option\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-option.png)

1. In **Configuration**, choose **MySQL**\.

1. For **DB instance size**, choose **Free tier**\.

1. For **DB instance identifier**, enter a name for the DB instance, or leave the default name\.

1. For **Master username**, enter a name for the master user, or leave the default name\.

   The **Create database** page should look similar to the following image\.  
![\[Create database page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-mysql.png)

1. To use an automatically generated master password for the DB instance, enable **Auto generate a password**\.

   To enter your master password, disable **Auto generate a password**, and then enter the same password in **Master password** and **Confirm password**\.

1. \(Optional\) Open **View default settings for Easy create**\.  
![\[Easy Create default settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-view-default-settings.png)

   You can examine the default settings used when **Easy Create** is enabled\. If you want to change one or more settings during database creation, choose **Standard Create** to set them\. The **Editable after database creation** column shows which options you can change after database creation\. To change a setting with **No** in that column, use **Standard Create**\. For settings with **Yes** in that column, you can either use **Standard Create** or modify the DB instance after it is created to change the setting\.

1. Choose **Create database**\.

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master username and password for the DB instance, choose **View credential details**\.  
![\[Master user credentials after automatic password generation.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-credentials.png)

   You can use the username and password that appears to connect to the DB instance as the master user\.
**Important**  
You won't be able to view master user password again\. If you don't record it, you might have to change it\. If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. In the **Databases** list, choose the name of the new MySQL DB instance\.

   On the RDS console, the details for new DB instance appear\. The DB instance has a status of **creating** until the DB instance is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[Screenshot of the DB instance details.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch06.png)

## Connecting to a Database on a DB Instance Running the MySQL Database Engine<a name="CHAP_GettingStarted.Connecting.MySQL"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to a database on the DB instance\. In this example, you connect to a database on a MySQL DB instance using MySQL monitor commands\. One GUI\-based application you can use to connect is MySQL Workbench; for more information, go to the [ Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/) page\. For more information on using MySQL, go to the [MySQL documentation](http://dev.mysql.com/doc/)\. For information about installing MySQL \(including the MySQL client\), see [Installing and Upgrading MySQL](https://dev.mysql.com/doc/refman/8.0/en/installing.html)\.

 **To connect to a database on a DB instance using MySQL monitor** 

1. Find the endpoint \(DNS name\) and port number for your DB instance\. 

   1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

   1. Choose the MySQL DB instance name to display its details\. 

   1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Connect to a MySQL DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQLConnect1.png)

1. Download a SQL client that you can use to connect to the DB instance\.

   You can connect to an Amazon RDS MySQL DB instance by using tools like the MySQL command line utility\. For more information on using the MySQL client, go to [mysql — The MySQL Command\-Line Client](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) in the MySQL documentation\. One GUI\-based application you can use to connect is MySQL Workbench\. For more information, go to the [ Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/) page\.

1. Connect to the a database on a MySQL DB instance\. For example, enter the following command at a command prompt on a client computer to connect to a database on a MySQL DB instance using the MySQL client\. Substitute the DNS name for your DB instance for *<endpoint>*, the master user name you used for *<mymasteruser>*, and provide the master password you used when prompted for a password\.

   ```
   PROMPT> mysql -h <endpoint> -P 3306 -u <mymasteruser> -p
   ```

   After you enter the password for the user, you should see output similar to the following\.

   ```
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 350
   Server version: 5.6.40-log MySQL Community Server (GPL)
   
   Type 'help;' or '\h' for help. Type '\c' to clear the buffer.
   
   mysql>
   ```

If you can't connect to your MySQL DB instance, two common causes of connection failures to a new DB instance are:
+ The DB instance was created using a security group that does not authorize connections from the device or Amazon EC2 instance where the MySQL application or utility is running\. If the DB instance was created in a VPC, it must have a VPC security group that authorizes the connections\. If the DB instance was created outside of a VPC, it must have a DB security group that authorizes the connections\. For more information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.
+ The DB instance was created using the default port of 3306, and your company has firewall rules blocking connections to that port from devices in your company network\. To fix this failure, recreate the instance with a different port\.

For more information about connecting to a MySQL DB instance, see [Connecting to a DB Instance Running the MySQL Database Engine](USER_ConnectToInstance.md)\. For information on connection issues, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

## Deleting a DB Instance<a name="CHAP_GettingStarted.Deleting.MySQL"></a>

After you have connected to the sample DB instance that you created, you should delete the DB instance so you are no longer charged for it\. 

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. For **Create final snapshot?**, choose **No**, and select the acknowledgment\. 

1. Choose **Delete**\. 