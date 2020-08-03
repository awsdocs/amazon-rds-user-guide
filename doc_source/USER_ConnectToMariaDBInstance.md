# Connecting to a DB Instance Running the MariaDB Database Engine<a name="USER_ConnectToMariaDBInstance"></a>

After Amazon RDS provisions your DB instance, you can use any standard MariaDB client application or utility to connect to the instance\. In the connection string, you specify the Domain Name System \(DNS\) address from the DB instance endpoint as the host parameter\. You also specify the port number from the DB instance endpoint as the port parameter\.

You can use the AWS Management Console, the AWS CLI [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command, or the Amazon RDS API [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) operation to list the details of an Amazon RDS DB instance, including its endpoint\.

**To find the endpoint for a MariaDB DB instance in the AWS Management Console**

1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

1. Choose the MariaDB DB instance name to display its details\. 

1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Connect to a MariaDB DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDBConnect1.png)

If an endpoint value is `mariadb-instance1.123456789012.us-east-1.rds.amazonaws.com:3306`, then you specify the following values in a MariaDB connection string:
+ For host or host name, specify `mariadb-instance1.123456789012.us-east-1.rds.amazonaws.com`\.
+ For port, specify `3306`\.

You can connect to an Amazon RDS MariaDB DB instance by using tools like the mysql command line utility\. For more information on using the mysql utility, see [mysql Command\-line Client](http://mariadb.com/kb/en/mariadb/mysql-command-line-client/) in the MariaDB documentation\. One GUI\-based application you can use to connect is Heidi\. For more information, see the [Download Heidi](http://www.heidisql.com/download.php) page\.

To connect to a DB instance from outside of its Amazon VPC, the DB instance must be publicly accessible, access must be granted using the inbound rules of the DB instance's security group, and other requirements must be met\. For more information, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

You can use SSL encryption on connections to an Amazon RDS MariaDB DB instance\. For information, see [Using SSL with a MariaDB DB Instance](CHAP_MariaDB.md#MariaDB.Concepts.SSLSupport)\.

## Connecting from the mysql Utility<a name="USER_ConnectToMariaDBInstance.CLI"></a>

To connect to a DB instance using the mysql utility, enter the following command at a command prompt on a client computer\. Doing this connects you to a database on a MariaDB DB instance\. Substitute the DNS name \(endpoint\) for your DB instance for *`<endpoint>`* and the master user name that you used for *`<quartermaster>`*\. Provide the master password that you used when prompted for a password\.

```
mysql -h <endpoint> -P 3306 -u <mymasteruser> -p
```

After you enter the password for the user, you see output similar to the following\.

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

1. Download a root certificate that works for all AWS Regions\.

   For information about downloading certificates, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

1. Enter the following command at a command prompt to connect to a DB instance with SSL using the `mysql` utility\. For the `-h` parameter, substitute the DNS name for your DB instance\. For the `--ssl-ca` parameter, substitute the SSL certificate file name as appropriate\.

   ```
   mysql -h mariadb-instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=rds-ca-2015-root.pem -p
   ```

1. Include the `--ssl-verify-server-cert` parameter so that the SSL connection verifies the DB instance endpoint against the endpoint in the SSL certificate\. For example:

   ```
   mysql -h mariadb-instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=rds-ca-2015-root.pem --ssl-verify-server-cert -p
   ```

1. Enter the master user password when prompted\.

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

## Troubleshooting Connections to Your MariaDB DB Instance<a name="USER_ConnectToMariaDBInstance.Troubleshooting"></a>

Two common causes of connection failures to a new DB instance are the following:
+ The DB instance was created using a security group that doesn't authorize connections from the device or Amazon EC2 instance where the MariaDB application or utility is running\. If the DB instance was created in an Amazon VPC, it must have a VPC security group that authorizes the connections\. For more information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.

  You can add or edit an inbound rule in the security group\. For **Source**, choose **My IP**\. This allows access to the DB instance from the IP address detected in your browser\.

  If the DB instance was created outside of a VPC, it must have a DB security group that authorizes the connections\.
+ The DB instance was created using the default port of 3306, and your company has firewall rules blocking connections to that port from devices in your company network\. To fix this failure, recreate the instance with a different port\.

For more information on connection issues, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.