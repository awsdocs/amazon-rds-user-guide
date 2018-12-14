# Connecting to a DB Instance Running the Microsoft SQL Server Database Engine<a name="USER_ConnectToMicrosoftSQLServerInstance"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the DB instance\. In this topic you connect to your DB instance by using either Microsoft SQL Server Management Studio \(SSMS\) or SQL Workbench/J\. 

For an example that walks you through the process of creating and connecting to a sample DB instance, see [Creating a Microsoft SQL Server DB Instance and Connecting to a DB Instance](CHAP_GettingStarted.CreatingConnecting.SQLServer.md)\. 

## Connecting to Your DB Instance with Microsoft SQL Server Management Studio<a name="USER_ConnectToMicrosoftSQLServerInstance.SSMS"></a>

In this procedure you connect to your sample DB instance by using Microsoft SQL Server Management Studio \(SSMS\)\. To download a stand\-alone version of this utility, see [Download SQL Server Management Studio \(SSMS\)](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms) in the Microsoft documentation\. 

**To connect to a DB Instance using SSMS**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the Amazon RDS console, select the region of your DB instance\. 

1. Find the DNS name and port number for your DB Instance\. 

   1. Open the RDS console and then choose **Instances** to display a list of your DB instances\. 

   1. Choose the row for your SQL Server DB instance to display the summary information for the instance\.   
