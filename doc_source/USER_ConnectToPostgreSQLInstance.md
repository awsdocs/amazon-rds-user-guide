# Connecting to a DB Instance Running the PostgreSQL Database Engine<a name="USER_ConnectToPostgreSQLInstance"></a>

Once Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the instance\. It is important to note that the security group you assigned to the DB instance when you created it must allow access to the DB instance\. If you have difficulty connecting to the DB instance, the problem is most often with the access rules you set up in the security group you assigned to the DB instance\.

You can use the AWS Management Console, the AWS CLI [describe\-db\-instances](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command, or the Amazon RDS API [DescribeDBInstances](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) action to list the details of an Amazon RDS DB instance, including its endpoint\. If an endpoint value is `myinstance.123456789012.us-east-1.rds.amazonaws.com:5432`, then you would specify the following values in a PostgreSQL connection string:

+ For host or host name, specify 

  ```
  myinstance.123456789012.us-east-1.rds.amazonaws.com
  ```

+ For port, specify 

  ```
  5432
  ```

Two common causes of connection failures to a new DB instance are:

+ The DB instance was created using a security group that does not authorize connections from the device or Amazon EC2 instance where the PostgreSQL application or utility is running\. If the DB instance was created in a VPC, it must have a VPC security group that authorizes the connections\. If the DB instance was created outside of a VPC, it must have a DB security group that authorizes the connections\.

+ The DB instance was created using the default port of 5432, and your company has firewall rules blocking connections to that port from devices in your company network\. To fix this failure, modify the instance to use a different port\.

This section shows two ways to connect to a PostgreSQL DB instance\. The first example uses *pgAdmin*, a popular Open Source administration and development tool for PostgreSQL\. You can download and use *pgAdmin* without having a local instance of PostgreSQL on your client computer\. The second example uses *psql*, a command line utility that is part of a PostgreSQL installation\. To use *psql*, you must have a PostgreSQL installed on your client computer or have installed the *psql* client on your machine\. 

In this example, you connect to a PostgreSQL DB instance using pgAdmin\. 

## Using pgAdmin to Connect to a PostgreSQL DB Instance<a name="USER_ConnectToPostgreSQLInstance.pgAdmin"></a>

 **To connect to a PostgreSQL DB instance using pgAdmin** 

1. Launch the *pgAdmin* application on your client computer\. You can install *pgAdmin* from [http://www\.pgadmin\.org/](http://www.pgadmin.org/)\.

1. Select **Add Server** from the **File** menu\.

1. In the **New Server Registration** dialog box, enter the DB instance endpoint \(for example, mypostgresql\.c6c8dntfzzhgv0\.us\-west\-2\.rds\.amazonaws\.com\) in the **Host** text box\. Do not include the colon or port number as shown on the Amazon RDS console \(mypostgresql\.c6c8dntfzzhgv0\.us\-west\-2\.rds\.amazonaws\.com*:5432*\)\. 

   Enter the port you assigned to the DB instance into the **Port** text box\. Enter the user name and user password you entered when you created the DB instance into the **Username** and **Password** text boxes, respectively\.  
![\[Postgres connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect01.png)

1. Click **OK**\. 

1. In the **Object browser**, expand the **Server Groups**\. Select the Server \(the DB instance\) you created, and then select the database name\.  
![\[Postgres connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect02.png)

1. Click the plugin icon and click **PSQL Console**\. The *psql* command window opens for the default database you created\.  
![\[Postgres connect\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect03.png)

1. Use the command window to enter SQL or *psql* commands\. Type `\q` to close the window\.

## Using *psql* to Connect to a PostgreSQL DB Instance<a name="USER_ConnectToPostgreSQLInstance.psql"></a>

If your client computer has PostgreSQL installed, you can use a local instance of *psql* to connect to a PostgreSQL DB instance\. To connect to your PostgreSQL DB instance using *psql*, you need to provide host information and access credentials\.

The following format is used to connect to a PostgreSQL DB instance on Amazon RDS\. Note that you will be prompted for a password; use the \-\-no\-password option for batch jobs or scripts\.

For Linux, OS X, or Unix:

```
psql \
   --host=<DB instance endpoint> \
   --port=<port> \
   --username <master user name> \
   --password \
   --dbname=<database name>
```

For Windows:

```
psql ^
   --host=<DB instance endpoint> ^
   --port=<port> ^
   --username <master user name> ^
   --password ^
   --dbname=<database name>
```

 For example, the following command connects to a database called `mypgdb` on a PostgreSQL DB instance called `mypostgresql` using fictitious credentials: 

```
psql --host=mypostgresql.c6c8mwvfdgv0.us-west-2.rds.amazonaws.com --port=5432 --username=awsuser --password --dbname=mypgdb 
```

## Troubleshooting Connection Issues<a name="USER_ConnectToPostgreSQLInstance.Troubleshooting"></a>

By far the most common problem that occurs when attempting to connect to a database on a DB instance is the access rules in the security group assigned to the DB instance\. If you used the default DB security group when you created the DB instance, chances are good that the security group did not have the rules that will allow you to access the instance\. For more information about Amazon RDS security groups, see [Amazon RDS Security Groups](Overview.RDSSecurityGroups.md)

The most common error is *could not connect to server: Connection timed out*\. If you receive this error, check that the host name is the DB instance endpoint and that the port number is correct\. Check that the DB security group assigned to the DB instance has the necessary rules to allow access through any firewall your connection may be going through\.

## Related Topics<a name="USER_ConnectToPostgreSQLInstance.related"></a>

+  [Amazon RDS DB Instances](Overview.DBInstance.md) 

+  [Creating a DB Instance Running the PostgreSQL Database Engine](USER_CreatePostgreSQLInstance.md) 

+  [Amazon RDS Security Groups](Overview.RDSSecurityGroups.md) 

+  [Deleting a DB Instance](USER_DeleteInstance.md) 