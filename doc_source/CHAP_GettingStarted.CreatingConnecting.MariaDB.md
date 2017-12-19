# Creating a MariaDB DB Instance and Connecting to a Database on a MariaDB DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.MariaDB"></a>

The easiest way to create a MariaDB DB instance is to use the Amazon RDS console\. Once you have created the DB instance, you can use command line tools such as `mysql` or standard graphical tools such as HeidiSQL to connect to a database on the DB instance\.

**Important**  
You must complete the tasks in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section before you can create or connect to a DB instance\.


+ [Creating a MariaDB Instance](#CHAP_GettingStarted.Creating.MariaDB)
+ [Connecting to a Database on a DB Instance Running the MariaDB Database Engine](#CHAP_GettingStarted.Connecting.MariaDB)
+ [Deleting a DB Instance](#CHAP_GettingStarted.Deleting.MariaDB)

## Creating a MariaDB Instance<a name="CHAP_GettingStarted.Creating.MariaDB"></a>

The basic building block of Amazon RDS is the DB instance\. This environment is where you run your MariaDB databases\.

In this example, you create a DB instance running the MariaDB database engine called *east1\-mariadb\-instance1*, with a *db\.t2\.small* DB instance class, 5 GB of storage, and automated backups enabled with a retention period of one day\.

**To create a MariaDB DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the Amazon RDS console, choose the region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Instances**\.

1. Choose **Launch DB Instance**\. The **Launch DB Instance Wizard** opens on the **Select Engine** page\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDB-Launch01.png)

1. On the **Select Engine** page, choose the MariaDB icon, and then choose **Select** for the MariaDB engine\.

1. Next, the **Production?** page asks if you plan to use the DB instance you are creating for production\. Because this is an example instance, choose **No**\. When you are finished, choose **Next**\.
**Note**  
If you create a production instance, you typically choose **Yes** on this page to enable the failover option Multi\-AZ and the Provisioned IOPS storage option\.

1. On the **Specify DB Details** page, specify your DB instance information\. The following table shows settings for an example DB instance\. When the settings are as you want them, choose **Next**\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MariaDB.html)  
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDB-Launch02.png)

1.  On the **Configure Advanced Settings** page, provide additional information that RDS needs to launch the MariaDB DB instance\. The table shows settings for an example DB instance\. Specify your DB instance information, then choose **Launch DB Instance**\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MariaDB.html)  
![\[Additional Configuration panel\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDB-Launch03.png)

1. On the RDS console, the new DB instance appears in the list of DB instances\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to a database on the DB instance\. Depending on the DB instance class and store allocated, it can take several minutes for the new DB instance to become available\.   
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDB-Launch06.png)

## Connecting to a Database on a DB Instance Running the MariaDB Database Engine<a name="CHAP_GettingStarted.Connecting.MariaDB"></a>

Once Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to a database on the DB instance\. In this example, you connect to a database on a MariaDB DB instance using the `mysql` command\-line tool\. One GUI\-based application you can use to connect is HeidiSQL; for more information, go to the [ Download HeidiSQL](http://www.heidisql.com/download.php) page\. For more information on using MariaDB, go to the [MariaDB documentation](https://mariadb.com/kb/en/mariadb/documentation/)\.

 **To connect to a database on a DB instance using the `mysql` command\-line tool** 

Type the following command at a command prompt on a client computer to connect to a database on a MariaDB DB instance\. Substitute the DNS name for your DB instance for `<endpoint>`, the master user name you used for `<mymasteruser>`, and provide the master password you used when prompted for a password\.

```
PROMPT> mysql -h <endpoint> -P 3306 -u <mymasteruser> -p <master password>
```

You should see output similar to the following\.

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

## Deleting a DB Instance<a name="CHAP_GettingStarted.Deleting.MariaDB"></a>

Once you have connected to the sample DB instance that you created, you should delete the DB instance so you are no longer charged for it\.

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. For **Instances**, choose the DB instance you want to delete\.

1. For **Instance Actions**, choose **Delete**\.

1.  For **Create final Snapshot?**, choose **No**\.

1. Choose **Yes, Delete**\. 