# Connecting to a DB instance running the PostgreSQL database engine<a name="USER_ConnectToPostgreSQLInstance"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the instance\. Before you can connect, the DB instance must be available and accessible\. Whether you can connect to the instance from outside the VPC depends on how you created the Amazon RDS DB instance: 
+ If you created your DB instance as *public*, devices and Amazon EC2 instances outside the VPC can connect to your database\. 
+ If you created your DB instance as *private*, only Amazon EC2 instances and devices inside the Amazon VPC can connect to your database\. 

To check whether your DB instance is public or private, use the AWS Management Console to view the **Connectivity & security** tab for your instance\. Under **Security**, you can find the "Publicly accessible" value, with No for private, Yes for public\. 

To learn more about different Amazon RDS and Amazon VPC configurations and how they affect accessibility, see [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)\. 

If the DB instance is available and accessible, you can connect by providing the following information to the SQL client application: 
+ The DB instance endpoint, which serves as the host name \(DNS name\) for the instance\.
+ The port on which the DB instance is listening\. For PostgreSQL, the default port is 5432\. 
+ The user name and password for the DB instance\. The default 'master username' for PostgreSQL is `postgres`\. 
+ The name and password of the database \(DB name\)\. 

 You can obtain these details by using the AWS Management Console, the AWS CLI [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command, or the Amazon RDS API [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) operation\. 

**To find the endpoint, port number, and DB name using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

1. Choose the PostgreSQL DB instance name to display its details\. 

1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Obtain the endpoint from the RDS Console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/PostgreSQL-endpoint.png)

