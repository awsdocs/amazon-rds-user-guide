# Connecting to an Amazon Aurora DB Cluster<a name="Aurora.Connecting"></a>

You can connect to a DB instance in an Amazon Aurora DB cluster using the same tools that you use to connect to a MySQL or PostgreSQL database\. As part of this, you use the same public key for Secure Sockets Layer \(SSL\) connections\. You can use the endpoint and port information from the primary instance or Aurora Replicas in your Aurora DB cluster in the connection string of any script, utility, or application that connects to a MySQL or PostgreSQL DB instance\. In the connection string, specify the DNS address from the primary instance or Aurora Replica endpoint as the host parameter\. Specify the port number from the endpoint as the port parameter\.

## Connecting to an Amazon Aurora MySQL DB Cluster<a name="Aurora.Connecting.AuroraMySQL"></a>

To authenticate to your Aurora MySQL DB cluster, you can use either MySQL user name and password authentication or AWS Identity and Access Management \(IAM\) database authentication\. For more information on using MySQL user name and password authentication, see [User Account Management](https://dev.mysql.com/doc/refman/5.6/en/user-account-management.html) in the MySQL documentation\. For more information on using IAM database authentication, see [IAM Database Authentication for MySQL and Amazon Aurora](UsingWithRDS.IAMDBAuth.md)\.

When you have a connection to your Amazon Aurora DB cluster with MySQL 5\.6 compatibility, you can execute SQL commands that are compatible with MySQL version 5\.6\. For more information about MySQL 5\.6 SQL syntax, see the [MySQL 5\.6 Reference Manual](http://dev.mysql.com/doc/refman/5.6/en/index.html)\.

When you have a connection to your Amazon Aurora DB cluster with MySQL 5\.7 compatibility, you can execute SQL commands that are compatible with MySQL version 5\.7\. For more information about MySQL 5\.7 SQL syntax, see the [MySQL 5\.7 Reference Manual](http://dev.mysql.com/doc/refman/5.7/en/index.html)\. For information about limitations that apply to Aurora MySQL 5\.7, see [Comparison of Aurora MySQL 5\.7 and MySQL 5\.7](Aurora.AuroraMySQL.md#Aurora.AuroraMySQL.CompareMySQL57)\.

**Note**  
For a helpful and detailed guide on connecting to an Amazon Aurora MySQL DB cluster, you can see [RDS Aurora Connectivity](https://s3-us-west-2.amazonaws.com/jsmiley-share/Aurora/RDS+Aurora+Connectivity+Guide+-+v4.pdf)\.

In the details view for your DB cluster, you can find the cluster endpoint, which you can use in your MySQL connection string\. The endpoint is made up of the domain name and port for your DB cluster\. For example, if an endpoint value is `mycluster.cluster-123456789012.us-east-1.rds.amazonaws.com:3306`, then you specify the following values in a MySQL connection string:
+ For host or host name, specify `mycluster.cluster-123456789012.us-east-1.rds.amazonaws.com`
+ For port, specify `3306` or the port value you used when you created the DB cluster

The cluster endpoint connects you to the primary instance for the DB cluster\. You can perform both read and write operations using the cluster endpoint\. Your DB cluster can also have up to 15 Aurora Replicas that support read\-only access to the data in your DB cluster\. The primary instance and each Aurora Replica has a unique endpoint that is independent of the cluster endpoint and allows you to connect to a specific DB instance in the cluster directly\. The cluster endpoint always points to the primary instance\. If the primary instance fails and is replaced, then the cluster endpoint points to the new primary instance\.

To view the cluster endpoint, choose **Clusters** on the Amazon RDS console and choose the DB cluster to show the DB cluster details\.

![\[Amazon Aurora Launch DB Instance Wizard Create Aurora Replica DB Instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraMySQLConnect.png)

### Connection Utilities<a name="Aurora.Connecting.AuroraMySQL.Utilities"></a>

Some connection utilities you can use are the following:
+ **Command line** – You can connect to an Amazon Aurora DB cluster by using tools like the MySQL command line utility\. For more information on using the MySQL utility, see [mysql \- The MySQL Command Line Tool](http://dev.mysql.com/doc/refman/5.6/en/mysql.html) in the MySQL documentation\.
+ **GUI** – You can use the MySQL Workbench utility to connect by using a UI interface\. For more information, see the [Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/) page\.
+ **Applications** – You can use the MariaDB Connector/J utility to connect your applications to your Aurora DB cluster\. For more information, see the [MariaDB Connector/J download](https://downloads.mariadb.org/connector-java/) page\.

You can use SSL encryption on connections to an Amazon Aurora DB instance\. For information, see [Using SSL with a MySQL DB Instance](CHAP_MySQL.md#MySQL.Concepts.SSLSupport)\.

**Note**  
Because you can create Amazon Aurora DB cluster only in an Amazon Virtual Private Cloud \(VPC\), connections to an Amazon Aurora DB cluster from AWS instances that are not in a VPC have been required to use the public endpoint address of the Amazon Aurora DB cluster\. However, you can now communicate with an Amazon EC2 instance that is not in a VPC and an Amazon Aurora DB cluster using ClassicLink\. For more information, see [A DB Instance in a VPC Accessed by an EC2 Instance Not in a VPC](USER_VPC.Scenarios.md#USER_VPC.ClassicLink)\.

### Connecting with SSL<a name="Aurora.Connecting.SSL"></a>

To connect using SSL, use the MySQL utility as described in the following procedure\. If you are using IAM database authentication, you must use an SSL connection\. For information, see [IAM Database Authentication for MySQL and Amazon Aurora](UsingWithRDS.IAMDBAuth.md)\. 

**Note**  
In order to connect to the cluster endpoint using SSL, your client connection utility must support Subject Alternative Names \(SAN\)\. If your client connection utility doesn't support SAN, you can connect directly to the instances in your Aurora DB cluster\. For more information on Aurora endpoints, see [Aurora Endpoints](Aurora.Overview.md#Aurora.Overview.Endpoints)\.

**To connect to a DB cluster with SSL using the MySQL utility**

1. Download the public key for the Amazon RDS signing certificate from [https://s3\.amazonaws\.com/rds\-downloads/rds\-combined\-ca\-bundle\.pem](https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem)\. Doing this downloads a file named `rds-combined-ca-bundle.pem`\.

1. Type the following command at a command prompt to connect to the primary instance of a DB cluster with SSL using the MySQL utility\. For the \-h parameter, substitute the endpoint DNS name for your primary instance\. For the \-\-ssl\_ca parameter, substitute the SSL certificate file name as appropriate\. Type the master user password when prompted\.

   `mysql -h mycluster-primary.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=[full path]rds-combined-ca-bundle.pem --ssl-verify-server-cert`

You should see output similar to the following\.

```
1. Welcome to the MySQL monitor.  Commands end with ; or \g.
2. Your MySQL connection id is 350
3. Server version: 5.6.10-log MySQL Community Server (GPL)
4. 
5. Type 'help;' or '\h' for help. Type '\c' to clear the buffer.
6. 
7. mysql>
```

For general instructions on constructing Amazon RDS MySQL connection strings and finding the public key for SSL connections, see [Connecting to a DB Instance Running the MySQL Database Engine](USER_ConnectToInstance.md)\.

## Connecting to an Amazon Aurora PostgreSQL DB Cluster<a name="Aurora.Connecting.AuroraPostgreSQL"></a>

You can connect to a DB instance in your Amazon Aurora PostgreSQL DB cluster using the same tools that you use to connect to a PostgreSQL database\. As part of this, you use the same public key for Secure Sockets Layer \(SSL\) connections\. You can use the endpoint and port information from the primary instance or Aurora Replicas in your Aurora PostgreSQL DB cluster in the connection string of any script, utility, or application that connects to a PostgreSQL DB instance\. In the connection string, specify the DNS address from the primary instance or Aurora Replica endpoint as the host parameter\. Specify the port number from the endpoint as the port parameter\.

When you have a connection to a DB instance in your Amazon Aurora PostgreSQL DB cluster, you can run any SQL command that is compatible with PostgreSQL version 9\.6\.3\. 

For a helpful and detailed guide on connecting to an Amazon Aurora DB cluster, see [RDS Aurora Connectivity](https://s3-us-west-2.amazonaws.com/jsmiley-share/Aurora/RDS+Aurora+Connectivity+Guide+-+v4.pdf)\.

In the details view for your Aurora PostgreSQL DB cluster you can find the cluster endpoint\. You use this endpoint in your PostgreSQL connection string\. The endpoint is made up of the domain name and port for your DB cluster\. For example, if an endpoint value is `mycluster.cluster-123456789012.us-east-1.rds.amazonaws.com:5432`, then you specify the following values in a PostgreSQL connection string:
+ For host or host name, specify `mycluster.cluster-123456789012.us-east-1.rds.amazonaws.com`
+ For port, specify `5432` or the port value you used when you created the DB cluster

The cluster endpoint connects you to the primary instance for the DB cluster\. You can perform both read and write operations using the cluster endpoint\. Your DB cluster can also have up to 15 Aurora Replicas that support read\-only access to the data in your DB cluster\. The primary instance and each Aurora Replica each has a unique endpoint that is independent of the cluster endpoint\. This unique endpoint allows you to connect to a specific DB instance in the cluster directly\. The cluster endpoint always points to the primary instance\. If the primary instance fails and is replaced, the cluster endpoint points to the new primary instance\.

To view the cluster endpoint, choose **Clusters** on the Amazon RDS console and click the DB cluster to show the DB cluster details\.

![\[Amazon Aurora Launch DB Instance Wizard Create Aurora Replica DB Instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraPostgreSQLConnect.png)

### Connection Utilities<a name="Aurora.Connecting.AuroraPostgreSQL.Utilities"></a>

Some connection utilities you can use are the following:
+ **Command line** – You can connect to an Amazon Aurora PostgreSQL DB instance by using tools like psql, the PostgreSQL interactive terminal\. For more information on using the PostgreSQL interactive terminal, see [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) in the PostgreSQL documentation\.
+ **GUI** – You can use the pgAdmin utility to connect to a PostgreSQL DB instance by using a UI interface\. For more information, see the [Download](https://www.pgadmin.org/download/) page from the pgAdmin website\.
+ **Applications** – You can use the PostgreSQL JDBC driver to connect your applications to your PostgreSQL DB instance\. For more information, see the [Download](https://jdbc.postgresql.org/download.html) page from the PostgreSQL JDBC driver website\.

**Note**  
Because you can create an Amazon Aurora PostgreSQL DB cluster only in an Amazon Virtual Private Cloud \(VPC\), connections to an Aurora PostgreSQL DB cluster from AWS instances that are not in a VPC have been required to use the public endpoint address of the Aurora PostgreSQL DB cluster\. However, you can now communicate with an Amazon EC2 instance that is not in a VPC and an Aurora PostgreSQL DB cluster using ClassicLink\. For more information, see [A DB Instance in a VPC Accessed by an EC2 Instance Not in a VPC](USER_VPC.Scenarios.md#USER_VPC.ClassicLink)\.

## Troubleshooting Aurora Connection Failures<a name="Aurora.Connecting.Troubleshooting"></a>

**Note**  
For a helpful and detailed guide on connecting to an Amazon Aurora DB cluster, you can see [RDS Aurora Connectivity](https://s3-us-west-2.amazonaws.com/jsmiley-share/Aurora/RDS+Aurora+Connectivity+Guide+-+v4.pdf)\.

Common causes of connection failures to a new Aurora DB cluster are as follows:
+ The DB cluster was created using a VPC that does not allow connections from your device\. To fix this failure, modify the VPC to allow connections from your device, or create a new VPC for your DB cluster that allows connections from your device\. For an example, see [Create a VPC and Subnets](Aurora.CreateVPC.md#CHAP_Aurora.CreateVPC)\.
+ The DB cluster was created using the default port, and your company has firewall rules blocking connections to that port from devices in your company network\. To fix this failure, recreate the instance with a different port\.
+ If you are using IAM database authentication, you might need to configure IAM database authentication\. For information, see [IAM Database Authentication for MySQL and Amazon Aurora](UsingWithRDS.IAMDBAuth.md)\.

## Related Topics<a name="Aurora.Connecting.RelatedTopics"></a>
+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)