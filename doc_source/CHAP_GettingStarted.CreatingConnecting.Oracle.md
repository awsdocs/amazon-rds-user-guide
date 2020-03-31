# Creating an Oracle DB Instance and Connecting to a Database on an Oracle DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.Oracle"></a>

The basic building block of Amazon RDS is the DB instance\. Your Amazon RDS DB instance is similar to your on\-premises Oracle database\. 

**Important**  
You must have an AWS account before you can create a DB instance\. If you don't have an AWS account, open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\. 

In this topic, you create a sample Oracle DB instance\. You then connect to the DB instance and run a simple query\. Finally, you delete the sample DB instance\. 

## Creating a Sample Oracle DB Instance<a name="CHAP_GettingStarted.Creating.Oracle"></a>

The DB instance is where you run your Oracle databases\.

### Console<a name="CHAP_GettingStarted.Creating.Oracle.Console"></a>

You can create a DB instance running Oracle with the AWS Management Console with **Easy Create** enabled or not enabled\. With **Easy Create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy Create** uses the default setting for other configuration options\. With **Easy Create** not enabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

For this example, you use **Easy Create** to create a DB instance running the Oracle database engine with a db\.t2\.micro DB instance class\.

**Note**  
For information about creating DB instances with **Easy Create** not enabled, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

**To create an Oracle DB instance with Easy Create enabled**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and ensure that **Easy Create** is chosen\.  
![\[Easy Create option\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-option.png)

1. In **Configuration**, choose **Oracle**\.

1. For **DB instance size**, choose **Free tier**\. If **Free tier** isn't available, choose **Dev/Test**\.

1. For **DB instance identifier**, enter a name for the DB instance, or leave the default name\.

1. For **Master username**, enter a name for the master user, or leave the default name\.

   The **Create database** page should look similar to the following image\.  
![\[Create database page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-oracle.png)

1. To use an automatically generated master password for the DB instance, make sure that the **Auto generate a password** check box is chosen\.

   To enter your master password, clear the **Auto generate a password** check box, and then enter the same password in **Master password** and **Confirm password**\.

1. \(Optional\) Open **View default settings for Easy create**\.  
![\[Easy Create default settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-view-default-settings.png)

   You can examine the default settings that are used when **Easy Create** is enabled\. If you want to change one or more settings during database creation, choose **Standard Create** to set them\. The **Editable after database creation** column shows which options you can change after database creation\. To change a setting with **No** in that column, use **Standard Create**\. For settings with **Yes** in that column, you can either use **Standard Create** or modify the DB instance after it's created to change the setting\.

1. Choose **Create database**\.

   If you used an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB instance, choose **View credential details**\.  
![\[Master user credentials after automatic password generation.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-credentials.png)

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
>You can't view the master user password again\. If you don't record it, you might have to change it\. If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. For **Databases**, choose the name of the new Oracle DB instance\.

   On the RDS console, the details for new DB instance appear\. The DB instance has a status of **creating** until the DB instance is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[Screenshot of the DB instance details.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Oracle-Launch05.png)

## Connecting to Your Sample Oracle DB Instance<a name="CHAP_GettingStarted.Connecting.Oracle"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the DB instance\. In this procedure, you connect to your sample DB instance by using the Oracle sqlplus command line utility\. To download a stand\-alone version of this utility, see [SQL\*Plus User's Guide and Reference](http://download.oracle.com/docs/cd/B19306_01/server.102/b14357/ape.htm)\. 

**To connect to a DB Instance using SQL\*Plus**

1. Find the endpoint \(DNS name\) and port number for your DB Instance\. 

   1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

   1. Choose the Oracle DB instance name to display its details\. 

   1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/OracleConnect1.png)

1. Enter the following command on one line at a command prompt to connect to your DB instance by using the sqlplus utility\. The value for `Host` is the endpoint for your DB instance, and the value for `Port` is the port you assigned the DB instance\. The value for the Oracle `SID` is the name of the DB instance's database that you specified when you created the DB instance, not the name of the DB instance\. 

   ```
   PROMPT>sqlplus 'mydbusr@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=endpoint)(PORT=1521))(CONNECT_DATA=(SID=ORCL)))'
   ```

   You should see output similar to the following\. 

   ```
   SQL*Plus: Release 11.1.0.7.0 - Production on Wed May 25 15:13:59 2011
       					
   SQL>
   ```

For more information about connecting to an Oracle DB instance, see [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)\. For information on connection issues, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

## Deleting Your Sample DB Instance<a name="CHAP_GettingStarted.Deleting.Oracle"></a>

After you are done exploring the sample DB instance that you created, you should delete the DB instance so that you are no longer charged for it\. 

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. For **Create final snapshot?**, choose **No**, and choose the acknowledgment\.

1. Choose **Delete**\. 