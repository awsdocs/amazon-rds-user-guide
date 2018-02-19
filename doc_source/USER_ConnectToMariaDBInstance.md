# Connecting to a DB Instance Running the MariaDB Database Engine<a name="USER_ConnectToMariaDBInstance"></a>

Once Amazon RDS provisions your DB instance, you can use any standard MariaDB client application or utility to connect to the instance\. In the connection string, you specify the DNS address from the DB instance endpoint as the host parameter, and specify the port number from the DB instance endpoint as the port parameter\.

You can use the AWS Management Console, the AWS CLI [describe\-db\-instances](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command, or the Amazon RDS API [DescribeDBInstances](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) action to list the details of an Amazon RDS DB instance, including its endpoint\.

To find the endpoint for a MariaDB instance in the AWS Management Console:

1. Open the RDS console and then choose **Instances** to display a list of your DB instances\. 

1. Choose the MariaDB instance and choose **See details** from **Instance actions** to display the details for the DB instance\. 

1. Scroll to the **Connect** section and copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Connect to a MariaDB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDBConnect1.png)

If an endpoint value is `mariadb-instance1.123456789012.us-east-1.rds.amazonaws.com:3306`, then you specify the following values in a MariaDB connection string:

+ For host or host name, specify `mariadb-instance1.123456789012.us-east-1.rds.amazonaws.com`

+ For port, specify `3306`

You can connect to an Amazon RDS MariaDB DB instance by using tools like the `mysql` command line utility\. For more information on using the `mysql` utility, go to [mysql Command\-line Client](http://mariadb.com/kb/en/mariadb/mysql-command-line-client/) in the MariaDB documentation\. One GUI\-based application you can use to connect is HeidiSQL; for more information, go to the [ Download HeidiSQL](http://www.heidisql.com/download.php) page\.

Two common causes of connection failures to a new DB instance are the following:

+ The DB instance was created using a security group that does not authorize connections from the device or Amazon EC2 instance where the MariaDB application or utility is running\. If the DB instance was created in an Amazon VPC, it must have a VPC security group that authorizes the connections\. If the DB instance was created outside of a VPC, it must have a DB security group that authorizes the connections\.

+ The DB instance was created using the default port of 3306, and your company has firewall rules blocking connections to that port from devices in your company network\. To fix this failure, recreate the instance with a different port\.

You can use SSL encryption on connections to an Amazon RDS MariaDB DB instance\. For information, see [Using SSL with a MariaDB DB Instance](CHAP_MariaDB.md#MariaDB.Concepts.SSLSupport)\.

## Connecting from the mysql Utility<a name="USER_ConnectToMariaDBInstance.CLI"></a>

To connect to a DB instance using the mysql utility, type the following command at a command prompt on a client computer to connect to a database on a MariaDB DB instance\. Substitute the DNS name \(endpoint\) for your DB instance for *<endpoint>*, the master user name you used for *<mymasteruser>*, and provide the master password you used when prompted for a password\.

```
mysql -h <endpoint> -P 3306 -u <mymasteruser> -p
```

You will see output similar to the following\.

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

## Connecting with SSL<a name="USER_ConnectToMariaDBInstanceSSL.CLI"></a>

Amazon RDS creates an SSL certificate for your DB instance when the instance is created\. If you enable SSL certificate verification, then the SSL certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL certificate to guard against spoofing attacks\. To connect to your DB instance using SSL, follow these steps:

**To connect to a DB instance with SSL using the mysql utility**

1.  Download a root certificate that works for all regions from [here](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-root.pem)\.

1. Type the following command at a command prompt to connect to a DB instance with SSL using the `mysql` utility\. For the `-h` parameter, substitute the DNS name for your DB instance\. For the `--ssl-ca` parameter, substitute the SSL certificate file name as appropriate\.

   ```
   mysql -h mariadb-instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=rds-ca-2015-root.pem 
   ```

1. Include the `--ssl-verify-server-cert` parameter so that the SSL connection verifies the DB instance endpoint against the endpoint in the SSL certificate\. For example:

   ```
   mysql -h mariadb-instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=rds-ca-2015-root.pem --ssl-verify-server-cert 
   ```

1. Type the master user password when prompted\.

You will see output similar to the following\.

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

## Maximum MariaDB Connections<a name="USER_ConnectToMariaDBInstance.max_connections"></a>

The maximum number of connections allowed to an Amazon RDS MariaDB DB instance is based on the amount of memory available for the DB instance class of the DB instance\. A DB instance class with more memory available results in a larger number of connections available\. For more information on DB instance classes, see [DB Instance Class](Concepts.DBInstanceClass.md)\.

The connection limit for a DB instance is set by default to the maximum for the DB instance class for the DB instance\. You can limit the number of concurrent connections to any value up to the maximum number of connections allowed using the `max_connections` parameter in the parameter group for the DB instance\. For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

You can retrieve the maximum number of connections allowed for an Amazon RDS MariaDB DB instance by executing the following query on your DB instance:

```
SELECT @@max_connections;
```

You can retrieve the number of active connections to an Amazon RDS MariaDB DB instance by executing the following query on your DB instance:

```
SHOW STATUS WHERE `variable_name` = 'Threads_connected';
```

## Related Topics<a name="USER_ConnectToMariaDBInstance.related"></a>

+  [Amazon RDS DB Instances](Overview.DBInstance.md) 

+  [Creating a DB Instance Running the MariaDB Database Engine](USER_CreateMariaDBInstance.md) 

+  [Amazon RDS Security Groups](Overview.RDSSecurityGroups.md) 

+  [Deleting a DB Instance](USER_DeleteInstance.md) 