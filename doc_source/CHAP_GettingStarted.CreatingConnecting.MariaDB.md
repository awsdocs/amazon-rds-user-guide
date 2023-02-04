# Creating a MariaDB DB instance and connecting to a database on a MariaDB DB instance<a name="CHAP_GettingStarted.CreatingConnecting.MariaDB"></a>

The easiest way to create a MariaDB DB instance is to use the AWS Management Console\. After you create the DB instance, you can connect to a database on the DB instance\. To do so, you can use command line tools such as mysql or graphical tools such as HeidiSQL\.

**Important**  
Before you can create or connect to a DB instance, make sure to complete the tasks in [Setting up for Amazon RDS](CHAP_SettingUp.md)\.

**Topics**
+ [Creating a MariaDB DB instance](#CHAP_GettingStarted.Creating.MariaDB)
+ [Connecting an EC2 instance and a MariaDB instance automatically](#CHAP_GettingStarted.Connecting.EC2.MariaDB)
+ [Connecting to a database on a DB instance running the MariaDB database engine](#CHAP_GettingStarted.Connecting.MariaDB)
+ [Deleting a DB instance](#CHAP_GettingStarted.Deleting.MariaDB)

## Creating a MariaDB DB instance<a name="CHAP_GettingStarted.Creating.MariaDB"></a>

The basic building block of Amazon RDS is the DB instance\. This environment is where you run your MariaDB databases\.

You can use **Easy create** to create a DB instance running MariaDB with the AWS Management Console\. With **Easy create**, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy create** uses the default settings for the other configuration options\. When you use **Standard create** instead of **Easy create**, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

In this example, you use **Easy create** to create a DB instance running the MariaDB database engine with a db\.t2\.micro DB instance class\.

**Note**  
For information about creating DB instances with **Standard create**, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

**To create a MariaDB DB instance with Easy create**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and make sure that **Easy create** is chosen\.   
![\[Easy create option\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-option.png)

1. In **Configuration**, choose **MariaDB**\.

1. For **DB instance size**, choose **Free tier**\.

1. For **DB instance identifier**, enter a name for the DB instance, or leave the default name\.

1. For **Master username**, enter a name for the master user, or leave the default name\.

   The **Create database** page should look similar to the following image\.  
![\[Create database page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-mariadb.png)

1. To use an automatically generated master password for the DB instance, make sure that the **Auto generate a password** box is selected\.

   To enter your master password, clear the **Auto generate a password** box, and then enter the same password in **Master password** and **Confirm password**\.

1. \(Optional\) Open **View default settings for Easy create**\.  
![\[Easy create default settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-view-default-maria.png)

   You can examine the default settings used with **Easy create**\. The **Editable after database is created** column shows which options you can change after database creation\.
   + To change settings with **No** in that column, use **Standard create**\. 
   + To change settings with **Yes** in that column, either use **Standard create**, or modify the DB instance after it is created to change the settings\.

   The following are important considerations for changing the default settings:
   + In some cases, you might want your DB instance to use a specific virtual private cloud \(VPC\) based on the Amazon VPC service\. Or you might require a specific subnet group or security group\. If so, use **Standard create** to specify these resources\. You might have created these resources when you set up for Amazon RDS\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.
   + If you want to be able to access the DB instance from a client outside of its VPC, use **Standard create** to set **Public access** to **Yes**\.

     If the DB instance should be private, leave **Public access** set to **No**\.

1. Choose **Create database**\.

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB instance, choose **View credential details**\.

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\.   
If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

1. For **Databases**, choose the name of the new Maria DB instance\.

   On the RDS console, the details for new DB instance appear\. The DB instance has a status of **Creating** until the DB instance is ready to use\. When the state changes to **Available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[Summary during DB instance creation\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDB-Launch06.png)

## Connecting an EC2 instance and a MariaDB instance automatically<a name="CHAP_GettingStarted.Connecting.EC2.MariaDB"></a>

You can automatically connect an existing EC2 instance to the DB instance from the RDS console\. The RDS console simplifies setting up the connection between an EC2 instance and your MariaDB instance\.

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

## Connecting to a database on a DB instance running the MariaDB database engine<a name="CHAP_GettingStarted.Connecting.MariaDB"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to a database on the DB instance\. In this example, you connect to a database on a Maria DB instance using the mysql command\-line tool\. For more information on using MariaDB, see the [MariaDB documentation](https://mariadb.com/kb/en/mariadb/documentation/)\.

**To connect to a database on a DB instance using the mysql command\-line tool**

1. Install a SQL client that you can use to connect to the DB instance\.

   You can connect to an Amazon RDS for MariaDB DB instance by using tools such as the mysql command\-line client\. For more information on using the mysql command\-line client, see [mysql command\-line client](http://mariadb.com/kb/en/mariadb/mysql-command-line-client/) in the MariaDB documentation\. One GUI\-based application that you can use to connect is Heidi\. For more information, see the [Download HeidiSQL](http://www.heidisql.com/download.php) page\. For information about installing MySQL \(including the mysql command\-line client\), see [Installing and upgrading MySQL](https://dev.mysql.com/doc/refman/8.0/en/installing.html)\. 

1. Make sure that your DB instance is associated with a security group that provides access to it\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

   If you didn't specify the appropriate security group when you created the DB instance, you can modify the DB instance to change its security group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

   If your DB instance is publicly accessible, make sure its associated security group has inbound rules for the IP addresses that you want to access it\. If your DB instance is private, make sure its associated security group has inbound rules for the security group of each resource to access it\. An example is the security group for an Amazon EC2 instance\.

1. Find the endpoint \(DNS name\) and port number for your DB instance\. 

   1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

   1. Choose the Maria DB instance name to display its details\. 

   1. On the **Connectivity & security** tab, copy the endpoint\. Also note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Connect to a Maria DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDBConnect1.png)

1. Enter the following command at a command prompt on a client computer to connect to a database on a Maria DB instance\. Substitute the DNS name \(endpoint\) for your DB instance for `<endpoint>`\. In addition, substitute the master user name that you used for *`<mymasteruser>`* and provide the master password that you used when prompted for a password\.

   ```
   PROMPT> mysql -h <endpoint> -P 3306 -u <mymasteruser>  -p
   ```

   After you enter the password for the user, you should see output similar to the following\.

   ```
   Welcome to the MariaDB monitor.  Commands end with ; or \g.
   Your MariaDB connection id is 31
   Server version: 10.5.15-MariaDB-log Source distribution
    
   Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
     
   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
     
   MariaDB [(none)]>
   ```

For more information about connecting to a MariaDB DB instance, see [Connecting to a DB instance running the MariaDB database engine](USER_ConnectToMariaDBInstance.md)\. If you can't connect to your DB instance, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

## Deleting a DB instance<a name="CHAP_GettingStarted.Deleting.MariaDB"></a>

After you connect to the sample DB instance that you created, delete the DB instance so you're no longer charged for it\.

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance you want to delete\.

1. For **Actions**, choose **Delete**\.

1. For **Create final snapshot?**, choose **No**, and select the acknowledgment\.

1. Choose **Delete**\. 