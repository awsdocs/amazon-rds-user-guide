# Creating a PostgreSQL DB Instance and Connecting to a Database on a PostgreSQL DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.PostgreSQL"></a>

The easiest way to create a DB instance is to use the RDS console\. After you have created the DB instance, you can use standard SQL client utilities to connect to the DB instance such as the pgAdmin utility\. In this example, you create a DB instance running the PostgreSQL database engine called west2\-postgres1, with a db\.m1\.small DB instance class, 10 GiB of storage, and automated backups enabled with a retention period of one day\.

**Important**  
Before you can create or connect to a DB instance, you must complete the tasks in [Setting Up for Amazon RDS](CHAP_SettingUp.md)\.

**Topics**
+ [Creating a PostgreSQL DB Instance](#CHAP_GettingStarted.Creating.PostgreSQL)
+ [Connecting to a PostgreSQL DB Instance](#CHAP_GettingStarted.Connecting.PostgreSQL)
+ [Deleting a DB Instance](#CHAP_GettingStarted.Deleting.PostgreSQL)

## Creating a PostgreSQL DB Instance<a name="CHAP_GettingStarted.Creating.PostgreSQL"></a>

The basic building block of Amazon RDS is the DB instance\. This environment is where you run your PostgreSQL databases\.

**Note**  
A new console interface is available for database creation\. Choose either the **New Console** or the **Original Console** instructions based on the console that you are using\. The **New Console** instructions are open by default\.

### New Console<a name="CHAP_GettingStarted.Creating.PostgreSQL.Console"></a>

You can create a DB instance running PostgreSQL with the AWS Management Console with **Easy Create** enabled or disabled\. With **Easy Create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy Create** uses the default setting for other configuration options\. With **Easy Create** not enabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

In this example, you use **Easy Create** to create a DB instance running the PostgreSQL database engine with a db\.t2\.micro DB instance class\.

**Note**  
For information about creating a PostgreSQL DB instance with **Easy Create** not enabled, see [Creating a DB Instance Running the PostgreSQL Database Engine](USER_CreatePostgreSQLInstance.md)\.

**To create a PostgreSQL DB instance with Easy Create enabled**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and make sure that **Easy Create** is chosen\.   
![\[Easy Create option\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-option.png)

1. In **Configuration**, choose **PostgreSQL**\.

1. For **DB instance size**, choose **Free tier**\.

1. For **DB instance identifier**, enter a name for the DB instance, or leave the default name\.

1. For **Master username**, enter a name for the master user, or leave the default name\.

   The **Create database** page should look similar to the following image\.  
![\[Create database page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-postgresql.png)

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
You can't view the master user password again\. If you don't record it, you might have to change it\. If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying a DB Instance Running the PostgreSQL Database Engine](USER_ModifyPostgreSQLInstance.md)\.

1. For **Databases**, choose the name of the new PostgreSQL DB instance\.

   On the RDS console, the details for new DB instance appear\. The DB instance has a status of **creating** until the DB instance is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[Screenshot of the DB instance details.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch06.png)

### Original Console<a name="CHAP_GettingStarted.Creating.PostgreSQL.CurrentConsole"></a>

**To create a DB Instance Running the PostgreSQL DB Engine**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the AWS Management Console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

   If the navigation pane is closed, choose the menu icon at the top left to open it\.

1. Choose **Create database** to open the **Select engine** page\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-Postgres-Launch01a.png)

1. On the **Select engine** page, choose the PostgreSQL icon, and then choose **Next**\.

1. Next, the **Use case** page asks if you are planning to use the DB instance you are creating for production\. If you are, choose **Production**\. If you choose this option, the failover option **Multi\-AZ** and the **Provisioned IOPS** storage options are preselected in the following step\. Choose **Next** when you are finished\.

1. On the **Specify DB Details** page, specify your DB instance information\. Choose **Next** when you are finished\.  
****    
<a name="rds-postgresql-original-console-parameter-guidance"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html)

1.  On the **Configure Advanced Settings** page, provide additional information that RDS needs to launch the PostgreSQL DB instance\. The table shows settings for an example DB instance\. Specify your DB instance information, then choose **Create database**\.  
****    
<a name="rds-postgresql-original-console-advanced-parameter-guidance"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html)

1. On the final page, choose **Create database**\. 

1. On the Amazon RDS console, the new DB instance appears in the list of DB instances\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and store allocated, it could take several minutes for the new instance to be available\.   
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-Postgres-Launch06.png)

