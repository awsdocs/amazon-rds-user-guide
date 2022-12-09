# Connecting to a DB instance running the MariaDB database engine<a name="USER_ConnectToMariaDBInstance"></a>

After Amazon RDS provisions your DB instance, you can use any standard MariaDB client application or utility to connect to the instance\. In the connection string, you specify the Domain Name System \(DNS\) address from the DB instance endpoint as the host parameter\. You also specify the port number from the DB instance endpoint as the port parameter\.

You can connect to an Amazon RDS for MariaDB DB instance by using tools like the MySQL command\-line client\. For more information on using the MySQL command\-line client, see [mysql command\-line client](http://mariadb.com/kb/en/mariadb/mysql-command-line-client/) in the MariaDB documentation\. One GUI\-based application that you can use to connect is Heidi\. For more information, see the [Download HeidiSQL](http://www.heidisql.com/download.php) page\. For information about installing MySQL \(including the MySQL command\-line client\), see [Installing and upgrading MySQL](https://dev.mysql.com/doc/refman/8.0/en/installing.html)\. 

Most Linux distributions include the MariaDB client instead of the Oracle MySQL client\. To install the MySQL command\-line client on most RPM\-based Linux distributions, including Amazon Linux 2, run the following command\.

```
yum install mariadb
```

To install the MySQL command\-line client on most DEB\-based Linux distributions, run the following command\.

```
apt-get install mariadb-client
```

To check the version of your MySQL command\-line client, run the following command\.

```
mysql --version
```

To read the MySQL documentation for your current client version, run the following command\.

```
man mysql
```

To connect to a DB instance from outside of a virtual private cloud \(VPC\) based on Amazon VPC, the DB instance must be publicly accessible\. Also, access must be granted using the inbound rules of the DB instance's security group, and other requirements must be met\. For more information, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

You can use SSL encryption on connections to a MariaDB DB instance\. For information, see [Using SSL/TLS with a MariaDB DB instance](mariadb-ssl-connections.md#MariaDB.Concepts.SSLSupport)\.

**Topics**
+ [Finding the connection information for a MariaDB DB instance](#USER_ConnectToMariaDBInstance.EndpointAndPort)
+ [Connecting from the MySQL command\-line client \(unencrypted\)](#USER_ConnectToMariaDBInstance.CLI)
+ [Troubleshooting connections to your MariaDB DB instance](#USER_ConnectToMariaDBInstance.Troubleshooting)

## Finding the connection information for a MariaDB DB instance<a name="USER_ConnectToMariaDBInstance.EndpointAndPort"></a>

The connection information for a DB instance includes its endpoint, port, and a valid database user, such as the master user\. For example, suppose that an endpoint value is `mydb.123456789012.us-east-1.rds.amazonaws.com`\. In this case, the port value is `3306`, and the database user is `admin`\. Given this information, you specify the following values in a connection string:
+ For host or host name or DNS name, specify `mydb.123456789012.us-east-1.rds.amazonaws.com`\.
+ For port, specify `3306`\.
+ For user, specify `admin`\.

To connect to a DB instance, use any client for the MariaDB DB engine\. For example, you might use the MySQL command\-line client or MySQL Workbench\.

To find the connection information for a DB instance, you can use the AWS Management Console, the AWS Command Line Interface \(AWS CLI\) [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command, or the Amazon RDS API [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) operation to list its details\. 

### Console<a name="USER_ConnectToMariaDBInstance.EndpointAndPort.Console"></a>

**To find the connection information for a DB instance in the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases** to display a list of your DB instances\.

1. Choose the name of the MariaDB DB instance to display its details\.

1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[The endpoint and port of a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/endpoint-port.png)

1. If you need to find the master user name, choose the **Configuration** tab and view the **Master username** value\.

### AWS CLI<a name="USER_ConnectToMariaDBInstance.EndpointAndPort.CLI"></a>

To find the connection information for a MariaDB DB instance by using the AWS CLI, call the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command\. In the call, query for the DB instance ID, endpoint, port, and master user name\.

For Linux, macOS, or Unix:

```
aws rds describe-db-instances \
  --filters "Name=engine,Values=mariadb" \
  --query "*[].[DBInstanceIdentifier,Endpoint.Address,Endpoint.Port,MasterUsername]"
```

For Windows:

```
aws rds describe-db-instances ^
  --filters "Name=engine,Values=mariadb" ^
  --query "*[].[DBInstanceIdentifier,Endpoint.Address,Endpoint.Port,MasterUsername]"
```

Your output should be similar to the following\.

```
[
    [
        "mydb1",
        "mydb1.123456789012.us-east-1.rds.amazonaws.com",
        3306,
        "admin"
    ],
    [
        "mydb2",
        "mydb2.123456789012.us-east-1.rds.amazonaws.com",
        3306,
        "admin"
    ]
]
```

### RDS API<a name="USER_ConnectToMariaDBInstance.EndpointAndPort.API"></a>

To find the connection information for a DB instance by using the Amazon RDS API, call the [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) operation\. In the output, find the values for the endpoint address, endpoint port, and master user name\. 

## Connecting from the MySQL command\-line client \(unencrypted\)<a name="USER_ConnectToMariaDBInstance.CLI"></a>

**Important**  
Only use an unencrypted MySQL connection when the client and server are in the same VPC and the network is trusted\. For information about using encrypted connections, see [Connecting from the MySQL command\-line client with SSL/TLS \(encrypted\)](mariadb-ssl-connections.md#USER_ConnectToMariaDBInstanceSSL.CLI)\.

To connect to a DB instance using the MySQL command\-line client, enter the following command at a command prompt on a client computer\. Doing this connects you to a database on a MariaDB DB instance\. Substitute the DNS name \(endpoint\) for your DB instance for *`<endpoint>`* and the master user name that you used for *`<mymasteruser>`*\. Provide the master password that you used when prompted for a password\.

```
mysql -h <endpoint> -P 3306 -u <mymasteruser> -p
```

After you enter the password for the user, you see output similar to the following\.

```
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 31
Server version: 10.5.15-MariaDB-log Source distribution
 
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
  
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  
MariaDB [(none)]>
```

## Troubleshooting connections to your MariaDB DB instance<a name="USER_ConnectToMariaDBInstance.Troubleshooting"></a>

Two common causes of connection failures to a new DB instance are the following:
+ The DB instance was created using a security group that doesn't authorize connections from the device or Amazon EC2 instance where the MariaDB application or utility is running\. The DB instance must have a VPC security group that authorizes the connections\. For more information, see [Amazon VPC VPCs and Amazon RDS](USER_VPC.md)\.

  You can add or edit an inbound rule in the security group\. For **Source**, choose **My IP**\. This allows access to the DB instance from the IP address detected in your browser\.
+ The DB instance was created using the default port of 3306, and your company has firewall rules blocking connections to that port from devices in your company network\. To fix this failure, recreate the instance with a different port\.

For more information on connection issues, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.