1. On the **Configuration** tab, note the DB name\. If you created a database when you created the RDS for PostgreSQL instance, you see the name listed under DB name\. If you didn't create a database, the DB name displays a dash \(‐\)\.  
![\[Obtain the DB name from the RDS Console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/PostgreSQL-db-name.png)

Following are two ways to connect to a PostgreSQL DB instance\. The first example uses pgAdmin, a popular open\-source administration and development tool for PostgreSQL\. The second example uses psql, a command line utility that is part of a PostgreSQL installation\. 

**Topics**
+ [Using pgAdmin to connect to a RDS for PostgreSQL DB instance](#USER_ConnectToPostgreSQLInstance.pgAdmin)
+ [Using psql to connect to your RDS for PostgreSQL DB instance](#USER_ConnectToPostgreSQLInstance.psql)
+ [Connecting with the AWS JDBC Driver for PostgreSQL](#USER_ConnectToPostgreSQLInstance.JDBCDriverPostgreSQL)
+ [Troubleshooting connections to your RDS for PostgreSQL instance](#USER_ConnectToPostgreSQLInstance.Troubleshooting)

## Using pgAdmin to connect to a RDS for PostgreSQL DB instance<a name="USER_ConnectToPostgreSQLInstance.pgAdmin"></a>

You can use the open\-source tool pgAdmin to connect to your RDS for PostgreSQL DB instance\. You can download and install pgAdmin from [http://www\.pgadmin\.org/](http://www.pgadmin.org/) without having a local instance of PostgreSQL on your client computer\.

**To connect to your RDS for PostgreSQL DB instance using pgAdmin**

1. Launch the pgAdmin application on your client computer\. 

1. On the **Dashboard** tab, choose **Add New Server**\.

1. In the **Create \- Server** dialog box, type a name on the **General** tab to identify the server in pgAdmin\.

1. On the **Connection** tab, type the following information from your DB instance:
   + For **Host**, type the endpoint, for example `mypostgresql.c6c8dntfzzhgv0.us-east-2.rds.amazonaws.com`\.
   + For **Port**, type the assigned port\. 
   + For **Username**, type the user name that you entered when you created the DB instance \(if you changed the 'master username' from the default, `postgres`\)\. 
   + For **Password**, type the password that you entered when you created the DB instance\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect01.png)

1. Choose **Save**\. 

   If you have any problems connecting, see [Troubleshooting connections to your RDS for PostgreSQL instance](#USER_ConnectToPostgreSQLInstance.Troubleshooting)\. 

1. To access a database in the pgAdmin browser, expand **Servers**, the DB instance, and **Databases**\. Choose the DB instance's database name\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Connect02.png)

1. To open a panel where you can enter SQL commands, choose **Tools**, **Query Tool**\. 

## Using psql to connect to your RDS for PostgreSQL DB instance<a name="USER_ConnectToPostgreSQLInstance.psql"></a>

You can use a local instance of the psql command line utility to connect to a RDS for PostgreSQL DB instance\. You need either PostgreSQL or the psql client installed on your client computer\. 

To connect to your RDS for PostgreSQL DB instance using psql, you need to provide host \(DNS\) information, access credentials, and the name of the database\.

Use one of the following formats to connect to your RDS for PostgreSQL DB instance\. When you connect, you're prompted for a password\. For batch jobs or scripts, use the `--no-password` option\. This option is set for the entire session\.

**Note**  
A connection attempt with `--no-password` fails when the server requires password authentication and a password is not available from other sources\. For more information, see the [psql documentation](https://www.postgresql.org/docs/13/app-psql.html)\.

If this is the first time you are connecting to this DB instance, or if you didn't yet create a database for this RDS for PostgreSQL instance, you can connect to the **postgres** database using the 'master username' and password\.

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

## Connecting with the AWS JDBC Driver for PostgreSQL<a name="USER_ConnectToPostgreSQLInstance.JDBCDriverPostgreSQL"></a>

The AWS JDBC Driver for PostgreSQL is a client wrapper designed for use with RDS for PostgreSQL\. The AWS JDBC Driver for PostgreSQL extends the functionality of the community pgJDBC driver by enabling AWS features such as authentication\. For more information about the AWS JDBC Driver for PostgreSQL and complete instructions for using it, see the [AWS JDBC Driver for PostgreSQL GitHub repository](https://github.com/awslabs/aws-advanced-jdbc-wrapper)\.

The AWS JDBC Driver for PostgreSQL supports AWS Identity and Access Management \(IAM\) database authentication and AWS Secrets Manager\. For more information on using these authentication mechanisms with the driver, see [AWS IAM Authentication Plugin](https://github.com/awslabs/aws-advanced-jdbc-wrapper/blob/main/docs/using-the-jdbc-driver/using-plugins/UsingTheIamAuthenticationPlugin.md) and [AWS Secrets Manager Plugin](https://github.com/awslabs/aws-advanced-jdbc-wrapper/blob/main/docs/using-the-jdbc-driver/using-plugins/UsingTheAwsSecretsManagerPlugin.md) in the AWS JDBC Driver for PostgreSQL GitHub repository\.

For more information about IAM database authentication, see [IAM database authentication for MariaDB, MySQL, and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. For more information about Secrets Manager, see the [AWS Secrets Manager User Guide](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)\.

## Troubleshooting connections to your RDS for PostgreSQL instance<a name="USER_ConnectToPostgreSQLInstance.Troubleshooting"></a>

**Topics**
+ [Error – FATAL: database *name* does not exist](#USER_ConnectToPostgreSQLInstance.Troubleshooting-DBname)
+ [Error – Could not connect to server: Connection timed out](#USER_ConnectToPostgreSQLInstance.Troubleshooting-timeout)
+ [Errors with security group access rules](#USER_ConnectToPostgreSQLInstance.Troubleshooting-AccessRules)

### Error – FATAL: database *name* does not exist<a name="USER_ConnectToPostgreSQLInstance.Troubleshooting-DBname"></a>

If when trying to connect you receive an error like `FATAL: database name does not exist`, try using the default database name **postgres** for the `--dbname` option\. 

### Error – Could not connect to server: Connection timed out<a name="USER_ConnectToPostgreSQLInstance.Troubleshooting-timeout"></a>

If you can't connect to the DB instance, the most common error is `Could not connect to server: Connection timed out.` If you receive this error, check the following:
+ Check that the host name used is the DB instance endpoint and that the port number used is correct\. 
+ Make sure that the DB instance's public accessibility is set to **Yes** to allow external connections\. To modify the **Public access** setting, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.
+ Check that the security group assigned to the DB instance has rules to allow access through any firewall your connection might go through\. For example, if the DB instance was created using the default port of 5432, your company might have firewall rules blocking connections to that port from external company devices\.

  To fix this, modify the DB instance to use a different port\. Also, make sure that the security group applied to the DB instance allows connections to the new port\. To modify the **Database port** setting, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.
+ See also [ Errors with security group access rules](#USER_ConnectToPostgreSQLInstance.Troubleshooting-AccessRules)\.

### Errors with security group access rules<a name="USER_ConnectToPostgreSQLInstance.Troubleshooting-AccessRules"></a>

By far the most common connection problem is with the security group's access rules assigned to the DB instance\. If you used the default security group when you created the DB instance, the security group likely didn't have access rules that allow you to access the instance\. 

For the connection to work, the security group you assigned to the DB instance at its creation must allow access to the DB instance\. For example, if the DB instance was created in a VPC, it must have a VPC security group that authorizes connections\. Check if the DB instance was created using a security group that doesn't authorize connections from the device or Amazon EC2 instance where the application is running\.

You can add or edit an inbound rule in the security group\. For **Source**, choosing **My IP** allows access to the DB instance from the IP address detected in your browser\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

Alternatively, if the DB instance was created outside of a VPC, it must have a database security group that authorizes those connections\.

For more information about Amazon RDS security groups, see [Controlling access with security groups](Overview.RDSSecurityGroups.md)\. 