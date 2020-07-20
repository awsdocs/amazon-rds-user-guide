# Connecting to a DB Instance Running the Microsoft SQL Server Database Engine<a name="USER_ConnectToMicrosoftSQLServerInstance"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the DB instance\. In this topic, you connect to your DB instance by using either Microsoft SQL Server Management Studio \(SSMS\) or SQL Workbench/J\. 

For an example that walks you through the process of creating and connecting to a sample DB instance, see [Creating a Microsoft SQL Server DB Instance and Connecting to a DB Instance](CHAP_GettingStarted.CreatingConnecting.SQLServer.md)\. 

## Connecting to Your DB Instance with Microsoft SQL Server Management Studio<a name="USER_ConnectToMicrosoftSQLServerInstance.SSMS"></a>

In this procedure, you connect to your sample DB instance by using Microsoft SQL Server Management Studio \(SSMS\)\. To download a standalone version of this utility, see [Download SQL Server Management Studio \(SSMS\)](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms) in the Microsoft documentation\.

**To connect to a DB instance using SSMS**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region of your DB instance\. 

1. Find the Domain Name System \(DNS\) name and port number for your DB instance:

   1. Open the RDS console and choose **Databases** to display a list of your DB instances\.

   1. Choose the SQL Server DB instance name to display its details\.

   1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.  
![\[Locate DB instance endpoint and port\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Endpoint.png)

1. Start SQL Server Management Studio\. 

   The **Connect to Server** dialog box appears\.  
![\[Connect to Server dialog\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/RDSMSFTSQLConnect01.png)

1. Provide the information for your DB instance:

   1. For **Server type**, choose **Database Engine**\. 

   1. For **Server name**, enter the DNS name and port number of your DB instance, separated by a comma\. 
**Important**  
Change the colon between the DNS name and port number to a comma\. 

      For example, your server name should look like the following\.

      ```
      database-2.cg034itsfake.us-east-1.rds.amazonaws.com,1433
      ```

   1. For **Authentication**, choose **SQL Server Authentication**\. 

   1. For **Login**, enter the master user name for your DB instance\. 

   1. For **Password**, enter the password for your DB instance\. 

1. Choose **Connect**\. 

   After a few moments, SSMS connects to your DB instance\. If you can't connect to your DB instance, see [Security Group Considerations](#USER_ConnectToMicrosoftSQLServerInstance.Security) and [Troubleshooting Connections to Your SQL Server DB Instance](#USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting)\. 

1. Your SQL Server DB instance comes with SQL Server's standard built\-in system databases \(`master`, `model`, `msdb`, and `tempdb`\)\. To explore the system databases, do the following:

   1. In SSMS, on the **View** menu, choose **Object Explorer**\.

   1. Expand your DB instance, expand **Databases**, and then expand **System Databases**\.  
![\[Object Explorer displaying the system databases\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-SSMS-SystemDBs.png)

1. Your SQL Server DB instance also comes with a database named `rdsadmin`\. Amazon RDS uses this database to store the objects that it uses to manage your database\. The `rdsadmin` database also includes stored procedures that you can run to perform advanced tasks\. For more information, see [Common DBA Tasks for Microsoft SQL Server](Appendix.SQLServer.CommonDBATasks.md)\.

1. You can now start creating your own databases and running queries against your DB instance and databases as usual\. To run a test query against your DB instance, do the following:

   1. In SSMS, on the **File** menu point to **New** and then choose **Query with Current Connection**\.

   1. Enter the following SQL query\.

      ```
      select @@VERSION
      ```

   1. Run the query\. SSMS returns the SQL Server version of your Amazon RDS DB instance\.  
![\[SQL Query Window\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Query.png)

## Connecting to Your DB Instance with SQL Workbench/J<a name="USER_ConnectToMicrosoftSQLServerInstance.JDBC"></a>

This example shows how to connect to a DB instance running the Microsoft SQL Server database engine by using the SQL Workbench/J database tool\. To download SQL Workbench/J, see [SQL Workbench/J](http://www.sql-workbench.net/)\. 

SQL Workbench/J uses JDBC to connect to your DB instance\. You also need the JDBC driver for SQL Server\. To download this driver, see [Microsoft JDBC Drivers 4\.1 \(Preview\) and 4\.0 for SQL Server](http://www.microsoft.com/en-us/download/details.aspx?id=11774)\. 

**To connect to a DB instance using SQL Workbench**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region of your DB instance\. 

1. Find the DNS name and port number for your DB instance: 

   1. Open the RDS console, then choose **Databases** to display a list of your DB instances\. 

   1. Choose the name of your SQL Server DB instance to display its details\.   
![\[Locate DB instance endpoint and port\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Endpoint.png)

   1. On the **Connectivity** tab, copy the endpoint\. Also, note the port used by the DB instance\. 

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

1. Choose **OK**\. After a few moments, SQL Workbench/J connects to your DB instance\. If you can't connect to your DB instance, see [Security Group Considerations](#USER_ConnectToMicrosoftSQLServerInstance.Security) and [Troubleshooting Connections to Your SQL Server DB Instance](#USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting)\. 

1. In the query pane, enter the following SQL query\.

   ```
   select @@VERSION
   ```

1. Choose the `Execute` icon in the toolbar, as shown following\.  
![\[Run the query\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/execute_example.png)

   The query returns the version information for your DB instance, similar to the following\.

   ```
   Microsoft SQL Server 2012 - 11.0.2100.60 (X64)
   ```

## Security Group Considerations<a name="USER_ConnectToMicrosoftSQLServerInstance.Security"></a>

To connect to your DB instance, your DB instance must be associated with a security group\. This security group contains the IP addresses and network configuration that you use to access the DB instance\. You might have associated your DB instance with an appropriate security group when you created your DB instance\. If you assigned a default, no\-configured security group when you created your DB instance, your DB instance firewall prevents connections\.

In some cases, you might need to create a new security group to enable access\. If so, the type of security group to create depends on what Amazon EC2 platform your DB instance is on\. To determine your platform, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\. In general, if your DB instance is on the EC2\-Classic platform, you create a DB security group\. If your DB instance is on the VPC platform, you create a VPC security group\.

For instructions on creating a new security group, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\. For a topic that walks you through the process of setting up rules for your VPC security group, see [Tutorial: Create an Amazon VPC for Use with a DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.

After you have created the new security group, modify your DB instance to associate it with the security group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

You can enhance security by using SSL to encrypt connections to your DB instance\. For more information, see [Using SSL with a Microsoft SQL Server DB Instance](SQLServer.Concepts.General.SSL.Using.md)\. 

## Troubleshooting Connections to Your SQL Server DB Instance<a name="USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting"></a>

The following table shows error messages that you might encounter when you attempt to connect to your SQL Server DB instance\. For more information on connection issues, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.


****  
<a name="rds-sql-server-connection-troubleshooting-guidance"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToMicrosoftSQLServerInstance.html)