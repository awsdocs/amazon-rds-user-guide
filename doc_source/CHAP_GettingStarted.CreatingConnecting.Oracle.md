# Creating an Oracle DB Instance and Connecting to a Database on an Oracle DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.Oracle"></a>

The basic building block of Amazon RDS is the DB instance\. Your Amazon RDS DB instance is similar to your on\-premises Oracle database\. 

**Important**  
You must have an AWS account before you can create a DB instance\. If you don't have an AWS account, open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\. 

In this topic you create a sample Oracle DB instance\. You then connect to the DB instance and run a simple query\. Finally you delete the sample DB instance\. 

## Creating a Sample Oracle DB Instance<a name="CHAP_GettingStarted.Creating.Oracle"></a>

In this procedure you use the AWS Management Console to create a sample DB instance\. Since you are only creating a sample DB instance, each setting is not fully explained\. For a full explanation of each setting, see [Creating a DB Instance Running the Oracle Database Engine](USER_CreateOracleInstance.md)\. 

**To create a DB instance running the Oracle database engine**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the Amazon RDS console, choose the region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Instances**\. 

1. Choose **Launch DB instance**\. 

   The **Select engine** page appears\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Oracle-Launch01.png)

1. Choose the Oracle icon, and then choose **Select** for the **Oracle Standard Edition Two** edition\. 

1. The **Choose use case** page asks if you are planning to use the DB instance you are creating for production\. Choose **Dev/Test** and then choose **Next**\. 

   The **Specify DB details** page appears\.   
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Oracle-Launch-SE-02.png)

1. On the **Specify DB details** page, provide the information for your DB instance as shown in the following table:   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.Oracle.html)

1. Choose **Next** to continue\. 

   The **Configure Advanced Settings** page appears\. 

1. On the **Configure advanced settings** page, provide the information for your DB instance as shown in the following table:   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.Oracle.html)

1. Choose **Launch DB instance**\. 

1. Choose **View DB instance details**\. 

   On the RDS console, the details for new DB instance appear\. The DB instance has a status of **creating** until the DB instance is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Oracle-Launch-SE-05.png)

## Connecting to Your Sample Oracle DB Instance<a name="CHAP_GettingStarted.Connecting.Oracle"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the instance\. In this procedure you connect to your sample DB instance by using the Oracle *sqlplus* command line utility\. To download a stand\-alone version of this utility, see [SQL\*Plus User's Guide and Reference](http://download.oracle.com/docs/cd/B19306_01/server.102/b14357/ape.htm)\. 

**To connect to a DB Instance using SQL\*Plus**

1. Find the endpoint \(DNS name\) and port number for your DB Instance\. 

   1. Open the RDS console and then choose **Instances** to display a list of your DB instances\. 

   1. Choose the Oracle DB instance and choose **See details** from **Instance actions** to display the details for the DB instance\. 

   1. Scroll to the **Connect** section and copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/OracleConnect1.png)

1. Type the following command on one line at a command prompt to connect to your DB instance by using the sqlplus utility\. The value for `Host` is the endpoint for your DB instance, the value for `Port` is the port you assigned the DB instance, and the value for the Oracle `SID` is the name of the DB instance's database that you specified when you created the DB instance, not the name of the DB instance\. 

   ```
   PROMPT>sqlplus 'mydbusr@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=endpoint) (PORT=1521))(CONNECT_DATA=(SID=ORCL)))'
   ```

   You should see output similar to the following\. 

   ```
   SQL*Plus: Release 11.1.0.7.0 - Production on Wed May 25 15:13:59 2011
       					
   SQL>
   ```

## Deleting Your Sample DB Instance<a name="CHAP_GettingStarted.Deleting.Oracle"></a>

Once you are done exploring the sample DB instance that you created, you should delete the DB instance so that you are no longer charged for it\. 

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the DB instance you want to delete\.

1. For **Instance actions**, choose **Delete**\.

1. For **Create final snapshot?**, choose **No**, and select the acknowledgment\.

1. Choose **Delete**\. 

## Related Topics<a name="CHAP_GettingStarted.Oracle.Related"></a>
+ [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)
+ [Creating a DB Instance Running the Oracle Database Engine](USER_CreateOracleInstance.md)
+ [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)
+ [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)
+ [Oracle on Amazon RDS](CHAP_Oracle.md)