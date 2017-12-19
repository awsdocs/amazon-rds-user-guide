# Creating a MySQL DB Instance and Connecting to a Database on a MySQL DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.MySQL"></a>

The easiest way to create a DB instance is to use the AWS Management Console\. Once you have created the DB instance, you can use standard MySQL utilities such as MySQL Workbench to connect to a database on the DB instance\.

**Important**  
You must complete the tasks in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section before you can create or connect to a DB instance\.


+ [Creating a MySQL DB Instance](#CHAP_GettingStarted.Creating.MySQL)
+ [Connecting to a Database on a DB Instance Running the MySQL Database Engine](#CHAP_GettingStarted.Connecting.MySQL)
+ [Deleting a DB Instance](#CHAP_GettingStarted.Deleting.MySQL)

## Creating a MySQL DB Instance<a name="CHAP_GettingStarted.Creating.MySQL"></a>

 The basic building block of Amazon RDS is the DB instance\. This is the environment in which you run your MySQL databases\. 

In this example, you create a DB instance running the MySQL database engine called *west2\-mysql\-instance1*, with a *db\.m1\.small* DB instance class, 5 GB of storage, and automated backups enabled with a retention period of one day\.

**To create a MySQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the Amazon RDS console, choose the region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Instances**\.

1. Choose **Launch DB Instance**\. The **Launch DB Instance Wizard** opens on the **Select Engine** page\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch01.png)

1. On the **Select Engine** page, choose the MySQL icon and then choose **Select** for the MySQL DB engine\.

1. On the **Specify DB Details** page, specify your DB instance information\. The following table shows settings for an example DB instance\. When the settings are as you want them, choose **Next**\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MySQL.html)  
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch02.png)

1.  On the **Configure Advanced Settings** page, provide additional information that RDS needs to launch the MySQL DB instance\. The table shows settings for an example DB instance\. Specify your DB instance information, then choose **Launch DB Instance**\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MySQL.html)  
![\[Additional Configuration panel\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch03.png)

1. On the RDS console, the new DB instance appears in the list of DB instances\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to a database on the DB instance\. Depending on the DB instance class and store allocated, it could take several minutes for the new DB instance to become available\.   
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch06.png)

## Connecting to a Database on a DB Instance Running the MySQL Database Engine<a name="CHAP_GettingStarted.Connecting.MySQL"></a>

Once Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to a database on the DB instance\. In this example, you connect to a database on a MySQL DB instance using MySQL monitor commands\. One GUI\-based application you can use to connect is MySQL Workbench; for more information, go to the [ Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/) page\. For more information on using MySQL, go to the [MySQL documentation](http://dev.mysql.com/doc/)\.

 **To connect to a database on a DB instance using MySQL monitor** 

+ Type the following command at a command prompt on a client computer to connect to a database on a MySQL DB instance using the MySQL monitor\. Substitute the DNS name for your DB instance for <endpoint>, the master user name you used for <mymasteruser>, and the master password you used for <password>\.

  ```
  PROMPT> mysql -h <endpoint> -P 3306 -u <mymasteruser> -p
  ```

  You should see output similar to the following\.

  ```
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 350
  Server version: 5.6.27-log MySQL Community Server (GPL)
  
  Type 'help;' or '\h' for help. Type '\c' to clear the buffer.
  
  mysql>
  ```

## Deleting a DB Instance<a name="CHAP_GettingStarted.Deleting.MySQL"></a>

Once you have connected to the sample DB instance that you created, you should delete the DB instance so you are no longer charged for it\. 

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the **Instances** list, choose the DB instance you wish to delete\.

1. Choose **Instance Actions**, and then choose **Delete**\.

1.  Choose **No** for **Create final Snapshot?** 

1. Choose **Yes, Delete**\. 