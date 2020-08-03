# Connecting to a DB Instance Running the MySQL Database Engine<a name="USER_ConnectToInstance"></a>

 Before you can connect to a DB instance running the MySQL database engine, you must create a DB instance\. For information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. After Amazon RDS provisions your DB instance, you can use any standard MySQL client application or utility to connect to the instance\. In the connection string, you specify the DNS address from the DB instance endpoint as the host parameter, and specify the port number from the DB instance endpoint as the port parameter\. 

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

You can connect to an Amazon RDS MySQL DB instance by using tools like the MySQL command line utility\. For more information on using the MySQL client, go to [mysql — The MySQL Command\-Line Client](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) in the MySQL documentation\. One GUI\-based application you can use to connect is MySQL Workbench\. For more information, go to the [Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/) page\. For information about installing MySQL \(including the MySQL client\), see [Installing and Upgrading MySQL](https://dev.mysql.com/doc/refman/8.0/en/installing.html)\. 

To connect to a DB instance from outside of its Amazon VPC, the DB instance must be publicly accessible, access must be granted using the inbound rules of the DB instance's security group, and other requirements must be met\. For more information, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

You can use SSL encryption on connections to an Amazon RDS MySQL DB instance\. For information, see [Using SSL with a MySQL DB Instance](CHAP_MySQL.md#MySQL.Concepts.SSLSupport)\. If you are using IAM database authentication, you must use an SSL connection\. For information, see [IAM Database Authentication for MySQL and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. 

You can also connect to a DB instance from a web server\. For more information, see [Tutorial: Create a Web Server and an Amazon RDS DB Instance](TUT_WebAppWithRDS.md)\.

**Note**  
For information on connecting to a MariaDB DB instance, see [Connecting to a DB Instance Running the MariaDB Database Engine](USER_ConnectToMariaDBInstance.md)\.

## Connecting from the MySQL Client<a name="USER_ConnectToInstance.CLI"></a>

To connect to a DB instance using the MySQL client, type the following command at a command prompt to connect to a DB instance using the MySQL client\. For the \-h parameter, substitute the DNS name \(endpoint\) for your DB instance\. For the \-P parameter, substitute the port for your DB instance\. For the \-u parameter, substitute the username of a valid database user, such as the master user\. Enter the master user password when prompted\. 

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

1. Download a root certificate that works for all AWS Regions\.

   For information about downloading certificates, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

1. Enter the following command at a command prompt to connect to a DB instance with SSL using the MySQL client\. For the \-h parameter, substitute the DNS name \(endpoint\) for your DB instance\. For the \-\-ssl\-ca parameter, substitute the SSL certificate file name as appropriate\. For the \-P parameter, substitute the port for your DB instance\. For the \-u parameter, substitute the username of a valid database user, such as the master user\. Enter the master user password when prompted\.

   ```
   mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=rds-ca-2015-root.pem -P 3306 -u mymasteruser -p
   ```

1. You can require that the SSL connection verifies the DB instance endpoint against the endpoint in the SSL certificate\. 

   For MySQL 5\.7 and later:

   ```
   mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=rds-ca-2015-root.pem --ssl-mode=VERIFY_IDENTITY -P 3306 -u mymasteruser -p
   ```

   For MySQL 5\.6 and earlier:

   ```
   mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=rds-ca-2015-root.pem --ssl-verify-server-cert -P 3306 -u mymasteruser -p
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

   You can use the features of MySQL Workbench to customize connections\. For example, you can use the **SSL** tab to configure SSL connections\. For information about using MySQL Workbench, see the [MySQL Workbench documentation](https://dev.mysql.com/doc/workbench/en/)\.

1. Optionally, choose **Test Connection** to confirm that the connection to the DB instance is successful\.

1. Choose **Close**\.

1. From **Database**, choose **Connect to Database**\.

1. From **Stored Connection**, choose your connection\.

1. Choose **OK**\.

## Troubleshooting Connections to Your MySQL DB Instance<a name="USER_ConnectToInstance.Troubleshooting"></a>

Two common causes of connection failures to a new DB instance are:
+ The DB instance was created using a security group that doesn't authorize connections from the device or Amazon EC2 instance where the MySQL application or utility is running\. If the DB instance was created in a VPC, it must have a VPC security group that authorizes the connections\. For more information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.

  You can add or edit an inbound rule in the security group\. For **Source**, choose **My IP**\. This allows access to the DB instance from the IP address detected in your browser\.

  If the DB instance was created outside of a VPC, it must have a DB security group that authorizes the connections\.
+ The DB instance was created using the default port of 3306, and your company has firewall rules blocking connections to that port from devices in your company network\. To fix this failure, recreate the instance with a different port\.

For more information on connection issues, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.