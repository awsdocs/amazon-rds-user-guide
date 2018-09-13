# Creating a PostgreSQL DB Instance and Connecting to a Database on a PostgreSQL DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.PostgreSQL"></a>

The easiest way to create a DB instance is to use the RDS console\. Once you have created the DB instance, you can use standard SQL client utilities to connect to the DB instance such as the pgAdmin utility\. In this example, you create a DB instance running the PostgreSQL database engine called west2\-postgres1, with a db\.m1\.small DB instance class, 10 GiB of storage, and automated backups enabled with a retention period of one day\.

**Important**  
You must complete the tasks in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section before you can create or connect to a DB instance\.

**Topics**
+ [Creating a PostgreSQL DB Instance](#CHAP_GettingStarted.Creating.PostgreSQL)
+ [Connecting to a PostgreSQL DB Instance](#CHAP_GettingStarted.Connecting.PostgreSQL)
+ [Deleting a DB Instance](#CHAP_GettingStarted.Deleting.PostgreSQL)

## Creating a PostgreSQL DB Instance<a name="CHAP_GettingStarted.Creating.PostgreSQL"></a>

**To create a DB Instance Running the PostgreSQL DB Engine**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the AWS Management Console, choose the region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Instances**\.

1. Choose **Launch DB Instance** to start the **Launch DB Instance Wizard**\.

    The wizard opens on the **Select Engine** page\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch01a.png)

1. On the **Select Engine** page, choose the PostgreSQL icon, and then choose **Next**\.

1. Next, the **Use case** page asks if you are planning to use the DB instance you are creating for production\. If you are, choose **Production**\. If you choose this option, the failover option **Multi\-AZ** and the **Provisioned IOPS** storage options are preselected in the following step\. Choose **Next** when you are finished\.

1. On the **Specify DB Details** page, specify your DB instance information\. Choose **Next** when you are finished\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html)

1.  On the **Configure Advanced Settings** page, provide additional information that RDS needs to launch the PostgreSQL DB instance\. The table shows settings for an example DB instance\. Specify your DB instance information, then choose **Launch DB Instance**\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html)

1.  On the final page of the wizard, choose **Launch DB instance**\. 

1. On the Amazon RDS console, the new DB instance appears in the list of DB instances\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and store allocated, it could take several minutes for the new instance to be available\.   
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch06.png)

## Connecting to a PostgreSQL DB Instance<a name="CHAP_GettingStarted.Connecting.PostgreSQL"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the instance\. It is important to note that the security group you assigned to the DB instance when you created it must allow access to the DB instance\. If you have difficulty connecting to the DB instance, the problem is most often with the access rules you set up in the security group you assigned to the DB instance\.

This section shows two ways to connect to a PostgreSQL DB instance\. The first example uses *pgAdmin*, a popular Open Source administration and development tool for PostgreSQL\. You can download and use *pgAdmin* without having a local instance of PostgreSQL on your client computer\. The second example uses *psql*, a command line utility that is part of a PostgreSQL installation\. To use *psql*, you must have a PostgreSQL installed on your client computer or have installed the *psql* client on your machine\. 

In this example, you connect to a PostgreSQL DB instance using pgAdmin\. 

### Using pgAdmin to Connect to a PostgreSQL DB Instance<a name="CHAP_GettingStarted.Connecting.PostgreSQL.pgAdmin"></a>

**To connect to a PostgreSQL DB instance using pgAdmin**

1. Launch the *pgAdmin* application on your client computer\. You can install *pgAdmin* from [http://www\.pgadmin\.org/](http://www.pgadmin.org/)\.

1. Choose **Add Server** from the **File** menu\.

1. In the **New Server Registration** dialog box, enter the DB instance endpoint \(for example, mypostgresql\.c6c8dntfzzhgv0\.us\-west\-2\.rds\.amazonaws\.com\) in the **Host** box\. Do not include the colon or port number as shown on the Amazon RDS console \(mypostgresql\.c6c8dntfzzhgv0\.us\-west\-2\.rds\.amazonaws\.com*:5432*\)\. 

   Enter the port you assigned to the DB instance into the **Port** box\. Enter the user name and user password you entered when you created the DB instance into the **Username** and **Password** boxes, respectively\.   
![\[Postgres connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect01.png)

1. Choose **OK**\. 

1. In the **Object browser**, expand the **Server Groups**\. Choose the Server \(the DB instance\) you created, and then choose the database name\.   
![\[Postgres connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect02.png)

1. Choose the plugin icon and choose **PSQL Console**\. The *psql* command window opens for the default database you created\.   
![\[Postgres connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect03.png)

1. Use the command window to enter SQL or *psql* commands\. Type `\q` to close the window\.

### Using *psql* to Connect to a PostgreSQL DB Instance<a name="CHAP_GettingStarted.Connecting.PostgreSQL.psql"></a>

If your client computer has PostgreSQL installed, you can use a local instance of *psql* to connect to a PostgreSQL DB instance\. To connect to your PostgreSQL DB instance using *psql*, you need to provide host information and access credentials\.

The following format is used to connect to a PostgreSQL DB instance on Amazon RDS:

```
psql --host=<DB instance endpoint> --port=<port> --username=<master user name> --password --dbname=<database name> 
```

 For example, the following command connects to a database called `mypgdb` on a PostgreSQL DB instance called `mypostgresql` using fictitious credentials: 

```
psql --host=mypostgresql.c6c8mwvfdgv0.us-west-2.rds.amazonaws.com --port=5432 --username=awsuser --password --dbname=mypgdb 
```

### Troubleshooting Connection Issues<a name="CHAP_GettingStarted.Connecting.PostgreSQL.Troubleshooting"></a>

By far the most common problem that occurs when attempting to connect to a database on a DB instance is the access rules in the security group assigned to the DB instance\. If you used the default DB security group when you created the DB instance, chances are good that the security group did not have the rules that allow you to access the instance\. For more information about Amazon RDS security groups, see [Controlling Access with Amazon RDS Security Groups](Overview.RDSSecurityGroups.md)

The most common error is *could not connect to server: Connection timed out*\. If you receive this error, check that the host name is the DB instance endpoint and that the port number is correct\. Check that the security group assigned to the DB instance has the necessary rules to allow access through any firewall your connection may be going through\.

## Deleting a DB Instance<a name="CHAP_GettingStarted.Deleting.PostgreSQL"></a>

Once you have connected to the sample DB instance that you created, you should delete the DB instance so you are no longer charged for it\.

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the DB instance you wish to delete\.

1. Choose **Instance actions**, and then choose **Delete**\.

1. For **Create final snapshot?**, choose **No**, and select the acknowledgment\. 

1. Choose **Delete**\. 