![\[Locate DB Instance endpoint and port\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Endpoint.png)

   1. Copy the endpoint\. The **Endpoint** field has two parts separated by a colon \(:\)\. The part before the colon is the DNS name for the instance, the part following the colon is the port number\. Copy both parts\. 

1. Start SQL Server Management Studio\. 

   The **Connect to Server** dialog box appears\.   
![\[Connect to Server dialog\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/RDSMSFTSQLConnect01.png)

1. Provide the information for your DB instance\. 

   1. For **Server type**, choose **Database Engine**\. 

   1. For **Server name**, type or paste the DNS name and port number of your DB Instance, separated by a comma\. 
**Important**  
Change the colon between the DNS name and port number to a comma\. 

      For example, your server name should look like the following: 

      ```
      sample-instance.cg034hpkmmjt.us-east-1.rds.amazonaws.com,1433
      ```

   1. For **Authentication**, choose **SQL Server Authentication**\. 

   1. For **Login**, type the master user name for your DB instance\. 

   1. For **Password**, type the password for your DB instance\. 

1. Choose **Connect**\. 

   After a few moments, SSMS connects to your DB instance\. If you can't connect to your DB instance, see [Security Group Considerations](#USER_ConnectToMicrosoftSQLServerInstance.Security) and [Troubleshooting the Connection to Your SQL Server DB Instance](#USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting)\. 

1. Your SQL Server DB instance comes with SQL Server's standard built\-in system databases \(master, model, msdb, and tempdb\)\. To explore the system databases, do the following: 

   1. In SSMS, on the **View** menu, choose **Object Explorer**\.

   1. Expand your DB instance, expand **Databases**, and then expand **System Databases** as shown following\.   
![\[Object Explorer displaying the system databases\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-SSMS-SystemDBs.png)

1. Your SQL Server DB instance also comes with a database named `rdsadmin`\. Amazon RDS uses this database to store the objects that it uses to manage your database\. The `rdsadmin` database also includes stored procedures that you can run to perform advanced tasks\. For more information, see [Common DBA Tasks for Microsoft SQL Server](Appendix.SQLServer.CommonDBATasks.md)\. 

1. You can now start creating your own databases and running queries against your DB instance and databases as usual\. To run a test query against your DB instance, do the following: 

   1. In SSMS, on the **File** menu point to **New** and then choose **Query with Current Connection**\. 

   1. Type the following SQL query: 

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

1. In the top right corner of the Amazon RDS console, select the region of your DB instance\. 

1. Find the DNS name and port number for your DB Instance\. 

   1. Open the RDS console and then choose **Instances** to display a list of your DB instances\. 

   1. Choose the row for your SQL Server DB instance to display the summary information for the instance\.   
![\[Locate DB Instance endpoint and port\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Endpoint.png)

   1. Copy the endpoint\. The **Endpoint** field has two parts separated by a colon \(:\)\. The part before the colon is the DNS name for the instance, the part following the colon is the port number\. Copy both parts\. 

1. Open SQL Workbench/J\. The **Select Connection Profile** dialog box appears, as shown following:   
![\[Select Connection Profile dialog\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/workbench_profile.png)

1. In the first box at the top of the dialog box, enter a name for the profile\. 

1. For **Driver**, select **SQL JDBC 4\.0**\. 

1. For **URL**, type **jdbc:sqlserver://**, then type or paste the endpoint of your DB instance\. For example, the URL value could be the following: 

   ```
   jdbc:sqlserver://sqlsvr-pdz.abcd12340.us-west-2.rds.amazonaws.com:1433
   ```

1. For **Username**, type or paste the master user name for the DB instance\. 

1. For **Password**, type the password for the master user\. 

1. Choose the save icon in the dialog toolbar, as shown following:   
![\[Save the profile\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/save_example.png)

1. Choose **OK**\. After a few moments, SQL Workbench/J connects to your DB instance\. If you can't connect to your DB instance, see [Security Group Considerations](#USER_ConnectToMicrosoftSQLServerInstance.Security) and [Troubleshooting the Connection to Your SQL Server DB Instance](#USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting)\. 

1. In the query pane, type the following SQL query: 

   ```
   select @@VERSION
   ```

1. Choose the execute icon in the toolbar, as shown following:   
![\[Execute the query\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/execute_example.png)

   The query returns the version information for your DB instance, similar to the following: 

   ```
   Microsoft SQL Server 2012 - 11.0.2100.60 (X64)
   ```

## Security Group Considerations<a name="USER_ConnectToMicrosoftSQLServerInstance.Security"></a>

To connect to your DB instance, your DB instance must be associated with a security group that contains the IP addresses and network configuration that you use to access the DB instance\. You may have associated your DB instance with an appropriate security group when you created your DB instance\. If you assigned a default, non\-configured security group when you created your DB instance, your DB instance firewall prevents connections\. 

If you need to create a new security group to enable access, the type of security group that you create will depend on what Amazon EC2 platform your DB instance is on\. To determine your platform, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\. In general, if your DB instance is on the *EC2\-Classic* platform, you create a DB security group; if your DB instance is on the *VPC* platform, you create a VPC security group\. For instructions on creating a new security group, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\. 

After you have created the new security group, you modify your DB instance to associate it with the security group\. For more information, see [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)\. 

You can enhance security by using SSL to encrypt connections to your DB instance\. For more information, see [Using SSL with a Microsoft SQL Server DB Instance](SQLServer.Concepts.General.SSL.Using.md)\. 

## Troubleshooting the Connection to Your SQL Server DB Instance<a name="USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting"></a>

The following are issues you might encounter when you attempt to connect to your SQL Server DB instance\. 


****  

| Issue | Troubleshooting Suggestions | 
| --- | --- | 
|  Unable to connect to your DB instance\.   |  For a newly\-created DB instance, the DB instance has a status of **creating** until the DB instance is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   | 
|  Unable to connect to your DB instance\.   |  If you can't send or receive communications over the port that you specified when you created the DB instance, you can't connect to the DB instance\. Check with your network administrator to verify that the port you specified for your DB instance allows inbound and outbound communication\.   | 
|  Unable to connect to your DB instance\.   |  The access rules enforced by your local firewall and the IP addresses you authorized to access your DB instance in the security group for the DB instance might not not match\. The problem is most likely the egress or ingress rules on your firewall\. For more information about security groups, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\.  For a topic that walks you through the process of setting up rules for your security group, see [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.   | 
|  **Could not open a connection to SQL Server – Microsoft SQL Server, Error: 53**   |  Make sure specified the server name correctly\. For **Server name**, type or paste the DNS name and port number of your sample DB Instance, separated by a comma\.   Change the colon between the DNS name and port number to a comma\.   For example, your server name should look like the following: <pre>sample-instance.cg034hpkmmjt.us-east-1.rds.amazonaws.com,1433</pre>  | 
|  **No connection could be made because the target machine actively refused it – Microsoft SQL Server, Error: 10061**   |  You were able to reach the DB instance but the connection was refused\. This is usually caused by specifying the user name or password incorrectly\. Verify the user name and password and then retry\.   | 

## Related Topics<a name="USER_ConnectToMicrosoftSQLServerInstance.related"></a>
+ [Creating a DB Instance Running the Microsoft SQL Server Database Engine](USER_CreateMicrosoftSQLServerInstance.md)
+ [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)
+ [Deleting a DB Instance](USER_DeleteInstance.md)