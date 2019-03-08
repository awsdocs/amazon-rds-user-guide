# Creating a MySQL DB Instance and Connecting to a Database on a MySQL DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.MySQL"></a>

The easiest way to create a DB instance is to use the AWS Management Console\. Once you have created the DB instance, you can use standard MySQL utilities such as MySQL Workbench to connect to a database on the DB instance\.

**Important**  
You must complete the tasks in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section before you can create or connect to a DB instance\.

**Topics**
+ [Creating a MySQL DB Instance](#CHAP_GettingStarted.Creating.MySQL)
+ [Connecting to a Database on a DB Instance Running the MySQL Database Engine](#CHAP_GettingStarted.Connecting.MySQL)
+ [Deleting a DB Instance](#CHAP_GettingStarted.Deleting.MySQL)

## Creating a MySQL DB Instance<a name="CHAP_GettingStarted.Creating.MySQL"></a>

The basic building block of Amazon RDS is the DB instance\. This is the environment in which you run your MySQL databases\. 

In this example, you create a DB instance running the MySQL database engine called *mysql\-instance1*, with a *db\.m1\.small* DB instance class, 20 GiB of storage, and automated backups enabled with a retention period of one day\.

**To create a MySQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

   If the navigation pane is closed, choose the menu icon at the top left to open it\.

1. Choose **Create database**\. The **Select engine** page opens\.   
![\[Select engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch01.png)

1. Choose **MySQL**, and then choose **Next**\.

1. The **Choose use case** page asks if you are planning to use the DB instance you are creating for production\. Choose **Dev/Test** and then choose **Next**\. 

1. On the **Specify DB Details** page, specify your DB instance information\. The following table shows settings for an example DB instance\. When the settings are as you want them, choose **Next**\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MySQL.html)  
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch02.png)

1. Choose **Next**\.

1.  On the **Configure advanced settings** page, provide additional information that RDS needs to launch the MySQL DB instance\. The table shows settings for an example DB instance\. Specify your DB instance information, then choose **Create database**\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MySQL.html)

1. Choose **Create database**\. 

1. Choose **View DB instance details**\. 

   On the RDS console, the details for new DB instance appear\. The DB instance has a status of **creating** until the DB instance is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch06.png)

## Connecting to a Database on a DB Instance Running the MySQL Database Engine<a name="CHAP_GettingStarted.Connecting.MySQL"></a>

Once Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to a database on the DB instance\. In this example, you connect to a database on a MySQL DB instance using MySQL monitor commands\. One GUI\-based application you can use to connect is MySQL Workbench; for more information, go to the [ Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/) page\. For more information on using MySQL, go to the [MySQL documentation](http://dev.mysql.com/doc/)\.

 **To connect to a database on a DB instance using MySQL monitor** 

1. Find the endpoint \(DNS name\) and port number for your DB Instance\. 

   1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

   1. Choose the MySQL DB instance name to display its details\. 

   1. On the **Connectivity** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Connect to a MySQL DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQLConnect1.png)

1. Enter the following command at a command prompt on a client computer to connect to a database on a MySQL DB instance using the MySQL monitor\. Substitute the DNS name for your DB instance for *<endpoint>*, the master user name you used for *<mymasteruser>*, and provide the master password you used when prompted for a password\.

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

## Deleting a DB Instance<a name="CHAP_GettingStarted.Deleting.MySQL"></a>

Once you have connected to the sample DB instance that you created, you should delete the DB instance so you are no longer charged for it\. 

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

   If the navigation pane is closed, choose the menu icon at the top left to open it\.

1. Choose the DB instance you wish to delete\.

1. For **Actions**, choose **Delete**\.

1. For **Create final snapshot?**, choose **No**, and select the acknowledgment\. 

1. Choose **Delete**\. 