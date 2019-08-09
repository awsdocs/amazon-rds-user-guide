# Connecting to a DB Instance Running the MySQL Database Engine<a name="USER_ConnectToInstance"></a>

 Before you can connect to a DB instance running the MySQL database engine, you must create a DB instance\. For information, see [Creating a DB Instance Running the MySQL Database Engine](USER_CreateInstance.md)\. After Amazon RDS provisions your DB instance, you can use any standard MySQL client application or utility to connect to the instance\. In the connection string, you specify the DNS address from the DB instance endpoint as the host parameter, and specify the port number from the DB instance endpoint as the port parameter\. 

To authenticate to your RDS DB instance, you can use one of the authentication methods for MySQL and IAM database authentication\.
+ To learn how to authenticate to MySQL using one of the authentication methods for MySQL, see [ Authentication Method](https://dev.mysql.com/doc/internals/en/authentication-method.html) in the MySQL documentation\.
+ To learn how to authenticate to MySQL using IAM database authentication, see [IAM Database Authentication for MySQL and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\.

You can use the AWS Management Console, the AWS CLI [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command, or the Amazon RDS API [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) operation to list the details of an Amazon RDS DB instance, including its endpoint\. 

To find the endpoint for a MySQL DB instance in the AWS Management Console:

1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

1. Choose the MySQL DB instance name to display its details\. 

1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Connect to a MySQL DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQLConnect1.png)

If an endpoint value is `mysql–instance1.123456789012.us-east-1.rds.amazonaws.com` and the port value is `3306`, then you would specify the following values in a MySQL connection string:
+ For host or host name, specify `mysql–instance1.123456789012.us-east-1.rds.amazonaws.com`
+ For port, specify `3306`

You can connect to an Amazon RDS MySQL DB instance by using tools like the MySQL command line utility\. For more information on using the MySQL client, go to [mysql \- The MySQL Command Line Tool](http://dev.mysql.com/doc/refman/5.6/en/mysql.html) in the MySQL documentation\. One GUI\-based application you can use to connect is MySQL Workbench\. For more information, go to the [Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/) page\. For information about installing MySQL \(including the MySQL client\), see [Installing and Upgrading MySQL](https://dev.mysql.com/doc/refman/8.0/en/installing.html)\. 

Two common causes of connection failures to a new DB instance are:
+ The DB instance was created using a security group that does not authorize connections from the device or Amazon EC2 instance where the MySQL application or utility is running\. If the DB instance was created in a VPC, it must have a VPC security group that authorizes the connections\. If the DB instance was created outside of a VPC, it must have a DB security group that authorizes the connections\. For more information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.
+ The DB instance was created using the default port of 3306, and your company has firewall rules blocking connections to that port from devices in your company network\. To fix this failure, recreate the instance with a different port\.

You can use SSL encryption on connections to an Amazon RDS MySQL DB instance\. For information, see [Using SSL with a MySQL DB Instance](CHAP_MySQL.md#MySQL.Concepts.SSLSupport)\. If you are using IAM database authentication, you must use an SSL connection\. For information, see [IAM Database Authentication for MySQL and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. 

For information on connecting to a MariaDB DB instance, see [Connecting to a DB Instance Running the MariaDB Database Engine](USER_ConnectToMariaDBInstance.md)\.

## Connecting from the MySQL Client<a name="USER_ConnectToInstance.CLI"></a>

To connect to a DB instance using the MySQL client, type the following command at a command prompt to connect to a DB instance using the MySQL client\. For the \-h parameter, substitute the DNS name \(endpoint\) for your DB instance\. For the \-P parameter, substitute the port for your DB instance\. Enter the master user password when prompted\. 

```
mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com -P 3306 -u mymasteruser -p
```

After you enter the password for the user, you will see output similar to the following\.

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 350
Server version: 5.6.40-log MySQL Community Server (GPL)

Type 'help;' or '\h' for help. Type '\c' to clear the buffer.

mysql>
```

## Connecting with SSL<a name="USER_ConnectToInstanceSSL.CLI"></a>

Amazon RDS creates an SSL certificate for your DB instance when the instance is created\. If you enable SSL certificate verification, then the SSL certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL certificate to guard against spoofing attacks\. To connect to your DB instance using SSL, you can use native password authentication or IAM database authentication\. To connect to your DB instance using IAM database authentication, see [IAM Database Authentication for MySQL and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. To connect to your DB instance using native password authentication, you can follow these steps: 

**To connect to a DB instance with SSL using the MySQL client**

1. A root certificate that works for all regions can be downloaded [here](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-root.pem)\. 

1. Enter the following command at a command prompt to connect to a DB instance with SSL using the MySQL client\. For the \-h parameter, substitute the DNS name for your DB instance\. For the \-\-ssl\-ca parameter, substitute the SSL certificate file name as appropriate\. 

   ```
   mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=rds-ca-2015-root.pem -p
   ```

1. You can require that the SSL connection verifies the DB instance endpoint against the endpoint in the SSL certificate\. 

   For MySQL 5\.7 and later:

   ```
   mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=rds-ca-2015-root.pem --ssl-mode=VERIFY_IDENTITY -p
   ```

   For MySQL 5\.6 and earlier:

   ```
   mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=rds-ca-2015-root.pem --ssl-verify-server-cert -p
   ```

1. Enter the master user password when prompted\.

You will see output similar to the following\.

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 350
Server version: 5.6.40-log MySQL Community Server (GPL)

Type 'help;' or '\h' for help. Type '\c' to clear the buffer.

mysql>
```

## Connecting from MySQL Workbench<a name="USER_ConnectToInstance.MySQLWorkbench"></a>

**To connect from MySQL Workbench**

1. Download and install MySQL Workbench at [Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/)\.

1. Open MySQL Workbench\.  
![\[MySQL Workbench\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/mysql-workbench-main.png)

1. From **Database**, choose **Manage Connections**\.

1. In the **Manage Server Connections** window, choose **New**\.

1. In the **Connect to Database** window, enter the following information:
   + **Stored Connection** – Enter a name for the connection, such as **MyDB**\.
   + **Hostname** – Enter the DB instance endpoint\.
   + **Port** – Enter the port used by the DB instance\.
   + **Username** – Enter the username of a valid database user, such as the master user\.
   + **Password** – Optionally, choose **Store in Vault** and then enter and save the password for the user\.

   The window looks similar to the following:  
![\[MySQL Workbench Connection window\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/mysql-workbench-connect.png)

1. Optionally, choose **Test Connection** to confirm that the connection to the DB instance is successful\.

1. Choose **Close**\.

1. From **Database**, choose **Connect to Database**\.

1. From **Stored Connection**, choose your connection\.

1. Choose **OK**\.

For information about using MySQL Workbench, see the [MySQL Workbench documentation](https://dev.mysql.com/doc/workbench/en/)\.

## Maximum MySQL connections<a name="USER_ConnectToInstance.max_connections"></a>

The maximum number of connections allowed to an Amazon RDS MySQL DB instance is based on the amount of memory available for the DB instance class of the DB instance\. A DB instance class with more memory available will result in a larger amount of connections available\. For more information on DB instance classes, see [Choosing the DB Instance Class](Concepts.DBInstanceClass.md)\.

The connection limit for a DB instance is set by default to the maximum for the DB instance class for the DB instance\. You can limit the number of concurrent connections to any value up to the maximum number of connections allowed using the `max_connections` parameter in the parameter group for the DB instance\. For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

You can retrieve the maximum number of connections allowed for an Amazon RDS MySQL DB instance by executing the following query on your DB instance:

```
SELECT @@max_connections;
```

You can retrieve the number of active connections to an Amazon RDS MySQL DB instance by executing the following query on your DB instance:

```
SHOW STATUS WHERE `variable_name` = 'Threads_connected';
```

## <a name="USER_ConnectToInstance.related"></a>