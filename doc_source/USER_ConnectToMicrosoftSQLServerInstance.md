# Connecting to a DB instance running the Microsoft SQL Server database engine<a name="USER_ConnectToMicrosoftSQLServerInstance"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the DB instance\. In this topic, you connect to your DB instance by using either Microsoft SQL Server Management Studio \(SSMS\) or SQL Workbench/J\.

For an example that walks you through the process of creating and connecting to a sample DB instance, see [Creating a Microsoft SQL Server DB instance and connecting to it](CHAP_GettingStarted.CreatingConnecting.SQLServer.md)\. 

## Before you connect<a name="sqlserver-before-connect"></a>

Before you can connect to your DB instance, it has to be available and accessible\.

1. Make sure that its status is `available`\. You can check this on the details page for your instance in the AWS Management Console or by using the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) AWS CLI command\.  
![\[Check that the DB instance is available\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/sqlserver-available.png)

1. Make sure that it is publicly accessible\. You can check this when you check the availability\.

1. Make sure that the inbound rules of your VPC security group allow access to your DB instance\. For more information, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

## Finding the DB instance endpoint and port number<a name="sqlserver-endpoint"></a>

You need both the endpoint and the port number to connect to the DB instance\.

**To find the endpoint and port**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region of your DB instance\.

1. Find the Domain Name System \(DNS\) name \(endpoint\) and port number for your DB instance:

   1. Open the RDS console and choose **Databases** to display a list of your DB instances\.

   1. Choose the SQL Server DB instance name to display its details\.

   1. On the **Connectivity & security** tab, copy the endpoint\.  
![\[Locate DB instance endpoint and port\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Endpoint.png)

   1. Note the port number\.

## Connecting to your DB instance with Microsoft SQL Server Management Studio<a name="USER_ConnectToMicrosoftSQLServerInstance.SSMS"></a>

In this procedure, you connect to your sample DB instance by using Microsoft SQL Server Management Studio \(SSMS\)\. To download a standalone version of this utility, see [Download SQL Server Management Studio \(SSMS\)](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms) in the Microsoft documentation\.

**To connect to a DB instance using SSMS**

1. Start SQL Server Management Studio\.

   The **Connect to Server** dialog box appears\.  
![\[Connect to Server dialog\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/RDSMSFTSQLConnect01.png)

1. Provide the information for your DB instance:

   1. For **Server type**, choose **Database Engine**\. 

   1. For **Server name**, enter the DNS name \(endpoint\) and port number of your DB instance, separated by a comma\. 
**Important**  
Change the colon between the endpoint and port number to a comma\. 

      Your server name should look like the following example\.

      ```
      database-2.cg034itsfake.us-east-1.rds.amazonaws.com,1433
      ```

   1. For **Authentication**, choose **SQL Server Authentication**\. 

   1. For **Login**, enter the master user name for your DB instance\. 

   1. For **Password**, enter the password for your DB instance\. 

1. Choose **Connect**\. 

   After a few moments, SSMS connects to your DB instance\.

   If you can't connect to your DB instance, see [Security group considerations](#USER_ConnectToMicrosoftSQLServerInstance.Security) and [Troubleshooting connections to your SQL Server DB instance](#USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting)\. 

1. Your SQL Server DB instance comes with SQL Server's standard built\-in system databases \(`master`, `model`, `msdb`, and `tempdb`\)\. To explore the system databases, do the following:

   1. In SSMS, on the **View** menu, choose **Object Explorer**\.

   1. Expand your DB instance, expand **Databases**, and then expand **System Databases**\.  
![\[Object Explorer displaying the system databases\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-SSMS-SystemDBs.png)

1. Your SQL Server DB instance also comes with a database named `rdsadmin`\. Amazon RDS uses this database to store the objects that it uses to manage your database\. The `rdsadmin` database also includes stored procedures that you can run to perform advanced tasks\. For more information, see [Common DBA tasks for Microsoft SQL Server](Appendix.SQLServer.CommonDBATasks.md)\.

1. You can now start creating your own databases and running queries against your DB instance and databases as usual\. To run a test query against your DB instance, do the following:

   1. In SSMS, on the **File** menu point to **New** and then choose **Query with Current Connection**\.

   1. Enter the following SQL query\.

      ```
      select @@VERSION
      ```

   1. Run the query\. SSMS returns the SQL Server version of your Amazon RDS DB instance\.  
![\[SQL Query Window\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Query.png)

## Connecting to your DB instance with SQL Workbench/J<a name="USER_ConnectToMicrosoftSQLServerInstance.JDBC"></a>

This example shows how to connect to a DB instance running the Microsoft SQL Server database engine by using the SQL Workbench/J database tool\. To download SQL Workbench/J, see [SQL Workbench/J](http://www.sql-workbench.net/)\. 

SQL Workbench/J uses JDBC to connect to your DB instance\. You also need the JDBC driver for SQL Server\. To download this driver, see [Microsoft JDBC drivers 4\.1 \(preview\) and 4\.0 for SQL Server](http://www.microsoft.com/en-us/download/details.aspx?id=11774)\. 

**To connect to a DB instance using SQL Workbench/J**

1. Open SQL Workbench/J\. The **Select Connection Profile** dialog box appears, as shown following\.  
![\[Select Connection Profile dialog\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/workbench_profile.png)

1. In the first box at the top of the dialog box, enter a name for the profile\. 

1. For **Driver**, choose **SQL JDBC 4\.0**\. 

1. For **URL**, enter **jdbc:sqlserver://**, then enter the endpoint of your DB instance\. For example, the URL value might be the following\.

   ```
   jdbc:sqlserver://sqlsvr-pdz.abcd12340.us-west-2.rds.amazonaws.com:1433
   ```

1. For **Username**, enter the master user name for the DB instance\. 

1. For **Password**, enter the password for the master user\. 

1. Choose the save icon in the dialog toolbar, as shown following\.  
![\[Save the profile\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/save_example.png)

1. Choose **OK**\. After a few moments, SQL Workbench/J connects to your DB instance\. If you can't connect to your DB instance, see [Security group considerations](#USER_ConnectToMicrosoftSQLServerInstance.Security) and [Troubleshooting connections to your SQL Server DB instance](#USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting)\. 

1. In the query pane, enter the following SQL query\.

   ```
   select @@VERSION
   ```

1. Choose the `Execute` icon in the toolbar, as shown following\.  
![\[Run the query\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/execute_example.png)

   The query returns the version information for your DB instance, similar to the following\.

   ```
   Microsoft SQL Server 2017 (RTM-CU22) (KB4577467) - 14.0.3356.20 (X64)
   ```

## Security group considerations<a name="USER_ConnectToMicrosoftSQLServerInstance.Security"></a>

To connect to your DB instance, your DB instance must be associated with a security group\. This security group contains the IP addresses and network configuration that you use to access the DB instance\. You might have associated your DB instance with an appropriate security group when you created your DB instance\. If you assigned a default, no\-configured security group when you created your DB instance, your DB instance firewall prevents connections\.

In some cases, you might need to create a new security group to make access possible\. For instructions on creating a new security group, see [Controlling access with security groups](Overview.RDSSecurityGroups.md)\. For a topic that walks you through the process of setting up rules for your VPC security group, see [Tutorial: Create a VPC for use with a DB instance \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.

After you have created the new security group, modify your DB instance to associate it with the security group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

You can enhance security by using SSL to encrypt connections to your DB instance\. For more information, see [Using SSL with a Microsoft SQL Server DB instance](SQLServer.Concepts.General.SSL.Using.md)\. 

## Troubleshooting connections to your SQL Server DB instance<a name="USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting"></a>

The following table shows error messages that you might encounter when you attempt to connect to your SQL Server DB instance\. For more information on connection issues, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.


****  
<a name="rds-sql-server-connection-troubleshooting-guidance"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToMicrosoftSQLServerInstance.html)