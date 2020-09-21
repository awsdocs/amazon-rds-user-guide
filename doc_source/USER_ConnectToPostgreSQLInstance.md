# Connecting to a DB Instance Running the PostgreSQL Database Engine<a name="USER_ConnectToPostgreSQLInstance"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the instance\. To list the details of an Amazon RDS DB instance, you can use the AWS Management Console, the AWS CLI [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command, or the Amazon RDS API [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) operation\. You need the following information to connect:
+ The host or host name for the DB instance, for example: 

  ```
  myinstance.123456789012.us-east-1.rds.amazonaws.com
  ```
+ The port on which the DB instance is listening\. For example, the default PostgreSQL port is 5432\. 
+ The user name and password for the DB instance\.

Following are two ways to connect to a PostgreSQL DB instance\. The first example uses pgAdmin, a popular open\-source administration and development tool for PostgreSQL\. The second example uses psql, a command line utility that is part of a PostgreSQL installation\. 

## Using pgAdmin to Connect to a PostgreSQL DB Instance<a name="USER_ConnectToPostgreSQLInstance.pgAdmin"></a>

You can use the open\-source tool pgAdmin to connect to a PostgreSQL DB instance\. 

**To connect to a PostgreSQL DB instance using pgAdmin**

1. Find the endpoint \(DNS name\) and port number for your DB Instance\. 

   1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

   1. Choose the PostgreSQL DB instance name to display its details\. 

   1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Connect to a PostgreSQL DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/PostgreSQL-endpoint.png)

1. Install pgAdmin from [http://www\.pgadmin\.org/](http://www.pgadmin.org/)\. You can download and use pgAdmin without having a local instance of PostgreSQL on your client computer\.

1. Launch the pgAdmin application on your client computer\. 

1. On the **Dashboard** tab, choose **Add New Server**\.

1. In the **Create \- Server** dialog box, type a name on the **General** tab to identify the server in pgAdmin\.

1. On the **Connection** tab, type the following information from your DB instance:
   + For **Host**, type the endpoint, for example `mypostgresql.c6c8dntfzzhgv0.us-east-2.rds.amazonaws.com`\.
   + For **Port**, type the assigned port\. 
   + For **Username**, type the user name that you entered when you created the DB instance\.
   + For **Password**, type the password that you entered when you created the DB instance\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect01.png)

1. Choose **Save**\. 

   If you have any problems connecting, see [Troubleshooting Connections to Your PostgreSQL Instance](#USER_ConnectToPostgreSQLInstance.Troubleshooting)\. 

1. To access a database in the pgAdmin browser, expand **Servers**, the DB instance, and **Databases**\. Choose the DB instance's database name\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect02.png)

1. To open a panel where you can enter SQL commands, choose **Tools**, **Query Tool**\. 

## Using psql to Connect to a PostgreSQL DB Instance<a name="USER_ConnectToPostgreSQLInstance.psql"></a>

You can use a local instance of the psql command line utility to connect to a PostgreSQL DB instance\. You need either PostgreSQL or the psql client installed on your client computer\. To connect to your PostgreSQL DB instance using psql, you need to provide host information and access credentials\.

Use one of the following formats to connect to a PostgreSQL DB instance on Amazon RDS\. When you connect, you're prompted for a password\. For batch jobs or scripts, use the `--no-password` option\.

If this is the first time you are connecting to this DB instance, try using the default database name **postgres** for the `--dbname` option\. 

For Unix, use the following format\.

```
psql \
   --host=<DB instance endpoint> \
   --port=<port> \
   --username=<master username> \
   --password \
   --dbname=<database name>
```

For Windows, use the following format\.

```
psql ^
   --host=<DB instance endpoint> ^
   --port=<port> ^
   --username=<master username> ^
   --password ^
   --dbname=<database name>
```

For example, the following command connects to a database called `mypgdb` on a PostgreSQL DB instance called `mypostgresql` using fictitious credentials\. 

```
psql --host=mypostgresql.c6c8mwvfdgv0.us-west-2.rds.amazonaws.com --port=5432 --username=awsuser --password --dbname=mypgdb 
```

## Troubleshooting Connections to Your PostgreSQL Instance<a name="USER_ConnectToPostgreSQLInstance.Troubleshooting"></a>

If you can't connect to the DB instance, the most common error is `Could not connect to server: Connection timed out.` If you receive this error, do the following:
+ Check that the host name used is the DB instance endpoint and that the port number used is correct\.
+ Make sure that the DB instance's public accessibility is set to **Yes**\.
+ Check that the security group assigned to the DB instance has rules to allow access through any firewall your connection might go through\. For example, if the DB instance was created using the default port of 5432, your company might have firewall rules blocking connections to that port from company devices\.

  To fix this failure, modify the DB instance to use a different port\. Also, make sure that the security group applied to the DB instance allows connections to the new port\.
+ Check whether the DB instance was created using a security group that doesn't authorize connections from the device or Amazon EC2 instance where the application is running\. For the connection to work, the security group you assigned to the DB instance at its creation must allow access to the DB instance\. For example, if the DB instance was created in a VPC, it must have a VPC security group that authorizes connections\.

  You can add or edit an inbound rule in the security group\. For **Source**, choosing **My IP** allows access to the DB instance from the IP address detected in your browser\. For more information, see [Provide Access to Your DB Instance in Your VPC by Creating a Security Group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

  Alternatively, if the DB instance was created outside of a VPC, it must have a database security group that authorizes those connections\.

By far the most common connection problem is with the security group's access rules assigned to the DB instance\. If you used the default DB security group when you created the DB instance, the security group likely didn't have access rules that allow you to access the instance\. For more information about Amazon RDS security groups, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\.

If you receive an error like `FATAL: database some-name does not exist` when connecting, try using the default database name **postgres** for the `--dbname` option\. 