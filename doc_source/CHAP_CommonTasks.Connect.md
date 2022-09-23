# Connecting to an Amazon RDS DB instance<a name="CHAP_CommonTasks.Connect"></a>

 Before you can connect to a DB instance, you must create the DB instance\. For information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. After Amazon RDS provisions your DB instance, use any standard client application or utility for your DB engine to connect to the DB instance\. In the connection string, specify the DNS address from the DB instance endpoint as the host parameter\. Also, specify the port number from the DB instance endpoint as the port parameter\. 

**Topics**
+ [Finding the connection information for an Amazon RDS DB instance](#CHAP_CommonTasks.Connect.EndpointAndPort)
+ [Database authentication options](#CHAP_CommonTasks.Connect.DatabaseAuthentication)
+ [Encrypted connections](#CHAP_CommonTasks.Connect.EncryptedConnections)
+ [Scenarios for accessing a DB instance in a VPC](#CHAP_CommonTasks.Connect.ScenariosForAccess)
+ [Connecting to a DB instance that is running a specific DB engine](#CHAP_CommonTasks.Connect.DBEngine)
+ [Managing connections with RDS Proxy](#CHAP_CommonTasks.Connect.RDSProxy)

## Finding the connection information for an Amazon RDS DB instance<a name="CHAP_CommonTasks.Connect.EndpointAndPort"></a>

The connection information for a DB instance includes its endpoint, port, and a valid database user, such as the master user\. For example, for a MySQL DB instance, suppose that the endpoint value is `mydb.123456789012.us-east-1.rds.amazonaws.com`\. In this case, the port value is `3306`, and the database user is `admin`\. Given this information, you specify the following values in a connection string:
+ For host or host name or DNS name, specify `mydb.123456789012.us-east-1.rds.amazonaws.com`\.
+ For port, specify `3306`\.
+ For user, specify `admin`\.

The endpoint is unique for each DB instance, and the values of the port and user can vary\. The following list shows the most common port for each DB engine:
+ MariaDB – 3306
+ Microsoft SQL Server – 1433
+ MySQL – 3306
+ Oracle – 1521
+ PostgreSQL – 5432

To connect to a DB instance, use any client for a DB engine\. For example, you might use the mysql utility to connect to a MariaDB or MySQL DB instance\. You might use Microsoft SQL Server Management Studio to connect to a SQL Server DB instance\. You might use Oracle SQL Developer to connect to an Oracle DB instance\. Similarly, you might use the psql command line utility to connect to a PostgreSQL DB instance\.

To find the connection information for a DB instance, use the AWS Management Console\. You can also use the AWS Command Line Interface \(AWS CLI\) [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command or the RDS API [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) operation\. 

### Console<a name="CHAP_CommonTasks.Connect.EndpointAndPort.Console"></a>

**To find the connection information for a DB instance in the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases** to display a list of your DB instances\.

1. Choose the name of the DB instance to display its details\.

1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[The endpoint and port of a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/endpoint-port.png)

1. If you need to find the master user name, choose the **Configuration** tab and view the **Master username** value\.

### AWS CLI<a name="CHAP_CommonTasks.Connect.EndpointAndPort.CLI"></a>

To find the connection information for a DB instance by using the AWS CLI, call the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command\. In the call, query for the DB instance ID, endpoint, port, and master user name\.

For Linux, macOS, or Unix:

```
aws rds describe-db-instances \
  --query "*[].[DBInstanceIdentifier,Endpoint.Address,Endpoint.Port,MasterUsername]"
```

For Windows:

```
aws rds describe-db-instances ^
  --query "*[].[DBInstanceIdentifier,Endpoint.Address,Endpoint.Port,MasterUsername]"
```

Your output should be similar to the following\.

```
[
    [
        "mydb",
        "mydb.123456789012.us-east-1.rds.amazonaws.com",
        3306,
        "admin"
    ],
    [
        "myoracledb",
        "myoracledb.123456789012.us-east-1.rds.amazonaws.com",
        1521,
        "dbadmin"
    ],
    [
        "mypostgresqldb",
        "mypostgresqldb.123456789012.us-east-1.rds.amazonaws.com",
        5432,
        "postgresadmin"
    ]
]
```

### RDS API<a name="CHAP_CommonTasks.Connect.EndpointAndPort.API"></a>

To find the connection information for a DB instance by using the Amazon RDS API, call the [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) operation\. In the output, find the values for the endpoint address, endpoint port, and master user name\. 

## Database authentication options<a name="CHAP_CommonTasks.Connect.DatabaseAuthentication"></a>

Amazon RDS supports the following ways to authenticate database users:
+ **Password authentication** – Your DB instance performs all administration of user accounts\. You create users and specify passwords with SQL statements\. The SQL statements you can use depend on your DB engine\.
+ **AWS Identity and Access Management \(IAM\) database authentication** – You don't need to use a password when you connect to a DB instance\. Instead, you use an authentication token\.
+ **Kerberos authentication** – You use external authentication of database users using Kerberos and Microsoft Active Directory\. Kerberos is a network authentication protocol that uses tickets and symmetric\-key cryptography to eliminate the need to transmit passwords over the network\. Kerberos has been built into Active Directory and is designed to authenticate users to network resources, such as databases\.

IAM database authentication and Kerberos authentication are available only for specific DB engines and versions\.

For more information, see [Database authentication with Amazon RDS](database-authentication.md)\.

## Encrypted connections<a name="CHAP_CommonTasks.Connect.EncryptedConnections"></a>

You can use Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\) from your application to encrypt a connection to a DB instance\. Each DB engine has its own process for implementing SSL/TLS\. For more information, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

## Scenarios for accessing a DB instance in a VPC<a name="CHAP_CommonTasks.Connect.ScenariosForAccess"></a>

Using Amazon Virtual Private Cloud \(Amazon VPC\), you can launch AWS resources, such as Amazon RDS DB instances, into a virtual private cloud \(VPC\)\. When you use Amazon VPC, you have control over your virtual networking environment\. You can choose your own IP address range, create subnets, and configure routing and access control lists\.

A VPC security group controls access to DB instances inside a VPC\. Each VPC security group rule enables a specific source to access a DB instance in a VPC that is associated with that VPC security group\. The source can be a range of addresses \(for example, 203\.0\.113\.0/24\), or another VPC security group\. By specifying a VPC security group as the source, you allow incoming traffic from all instances \(typically application servers\) that use the source VPC security group\.

Before attempting to connect to your DB instance, configure your VPC for your use case\. The following are common scenarios for accessing a DB instance in a VPC: 
+ **A DB instance in a VPC accessed by an Amazon EC2 instance in the same VPC** – A common use of a DB instance in a VPC is to share data with an application server that is running in an EC2 instance in the same VPC\. The EC2 instance might run a web server with an application that interacts with the DB instance\.
+ **A DB instance in a VPC accessed by an EC2 instance in a different VPC** – In some cases, your DB instance is in a different VPC from the EC2 instance that you're using to access it\. If so, you can use VPC peering to access the DB instance\. 
+ **A DB instance in a VPC accessed by a client application through the internet** – To access a DB instance in a VPC from a client application through the internet, you configure a VPC with a single public subnet\. You also configure an internet gateway to enable communication over the internet\. 

  To connect to a DB instance from outside of its VPC, the DB instance must be publicly accessible\. Also, access must be granted using the inbound rules of the DB instance's security group, and other requirements must be met\. For more information, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.
+ **A DB instance in a VPC accessed by a private network** – If your DB instance isn't publicly accessible, you can use an AWS Site\-to\-Site VPN connection or an AWS Direct Connect connection to access it from a private network\.

For more information, see [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)\.

## Connecting to a DB instance that is running a specific DB engine<a name="CHAP_CommonTasks.Connect.DBEngine"></a>

For information about connecting to a DB instance that is running a specific DB engine, follow the instructions for your DB engine:
+ [Connecting to a DB instance running the MariaDB database engine](USER_ConnectToMariaDBInstance.md)
+ [Connecting to a DB instance running the Microsoft SQL Server database engine](USER_ConnectToMicrosoftSQLServerInstance.md)
+ [Connecting to a DB instance running the MySQL database engine](USER_ConnectToInstance.md)
+ [Connecting to your Oracle DB instance](USER_ConnectToOracleInstance.md)
+ [Connecting to a DB instance running the PostgreSQL database engine](USER_ConnectToPostgreSQLInstance.md)

## Managing connections with RDS Proxy<a name="CHAP_CommonTasks.Connect.RDSProxy"></a>

You can also use Amazon RDS Proxy to manage connections to RDS for MariaDB, RDS for Microsoft SQL Server, RDS for MySQL, and RDS for PostgreSQL DB instances\. RDS Proxy allows applications to pool and share database connections to improve scalability\. For more information, see [Using Amazon RDS Proxy](rds-proxy.md)\.