## Connecting to a PostgreSQL DB Instance<a name="CHAP_GettingStarted.Connecting.PostgreSQL"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the instance\. The security group that you assigned to the DB instance when you created it must allow access to the DB instance\. If you have difficulty connecting to the DB instance, the problem is most often with the access rules you set up in the security group you assigned to the DB instance\.

This section shows two ways to connect to a PostgreSQL DB instance\. The first example uses pgAdmin, a popular open\-source administration and development tool for PostgreSQL\. You can download and use pgAdmin without having a local instance of PostgreSQL on your client computer\. The second example uses psql, a command line utility that is part of a PostgreSQL installation\. To use psql, you must have a PostgreSQL installed on your client computer or have installed the psql client on your machine\. 

In this example, you connect to a PostgreSQL DB instance using pgAdmin\. 

### Using pgAdmin to Connect to a PostgreSQL DB Instance<a name="CHAP_GettingStarted.Connecting.PostgreSQL.pgAdmin"></a>

**To connect to a PostgreSQL DB instance using pgAdmin**

1. Find the endpoint \(DNS name\) and port number for your DB Instance\. 

   1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

   1. Choose the PostgreSQL DB instance name to display its details\. 

   1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Connect to a PostgreSQL DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/PostgreSQL-endpoint.png)

1. Install pgAdmin from [http://www\.pgadmin\.org/](http://www.pgadmin.org/)\. You can download and use pgAdmin without having a local instance of PostgreSQL on your client computer\.

1. Launch the pgAdmin application on your client computer\.

1. Choose **Add Server** from the **File** menu\.

1. In the **New Server Registration** dialog box, enter the DB instance endpoint \(for example, `mypostgresql.c6c8dntfzzhgv0.us-west-2.rds.amazonaws.com`\) in the **Host** box\. Don't include the colon or port number as shown on the Amazon RDS console \(`mypostgresql.c6c8dntfzzhgv0.us-west-2.rds.amazonaws.com:5432`\)\. 

   Enter the port you assigned to the DB instance for **Port**\. Enter the user name and user password that you entered when you created the DB instance for **Username** and **Password**\.   
![\[Postgres connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect01.png)

1. Choose **OK**\. 

1. In the Object browser, expand **Server Groups**\. Choose the server \(the DB instance\) you created, and then choose the database name\.   
![\[Postgres connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect02.png)

1. Choose the plugin icon and choose **PSQL Console**\. The psql command window opens for the default database you created\.   
![\[Postgres connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect03.png)

1. Use the command window to enter SQL or psql commands\. Enter `\q` to close the window\.

### Using psql to Connect to a PostgreSQL DB Instance<a name="CHAP_GettingStarted.Connecting.PostgreSQL.psql"></a>

If your client computer has PostgreSQL installed, you can use a local instance of psql to connect to a PostgreSQL DB instance\. To connect to your PostgreSQL DB instance using psql, provide host information and access credentials\.

The following format is used to connect to a PostgreSQL DB instance on Amazon RDS\.

```
psql --host=<DB instance endpoint> --port=<port> --username=<master user name> --password --dbname=<database name> 
```

 For example, the following command connects to a database called `mypgdb` on a PostgreSQL DB instance called `mypostgresql` using fictitious credentials\.

```
psql --host=mypostgresql.c6c8mwvfdgv0.us-west-2.rds.amazonaws.com --port=5432 --username=awsuser --password --dbname=mypgdb 
```

### Troubleshooting Connection Issues<a name="CHAP_GettingStarted.Connecting.PostgreSQL.Troubleshooting"></a>

By far the most common problem that occurs when attempting to connect to a database on a DB instance is the access rules in the security group assigned to the DB instance\. If you used the default DB security group when you created the DB instance, chances are good that the security group did not have the rules that enable you to access the instance\. For more information about Amazon RDS security groups, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)

The most common error is could not connect to server: Connection timed out\. If you receive this error, check that the host name is the DB instance endpoint and that the port number is correct\. Check that the security group assigned to the DB instance has the necessary rules to allow access through any firewall your connection may be going through\.

## Deleting a DB Instance<a name="CHAP_GettingStarted.Deleting.PostgreSQL"></a>

After you have connected to the sample DB instance that you created, you should delete the DB instance so you are no longer charged for it\.

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. For **Create final snapshot?**, choose **No**, and select the acknowledgment\. 

1. Choose **Delete**\. 