# Creating a PostgreSQL DB instance and connecting to a database on a PostgreSQL DB instance<a name="CHAP_GettingStarted.CreatingConnecting.PostgreSQL"></a>

The easiest way to create a DB instance is to use the AWS Management Console\. After you have created the DB instance, you can use standard SQL client utilities to connect to the DB instance, such as the pgAdmin utility\. In this example, you create a DB instance running the PostgreSQL database engine called `database-1`, with a db\.r6g\.large DB instance class and 100 gibibytes \(GiB\) of storage\.

**Important**  
Before you can create or connect to a DB instance, make sure to complete the tasks in [Setting up for Amazon RDS](CHAP_SettingUp.md)\.

There's no charge for creating an AWS account\. However, by completing this tutorial, you might incur costs for the AWS resources that you use\. You can delete these resources after you complete the tutorial if they are no longer needed\.

**Contents**
+ [Creating a PostgreSQL DB instance](#CHAP_GettingStarted.Creating.PostgreSQL)
+ [Connecting an EC2 instance and an PostgreSQL DB instance automatically](#CHAP_GettingStarted.Connecting.EC2.PostgreSQL)
+ [Connecting to a PostgreSQL DB instance](#CHAP_GettingStarted.Connecting.PostgreSQL)
  + [Using pgAdmin to connect to a PostgreSQL DB instance](#CHAP_GettingStarted.Connecting.PostgreSQL.pgAdmin)
  + [Using psql to connect to a PostgreSQL DB instance](#CHAP_GettingStarted.Connecting.PostgreSQL.psql)
+ [Deleting a DB instance](#CHAP_GettingStarted.Deleting.PostgreSQL)

## Creating a PostgreSQL DB instance<a name="CHAP_GettingStarted.Creating.PostgreSQL"></a>

The basic building block of Amazon RDS is the DB instance\. This environment is where you run your PostgreSQL databases\.

You can use **Easy create** to create a DB instance running PostgreSQL with the AWS Management Console\. With **Easy create**, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy create** uses the default settings for the other configuration options\. When you use **Standard create** instead of **Easy create**, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

In this example, you use **Easy create** to create a DB instance running the PostgreSQL database engine with a db\.r6g\.large DB instance class\.

**Note**  
For information about creating DB instances with **Standard create**, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. If you want to use free tier, use **Standard create**\.

**To create a PostgreSQL DB instance with Easy create**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and make sure that **Easy create** is chosen\.   
![\[Easy create option\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-option.png)

1. In **Configuration**, choose **PostgreSQL**\.

1. For **DB instance size**, choose **Dev/Test**\.

1. For **DB instance identifier**, enter a name for the DB instance, or leave the default name\.

1. For **Master username**, enter a name for the master user, or leave the default name \(**postgres**\)\.

   The **Create database** page should look similar to the following image\.  
![\[Create database page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-postgresql.png)

1. To use an automatically generated master password for the DB instance, make sure that the **Auto generate a password** box is selected\.

   To enter your master password, clear the **Auto generate a password** box, and then enter the same password in **Master password** and **Confirm password**\.

1. \(Optional\) Open **View default settings for Easy create**\.  
![\[RDS for PostgreSQL instance settings\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-view-default-postgres.png)

   You can examine the default settings used with **Easy create**\. The **Editable after database is created** column shows which options you can change after database creation\.
   + To change settings with **No** in that column, use **Standard create**\. 
   + To change settings with **Yes** in that column, either use **Standard create**, or modify the DB instance after it is created to change the settings\.

   The following are important considerations for changing the default settings:
   + In some cases, you might want your DB instance to use a specific virtual private cloud \(VPC\) based on the Amazon VPC service\. Or you might require a specific subnet group or security group\. If so, use **Standard create** to specify these resources\. You might have created these resources when you set up for Amazon RDS\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.
   + If you want to be able to access the DB instance from a client outside of its VPC, use **Standard create** to set **Public access** to **Yes**\.

     If the DB instance should be private, leave **Public access** set to **No**\.
   + If you want to use free tier, use **Standard create** to set the PostgreSQL version lower than version 13, and then choose **Free tier** in **Templates**\.

1. Choose **Create database**\.

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB instance, choose **View credential details**\.

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\.   
If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

1. For **Databases**, choose the name of the new PostgreSQL DB instance\.

   On the RDS console, the details for new DB instance appear\. The DB instance has a status of **Creating** until the DB instance is ready to use\. When the state changes to **Available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[Screenshot of the DB instance details.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch06.png)

## Connecting an EC2 instance and an PostgreSQL DB instance automatically<a name="CHAP_GettingStarted.Connecting.EC2.PostgreSQL"></a>

You can automatically connect an existing EC2 instance to the DB instance from the RDS console\. The RDS console simplifies setting up the connection between an EC2 instance and your PostgreSQL DB instance\.

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

## Connecting to a PostgreSQL DB instance<a name="CHAP_GettingStarted.Connecting.PostgreSQL"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the instance\. Following, you can find two ways to connect to a PostgreSQL DB instance\. The first example uses pgAdmin, a popular open\-source administration and development tool for PostgreSQL\. You can download and use pgAdmin without having a local instance of PostgreSQL on your client computer\. The second example uses psql, a command line utility that is part of a PostgreSQL installation\. To use psql, make sure that you have PostgreSQL or the psql client installed on your client computer\.

Before you try connecting to the DB instance, make sure that the DB instance is associated with a security group that provides access to it\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\. 

In some cases, you might have difficulty connecting to the DB instance\. If so, the problem is most often with the access rules that you set up\. These reside in the security group that you assigned to the DB instance\. If you didn't specify the appropriate security group when you created the DB instance, you can modify the DB instance to change its security group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

If your DB instance is publicly accessible, make sure its associated security group has inbound rules for the IP addresses that you want to access it\. If your DB instance is private, make sure its associated security group has inbound rules for the security group of each resource to access it\. An example is the security group for an Amazon EC2 instance\.

For more information about connecting to a PostgreSQL DB instance, see [Connecting to a DB instance running the PostgreSQL database engine](USER_ConnectToPostgreSQLInstance.md)\. If you can't connect to your DB instance, see [Troubleshooting connections to your RDS for PostgreSQL instance](USER_ConnectToPostgreSQLInstance.md#USER_ConnectToPostgreSQLInstance.Troubleshooting)\. 

**Topics**
+ [Using pgAdmin to connect to a PostgreSQL DB instance](#CHAP_GettingStarted.Connecting.PostgreSQL.pgAdmin)
+ [Using psql to connect to a PostgreSQL DB instance](#CHAP_GettingStarted.Connecting.PostgreSQL.psql)

### Using pgAdmin to connect to a PostgreSQL DB instance<a name="CHAP_GettingStarted.Connecting.PostgreSQL.pgAdmin"></a>

**To connect to a PostgreSQL DB instance using pgAdmin**

1. Find the endpoint \(DNS name\) and port number for your DB instance\. 

   1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

   1. Choose the PostgreSQL DB instance name to display its details\. 

   1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Connect to a PostgreSQL DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/PostgreSQL-endpoint.png)

   1. On the **Configuration** tab, note the DB name\. You don't need this to connect using pgAdmin, but you do need it to connect using psql\.  
![\[Name of the database (DB name) on the Configuration tab\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/PostgreSQL-db-name.png)

1. Install pgAdmin from [https://www\.pgadmin\.org/](https://www.pgadmin.org/)\. You can download and use pgAdmin without having a local instance of PostgreSQL on your client computer\.

1. Launch the pgAdmin application on your client computer\.

1. Choose **Add Server** from the **File** menu\.

1. In the **New Server Registration** dialog box, enter the DB instance endpoint \(for example, `database-1.123456789012.us-west-1.rds.amazonaws.com`\) in the **Host** box\. Don't include the colon or port number as shown on the Amazon RDS console \(`database-1.c6c8dntfzzhgv0.us-west-1.rds.amazonaws.com:5432`\)\. 

   Enter the port you assigned to the DB instance for **Port**\. Enter the user name and user password that you entered when you created the DB instance for **Username** and **Password**\.   
![\[PostgreSQL connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect01.png)

1. Choose **OK**\. 

1. In the Object browser, expand **Server Groups**\. Choose the server \(the DB instance\) you created, and then choose the database name\.   
![\[PostgreSQL connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect02.png)

1. Choose the plugin icon and choose **PSQL Console**\. The psql command window opens for the default database you created\.   
![\[PostgreSQL connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect03.png)

1. Use the command window to enter SQL or psql commands\. Enter `\q` to close the window\.

### Using psql to connect to a PostgreSQL DB instance<a name="CHAP_GettingStarted.Connecting.PostgreSQL.psql"></a>

If your client computer has PostgreSQL installed, you can use a local instance of psql to connect to a PostgreSQL DB instance\. To connect to your PostgreSQL DB instance using psql, you provide host information, port number, access credentials, and the database name\. You can obtain these details by following the first step in the procedure for [Using pgAdmin to connect to a PostgreSQL DB instance](#CHAP_GettingStarted.Connecting.PostgreSQL.pgAdmin) 

The following format is used to connect to a PostgreSQL DB instance on Amazon RDS\.

```
psql --host=DB_instance_endpoint --port=port --username=master_user_name --password --dbname=database_name
```

 For example, the following command connects to a database called `mypgdb` on a PostgreSQL DB instance called `mypostgresql` using fictitious credentials\.

```
psql --host=database-1.123456789012.us-west-1.rds.amazonaws.com --port=5432 --username=awsuser --password --dbname=postgres
```

## Deleting a DB instance<a name="CHAP_GettingStarted.Deleting.PostgreSQL"></a>

After you connect to the sample DB instance that you created, delete it so you're no longer charged for it\.

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. For **Create final snapshot?**, choose **No**, and select the acknowledgment\. 

1. Choose **Delete**\. 