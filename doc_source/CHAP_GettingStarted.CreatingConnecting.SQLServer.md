# Creating a Microsoft SQL Server DB Instance and Connecting to a DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.SQLServer"></a>

The basic building block of Amazon RDS is the DB instance\. Your Amazon RDS DB instance is similar to your on\-premises Microsoft SQL Server\. After you create your SQL Server DB instance, you can add one or more custom databases to it\. 

**Important**  
You must have an AWS account before you can create a DB instance\. If you don't have an AWS account, open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\. 

In this topic you create a sample SQL Server DB instance\. You then connect to the DB instance and run a simple query\. Finally you delete the sample DB instance\. 

## Creating a Sample SQL Server DB Instance<a name="CHAP_GettingStarted.Creating.SQLServer"></a>

In this procedure you use the AWS Management Console to create a sample DB instance\. Since you are only creating a sample DB instance, each setting is not fully explained\. For a full explanation of each setting, see [Creating a DB Instance Running the Microsoft SQL Server Database Engine](USER_CreateMicrosoftSQLServerInstance.md)\. 

**To create a DB instance running the Microsoft SQL Server DB engine**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the Amazon RDS console, choose the region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Instances**\. 

1. Choose **Launch DB Instance**\. 

   The **Select Engine** page appears\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch01.png)

1. Choose the SQL Server icon, and then choose **Select** for the **SQL Server Express** edition\. 

   The **Specify DB Details** page appears\.   
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch02.png)

1. On the **Specify DB Details** page, provide the information for your DB instance as shown in the following table:   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html)

1. Choose **Next** to continue\. 

   The **Configure Advanced Settings** page appears\.   
![\[Additional Configuration panel\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch03.png)

1. On the **Configure Advanced Settings** page, provide the information for your DB instance as shown in the following table:   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html)

1. Choose **Launch DB Instance**\. 

1. Choose **View Your DB Instances**\. 

   On the RDS console, the new DB instance appears in the list of DB instances\. The DB instance has a status of **creating** until the DB instance is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch05.png)

## Connecting to Your Sample SQL Server DB Instance<a name="CHAP_GettingStarted.Connecting.SQLServer"></a>

In this procedure you connect to your sample DB instance by using Microsoft SQL Server Management Studio \(SSMS\)\. To download a stand\-alone version of this utility, see [Download SQL Server Management Studio \(SSMS\)](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms) in the Microsoft documentation\. 

**To connect to a DB Instance using SSMS**

1. Find the DNS name and port number for your DB Instance\. 

   1. Open the RDS console and then choose **Instances** to display a list of your DB instances\. 

   1. Choose the row for your SQL Server DB instance to display the summary information for the instance\.   
![\[Locate DB Instance endpoint and port\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Endpoint.png)

   1. Copy the endpoint\. The **Endpoint** field has two parts separated by a colon \(:\)\. The part before the colon is the DNS name for the instance, the part following the colon is the port number\. Copy both parts\. 

1. Start SQL Server Management Studio\. 

   The **Connect to Server** dialog box appears\.   
![\[Connect to Server dialog\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/RDSMSFTSQLConnect01.png)

1. Provide the information for your sample DB instance\. 

   1. For **Server type**, choose **Database Engine**\. 

   1. For **Server name**, type or paste the DNS name and port number of your sample DB Instance, separated by a comma\. 
**Important**  
Change the colon between the DNS name and port number to a comma\. 

      For example, your server name should look like the following: 

      ```
      sample-instance.cg034hpkmmjt.us-east-1.rds.amazonaws.com,1433
      ```

   1. For **Authentication**, choose **SQL Server Authentication**\. 

   1. For **Login**, type the master user name you chose earlier for your sample DB instance\. 

   1. For **Password**, type the password you chose earlier for your sample DB instance\. 

1. Choose **Connect**\. 

   After a few moments, SSMS connects to your DB instance\. If you can't connect to your DB instance, see [Troubleshooting the Connection to Your SQL Server DB Instance](USER_ConnectToMicrosoftSQLServerInstance.md#USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting)\. 

## Exploring Your Sample SQL Server DB Instance<a name="CHAP_GettingStarted.SQLServer.Exploring"></a>

In this procedure you continue the previous procedure and explore your sample DB instance by using Microsoft SQL Server Management Studio \(SSMS\)\. 

**To explore a DB Instance using SSMS**

1. Your SQL Server DB instance comes with SQL Server's standard built\-in system databases \(master, model, msdb, and tempdb\)\. To explore the system databases, do the following: 

   1. In SSMS, on the **View** menu, choose **Object Explorer**\.

   1. Expand your DB instance, expand **Databases**, and then expand **System Databases** as shown following\.   
![\[Object Explorer displaying the system databases\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-SSMS-SystemDBs.png)

1. Your SQL Server DB instance also comes with a database named `rdsadmin`\. Amazon RDS uses this database to store the objects that it uses to manage your database\. The `rdsadmin` database also includes stored procedures that you can run to perform advanced tasks\.  

1. You can now start creating your own databases and running queries against your DB instance and databases as usual\. To run a test query against your sample DB instance, do the following: 

   1. In SSMS, on the **File** menu point to **New** and then choose **Query with Current Connection**\. 

   1. Type the following SQL query: 

      ```
      select @@VERSION
      ```

   1. Run the query\. SSMS returns the SQL Server version of your Amazon RDS DB instance\.   
![\[SQL Query Window\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-Connect-Query.png)

## Deleting Your Sample DB Instance<a name="CHAP_GettingStarted.Deleting.SQLServer"></a>

Once you are done exploring the sample DB instance that you created, you should delete the DB instance so that you are no longer charged for it\. 

**To delete a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the **Instances** list, choose your sample DB instance\. 

1. Choose **Instance Actions**, and then choose **Delete**\. 

1. For **Create final Snapshot**, choose **No**\. 
**Note**  
You should create a final snapshot for any production DB instance that you delete\. 

1. Choose **Delete**\. 

## Related Topics<a name="CHAP_GettingStarted.SQLServer.Related"></a>

+ [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)

+ [Creating a DB Instance Running the Microsoft SQL Server Database Engine](USER_CreateMicrosoftSQLServerInstance.md)

+ [Connecting to a DB Instance Running the Microsoft SQL Server Database Engine](USER_ConnectToMicrosoftSQLServerInstance.md)

+ [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)

+ [Microsoft SQL Server on Amazon RDS](CHAP_SQLServer.md)